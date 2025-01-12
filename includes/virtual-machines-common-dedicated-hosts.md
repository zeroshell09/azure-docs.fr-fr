---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a627592bdfcbebc3c7fcda911e31c0ae6f4a630f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976614"
---
## <a name="benefits"></a>Avantages 

La réservation de l’intégralité de l’hôte offre les avantages suivants:

-   Isolation matérielle au niveau du serveur physique. Aucune autre machine virtuelle ne sera placée sur vos hôtes. Les hôtes dédiés sont déployés dans les mêmes centres de données et partagent le même réseau et la même infrastructure de stockage sous-jacente que les autres hôtes non isolés.
-   Contrôle des événements de maintenance initiés par la plateforme Azure. Alors que la majorité des événements de maintenance n’ont que peu d’impact sur vos machines virtuelles, il existe des charges de travail sensibles où chaque seconde de pause peut avoir un impact. Avec les hôtes dédiés, vous pouvez vous abonner à une fenêtre de maintenance pour réduire l’impact sur votre service.
-   Avec l’offre Azure Hybrid Benefit, vous pouvez apporter vos propres licences pour Windows et SQL à Azure. L’utilisation des avantages Hybrid Benefit vous offre des avantages supplémentaires. Pour plus d’informations, consultez [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).



## <a name="groups-hosts-and-vms"></a>Groupes, hôtes et machines virtuelles  

![Vue des nouvelles ressources pour les hôtes dédiés.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Un **groupe hôte** est une ressource qui représente une collection d’hôtes dédiés. Vous créez un groupe hôte dans une région et une zone de disponibilité, et lui ajoutez des hôtes.

Un **hôte** est une ressource, mappée à un serveur physique dans un centre de données Azure. Le serveur physique est alloué lors de la création de l’hôte. Un hôte est créé dans un groupe hôte. Un hôte dispose d’une référence SKU décrivant les tailles de machine virtuelle qui peuvent être créées. Chaque hôte peut héberger plusieurs machines virtuelles de différentes tailles, à condition qu’elles proviennent de la même série de tailles.

Lorsque vous créez une machine virtuelle dans Azure, vous pouvez sélectionner l’hôte dédié à utiliser pour votre machine virtuelle. Vous disposez d’un contrôle total sur les machines virtuelles placées sur vos hôtes.


## <a name="high-availability-considerations"></a>Considérations relatives à la haute disponibilité 

Pour une haute disponibilité, vous devez déployer plusieurs machines virtuelles, réparties sur plusieurs hôtes (au minimum 2). Avec les hôtes dédiés Azure, vous disposez de plusieurs options pour configurer votre infrastructure afin de mettre en forme vos limites d’isolation des erreurs.

### <a name="use-availability-zones-for-fault-isolation"></a>Utiliser des zones de disponibilité pour l’isolation des erreurs

Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Un groupe hôte est créé dans une seule zone de disponibilité. Une fois créés, tous les hôtes sont placés dans cette zone. Pour obtenir une haute disponibilité entre les zones, vous devez créer plusieurs groupes hôtes (un par zone) et répartir vos hôtes en conséquence.

Si vous affectez un groupe hôte à une zone de disponibilité, toutes les machines virtuelles créées sur cet hôte doivent être créées dans la même zone.

### <a name="use-fault-domains-for-fault-isolation"></a>Utiliser des domaines d’erreur pour l’isolation des erreurs

Un hôte peut être créé dans un domaine d’erreur spécifique. À l’instar d’une machine virtuelle dans un groupe identique ou un groupe à haute disponibilité, les hôtes situés dans différents domaines d’erreur sont placés sur des racks physiques différents dans le centre de données. Lorsque vous créez un groupe hôte, vous devez spécifier le nombre de domaines d’erreur. Lorsque vous créez des hôtes dans le groupe hôte, vous affectez un domaine d’erreur pour chaque hôte. Les machines virtuelles ne nécessitent aucune attribution de domaine d’erreur.

Domaines d’erreur et colocation ne sont pas les mêmes choses. Le fait d’avoir le même domaine d’erreur pour deux hôtes ne signifie pas qu’ils sont à proximité les uns des autres.

Les domaines d’erreur sont étendus au groupe hôte. Vous ne devez pas faire d’hypothèses sur l’anti-affinité entre deux groupes hôtes (sauf s’ils se trouvent dans des zones de disponibilité différentes).

Les machines virtuelles déployées sur des hôtes avec des domaines d’erreur différents auront leurs services sous-jacents sur plusieurs tampons de stockage, afin d’augmenter la protection contre l’isolement des erreurs.

### <a name="using-availability-zones-and-fault-domains"></a>Utilisation de zones de disponibilité et de domaines d’erreur

Vous pouvez utiliser ces deux fonctionnalités ensemble pour obtenir une isolation des erreurs encore plus étendue. Dans ce cas, vous allez spécifier la zone de disponibilité et le nombre de domaines d’erreur pour chaque groupe hôte, attribuer un domaine d’erreur à chacun de vos hôtes dans le groupe et affecter une zone de disponibilité à chacune de vos machines virtuelles.

L’exemple de modèle Resource Manager, trouvé [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour diffuser des hôtes et obtenir une résilience maximale dans une région.

## <a name="maintenance-control"></a>Contrôle de la maintenance

L’infrastructure qui prend en charge vos machines virtuelles peut parfois être mise à jour pour améliorer la fiabilité, les performances, la sécurité et pour lancer de nouvelles fonctionnalités. La plateforme Azure tente de réduire l’impact de la maintenance de la plateforme chaque fois que cela est possible, mais les clients avec des charges de travail *sensibles à la maintenance* ne peuvent tolérer ( même pendant quelques secondes) que la machine virtuelle doive être gelée ou déconnectée pour la maintenance.

**Le contrôle de maintenance** offre aux clients la possibilité d’ignorer les mises à jour régulières de la plateforme planifiées sur leurs hôtes dédiés, puis de les appliquer au moment de leur choix dans une fenêtre de 35 jours.

> [!NOTE]
>  Le contrôle de maintenance est actuellement en phase de préversion limitée et nécessite un processus d’intégration. Demandez à essayer cette préversion en soumettant une [demande de nomination](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u).

## <a name="capacity-considerations"></a>Considérations relatives à la capacité

Une fois qu’un hôte dédié est approvisionné, Azure l’attribue au serveur physique. Cela garantit la disponibilité de la capacité lorsque vous avez besoin d’approvisionner votre machine virtuelle. Azure utilise la totalité de la capacité dans la région (ou zone) pour choisir un serveur physique pour votre hôte. Cela signifie également que les clients peuvent s’attendre à pouvoir augmenter l’encombrement de leur hôte dédié sans avoir à manquer d’espace dans le cluster.

## <a name="quotas"></a>Quotas

Il existe une limite de quota par défaut de 3 000 processeurs virtuels pour les hôtes dédiés, par région. Toutefois, le nombre d’hôtes que vous pouvez déployer est également limité par le quota de la série de tailles de machine virtuelle utilisée pour l’hôte. Par exemple, un abonnement avec **paiement à l’utilisation** peut uniquement avoir un quota de 10 processeurs virtuels disponibles pour la série de tailles Dsv3, dans la région USA Est. Dans ce cas, vous devez demander une augmentation du quota à au moins 64 processeurs virtuels avant de pouvoir déployer un hôte dédié. Sélectionnez le bouton **Demander une augmentation** dans le coin supérieur droit pour faire une demande.

![Capture d’écran de la page d’utilisation et des quotas dans le portail](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Pour plus d’informations, consultez [Quotas de processeurs virtuels pour les machines virtuelles](/azure/virtual-machines/windows/quotas).

## <a name="pricing"></a>Tarifs

Les utilisateurs sont facturés par hôte dédié, quel que soit le nombre de machines virtuelles déployées. Dans votre relevé mensuel, vous verrez un nouveau type de ressource facturable d’hôtes. Les machines virtuelles sur un hôte dédié seront toujours affichées dans votre instruction, mais le prix sera de 0.

Le prix de l’hôte est défini en fonction de la série de machines virtuelles, du type (taille matérielle) et de la région. Un prix d’hôte est relatif à la taille de machine virtuelle la plus grande prise en charge sur l’hôte.

Les licences logicielles, le stockage et l’utilisation du réseau sont facturés séparément de l’hôte et des machines virtuelles. Il n’y aucune modification pour ces éléments facturables.

Pour plus d’informations, consultez la page [Tarification pour hôte dédié Azure](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>Séries de machines virtuelles et générations de matériel

Une référence SKU est définie pour un hôte et représente la série et le type de la taille de machine virtuelle. Vous pouvez combiner plusieurs machines virtuelles de différentes tailles au sein d’un même hôte, à condition qu’elles soient de la même série de tailles. Le type est la génération matérielle actuellement disponible dans la région.

Des `types` différents entre les mêmes séries de machines virtuelles proviennent de différents fournisseurs de processeurs et ont des générations d’UC et un nombre de cœurs différents.

Pour en savoir plus, consultez la [page de tarification de l’hôte](https://aka.ms/ADHPricing).

Pendant la préversion, nous allons prendre en charge les types / références SKU d’hôte suivants :  DSv3_Type1 et ESv3_Type1

 
## <a name="host-life-cycle"></a>Cycle de vie de l’hôte


Azure surveille et gère l’état d’intégrité de vos hôtes. Les états suivants sont retournés lorsque vous interrogez votre hôte :

| État d’intégrité   | Description       |
|----------|----------------|
| Hôte disponible     | Il n’y a aucun problème connu avec votre hôte.   |
| Examen en cours de l’hôte  | Nous enquêtons sur des problèmes que nous rencontrons avec l’hôte. Il s’agit d’un état transitoire requis pour qu’Azure tente d’identifier l’étendue et la cause racine du problème identifié. Les machines virtuelles en cours d’exécution sur l’hôte peuvent être affectées. |
| Hôte en attente de désallocation   | Azure ne peut pas restaurer l’hôte dans un état sain et vous demande de redéployer vos machines virtuelles hors de cet hôte. Si `autoHealingOnFailure` est activé, vos machines virtuelles *sont réparées* sur du matériel sain. Dans le cas contraire, votre machine virtuelle est peut-être en cours d’exécution sur un hôte qui va échouer.|
| Hôte désalloué  | Toutes les machines virtuelles ont été supprimées de l’hôte. Vous n’êtes plus facturé pour cet hôte, car le matériel a été retiré de la rotation.   |

