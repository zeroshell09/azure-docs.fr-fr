---
title: Verrouiller les ressources Azure pour empêcher les modifications | Microsoft Docs
description: Empêchez les utilisateurs de mettre à jour ou de supprimer des ressources Azure critiques en appliquant un verrou à tous les utilisateurs et rôles.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: 31d77b4ea6e7594cd3ed4dba264f9ea6db4ca290
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155210"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Verrouiller les ressources pour empêcher les modifications inattendues 

En tant qu’administrateur, vous pouvez avoir besoin de verrouiller un abonnement, une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier de manière accidentelle des ressources critiques. Vous pouvez définir le niveau de verrouillage sur **CanNotDelete** ou **ReadOnly**. Dans le portail, les verrous sont appelés **Supprimer** et **Lecture seule** respectivement.

* **CanNotDelete** signifie que les utilisateurs autorisés peuvent lire et modifier une ressource, mais pas la supprimer. 
* **ReadOnly** signifie que les utilisateurs autorisés peuvent lire une ressource, mais pas la supprimer ni la mettre à jour. Appliquer ce verrou revient à limiter à tous les utilisateurs autorisés les autorisations accordées par le rôle **Lecteur**. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Application des verrous

Lorsque vous appliquez un verrou à une étendue parente, toutes les ressources de cette étendue héritent du même verrou. Même les ressources que vous ajoutez par la suite héritent du verrou du parent. Le verrou le plus restrictif de l’héritage est prioritaire.

Contrairement au contrôle d'accès basé sur les rôles, vous utilisez des verrous de gestion pour appliquer une restriction à tous les utilisateurs et rôles. Pour en savoir plus sur la définition des autorisations pour les utilisateurs et les rôles, consultez [Contrôle d’accès en fonction du rôle Azure](../role-based-access-control/role-assignments-portal.md).

Les verrous Resource Manager s'appliquent uniquement aux opérations qui se produisent dans le plan de gestion, c'est-à-dire les opérations envoyées à `https://management.azure.com`. Les verrous ne limitent pas la manière dont les ressources exécutent leurs propres fonctions. Les modifications des ressources sont limitées, mais pas les opérations sur les ressources. Par exemple, un verrou ReadOnly une base de données SQL empêche la suppression ou la modification de la base de données. Il ne vous empêche pas de créer, de mettre à jour ou de supprimer des données dans la base de données. Les transactions de données sont autorisées, car ces opérations ne sont pas envoyées à `https://management.azure.com`.

Si vous appliquez le paramètre **ReadOnly**, il se peut que vous obteniez des résultats inattendus, car certaines opérations qui, en apparence, ne modifient pas la ressource nécessitent en réalité des actions qui sont bloquées par ce verrou. Le verrou **ReadOnly** peut être appliqué à la ressource ou au groupe de ressources qui la contient. Voici quelques exemples courants d’opérations bloquées par un verrou **ReadOnly** :

* Un verrou **ReadOnly** appliqué à un compte de stockage empêche tous les utilisateurs de répertorier les clés. L’opération de listage de clés est gérée via une demande POST, car les clés retournées sont disponibles pour les opérations d’écriture.

* Un verrou **ReadOnly** sur une ressource App Service empêche l’Explorateur de serveurs Visual Studio d’afficher les fichiers de la ressource, car cette interaction requiert un accès en écriture.

* Un verrou **ReadOnly** appliqué à un groupe de ressources contenant une machine virtuelle empêche tous les utilisateurs de démarrer ou de redémarrer cette dernière. Ces opérations nécessitent une demande POST.

## <a name="who-can-create-or-delete-locks"></a>Utilisateurs autorisés à créer ou à supprimer des verrous
Pour créer ou supprimer des verrous de gestion, vous devez avoir accès à des actions `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions.

## <a name="managed-applications-and-locks"></a>Applications et verrous managés

Certains services Azure, tels qu’Azure Databricks, utilisent des [applications managées](../managed-applications/overview.md) pour implémenter le service. Dans ce cas, le service crée deux groupes de ressources. Un groupe de ressources contient une vue d’ensemble du service et n’est pas verrouillé. L’autre groupe contient l’infrastructure du service et est verrouillé.

Si vous essayez de supprimer le groupe de ressources contenant l’infrastructure, vous obtenez une erreur, qui indique que le groupe de ressources est verrouillé. Si vous tentez de supprimer ce verrou, vous obtenez une erreur, qui indique que le verrou ne peut pas être supprimé, car il appartient à une application système.

Au lieu de cela, supprimez le service, ce qui supprime également le groupe de ressources contenant l’infrastructure.

Pour les applications managées, sélectionnez le service que vous avez déployé.

![Sélectionner un service](./media/resource-group-lock-resources/select-service.png)

Notez que le service inclut un lien vers un **groupe de ressources managé**. Ce groupe de ressources contient l’infrastructure et est verrouillé. Il ne peut pas être supprimé directement.

![Afficher un groupe managé](./media/resource-group-lock-resources/show-managed-group.png)

Pour supprimer tous les éléments associés au service, y compris le groupe de ressources contenant l’infrastructure (qui est donc verrouillé), sélectionnez l’option **Supprimer** relative au service.

![Suppression du service](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portail
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modèle

Lorsque vous utilisez un modèle Resource Manager pour déployer un verrou, vous utilisez des valeurs différentes pour le nom et le type, en fonction de la portée du verrou.

Pour appliquer un verrou à une **ressource**, utilisez les formats suivants :

* nom : `{resourceName}/Microsoft.Authorization/{lockName}`
* type : `{resourceProviderNamespace}/{resourceType}/providers/locks`

Pour appliquer un verrou à un **groupe de ressources** ou à un **abonnement**, utilisez les formats suivants :

* nom : `{lockName}`
* type : `Microsoft.Authorization/locks`

L’exemple suivant représente un modèle créant un verrou sur un compte de stockage.plan App Service et un verrou sur le site web. Le type de ressource du verrou est le type de ressource de la ressource à verrouiller et **/providers/locks**. Le nom du verrou résulte de la concaténation du nom de la ressource avec **/Microsoft.Authorization/** et le nom du verrou.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Pour obtenir un exemple de définition d’un verrou pour un groupe de ressources, voir [Créer un groupe de ressources et de le verrouiller](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment) (Créer un groupe de ressources et le verrouiller).

## <a name="powershell"></a>PowerShell
Vous pouvez verrouiller des ressources déployées avec Azure PowerShell en utilisant la commande [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Pour verrouiller une ressource, indiquez le nom de la ressource, son type de ressource et son nom de groupe de ressources.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Pour verrouiller un groupe de ressources : indiquez le nom du groupe de ressources.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Pour obtenir des informations sur un verrou, utilisez [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Pour obtenir tous les verrous de votre abonnement, utilisez :

```azurepowershell-interactive
Get-AzResourceLock
```

Pour obtenir tous les verrous d’une ressource, utilisez :

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Pour obtenir tous les verrous d’un groupe de ressources, utilisez :

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Pour supprimer un verrou, utilisez :

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Verrouillez les ressources déployées avec Azure CLI à l’aide de la commande [az lock create](/cli/azure/lock#az-lock-create).

Pour verrouiller une ressource, indiquez le nom de la ressource, son type de ressource et son nom de groupe de ressources.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Pour verrouiller un groupe de ressources : indiquez le nom du groupe de ressources.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Pour obtenir des informations sur un verrou, utilisez la commande [az lock list](/cli/azure/lock#az-lock-list). Pour obtenir tous les verrous de votre abonnement, utilisez :

```azurecli
az lock list
```

Pour obtenir tous les verrous d’une ressource, utilisez :

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Pour obtenir tous les verrous d’un groupe de ressources, utilisez :

```azurecli
az lock list --resource-group exampleresourcegroup
```

Pour supprimer un verrou, utilisez :

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST
Vous pouvez verrouiller des ressources déployées à l’aide de l’ [API REST pour les verrous de gestion](https://docs.microsoft.com/rest/api/resources/managementlocks). L’API REST vous permet de créer et de supprimer des verrous, et de récupérer des informations relatives aux verrous existants.

Pour créer un verrou, exécutez :

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Le verrou peut être appliqué à un abonnement, à un groupe de ressources ou à une ressource. Le nom du verrou est personnalisable. Pour la version de l’API, utilisez **2016-09-01**.

Dans la demande, incluez un objet JSON spécifiant les propriétés du verrou.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur l’organisation logique de vos ressources, consultez [Organisation des ressources à l’aide de balises](resource-group-using-tags.md)
* Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Policy ?](../governance/policy/overview.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption-guide/subscription-governance).

