---
title: Créer, modifier ou supprimer une homologation de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment créer, modifier ou supprimer une homologation de réseau virtuel.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: c77608aa7a4d1410277dfbe259fb1f55ea8324ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449382"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Créer, modifier ou supprimer une homologation de réseau virtuel

Découvrez comment créer, modifier ou supprimer une homologation de réseau virtuel. Le Peering de réseaux virtuels vous permet de connecter des réseaux virtuels situés dans la même région ou dans des régions différentes (on parle alors également de Global VNet Peering) à l’aide du réseau principal Azure. Une fois appairés, les réseaux virtuels continuent d’être gérés comme des ressources distinctes. Si vous découvrez l’appairage de réseaux virtuels, vous trouverez plus d’informations à ce sujet dans l’article [Vue d’ensemble de l’appairage de réseaux virtuels](virtual-network-peering-overview.md) ou en suivant un [tutoriel](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com et connectez-vous avec un compte qui possède les [autorisations nécessaires](#permissions) pour utiliser les homologations.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 1.0.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell localement, vous devez également exécuter `Connect-AzAccount` avec un compte qui possède les [autorisations nécessaires](#permissions) pour utiliser les homologations, afin de vous connecter à Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel requiert Azure CLI version 2.0.31 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` avec un compte qui possède les [autorisations nécessaires](#permissions) pour utiliser les homologations, afin de vous connecter à Azure.

Le compte auquel vous vous connectez, ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans [Autorisations](#permissions).

## <a name="create-a-peering"></a>Créer une homologation

Avant de créer une homologation, familiarisez-vous avec les exigences et contraintes ainsi qu’avec les [autorisations nécessaires](#permissions).

1. Dans la zone de recherche située en haut du Portail Azure, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel pour lequel vous souhaitez créer une homologation.
3. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
4. Sélectionnez **Ajouter**. 
5. <a name="add-peering"></a> :
    - **Nom :** Le nom du peering doit être unique dans le réseau virtuel.
    - **Modèle de déploiement de réseau virtuel :** Sélectionnez le modèle de déploiement utilisé pour déployer le réseau virtuel avec lequel vous souhaitez effectuer le peering.
    - **Je connais mon ID de ressource :** Si vous avez accès en lecture au réseau virtuel avec lequel vous souhaitez effectuer le peering, laissez cette case décochée. Si vous n’avez pas accès en lecture au réseau virtuel ou à l’abonnement avec lequel vous souhaitez opérer l’homologation, activez cette case à cocher. Dans la zone **ID de ressource** qui s’affiche lorsque vous cochez la case, entrez l’ID de ressource complet du réseau virtuel avec lequel vous souhaitez opérer l’homologation. L’ID de ressource que vous entrez doit être celui d’un réseau virtuel existant dans la même [région](https://azure.microsoft.com/regions) Azure que ce réseau virtuel ou dans une [autre région Azure prise en charge](#requirements-and-constraints). L’ID complet de la ressource ressemble à `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`. Vous pouvez obtenir l’ID de ressource d’un réseau virtuel en affichant les propriétés de celui-ci. Pour savoir comment afficher les propriétés d’un réseau virtuel, consultez [Gérer des réseaux virtuels](manage-virtual-network.md#view-virtual-networks-and-settings). Si le locataire Azure Active Directory de l’abonnement est différent de celui de l’abonnement qui a le réseau virtuel à partir duquel vous créez le peering, ajoutez d’abord un utilisateur de chaque locataire comme [utilisateur invité](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) dans le locataire opposé.
    - **Abonnement :** Sélectionnez l’[abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) du réseau virtuel avec lequel vous souhaitez effectuer le peering. Un ou plusieurs abonnements sont répertoriés, selon le nombre d’abonnements auxquels votre compte a accès en lecture. Si vous avez activé la case à cocher **ID de ressource**, ce paramètre n’est pas disponible.
    - **Réseau virtuel :** Sélectionnez le réseau virtuel avec lequel vous souhaitez effectuer le peering. Vous pouvez sélectionner un réseau virtuel créé à l’aide d’un modèle de déploiement d’Azure. Si vous souhaitez sélectionner un réseau virtuel dans une autre région, vous devez le choisir dans une [région prise en charge](#cross-region). Pour que le réseau virtuel apparaisse dans la liste, vous devez disposer de l’accès en lecture à celui-ci. Si un réseau virtuel est répertorié, mais grisé, ce peut être parce que l’espace d’adressage du réseau virtuel chevauche l’espace d’adressage de ce réseau. Si les espaces d’adressage de réseaux virtuels se chevauchent, il n’est pas possible d’homologuer ces réseaux. Si vous avez activé la case à cocher **ID de ressource**, ce paramètre n’est pas disponible.
    - **Autoriser l’accès au réseau virtuel** : Sélectionnez **Activé** (par défaut) pour activer la communication entre les deux réseaux virtuels. L’activation de la communication entre les réseaux virtuels permet aux ressources connectées à ceux-ci de communiquer entre elles avec les mêmes bande passante et latence que si elles étaient connectées au même réseau virtuel. Toute communication entre les ressources des deux réseaux virtuels passe par le réseau privé Azure. La balise de service par défaut **VirtualNetwork** pour les groupes de sécurité réseau englobe le réseau virtuel et le réseau virtuel homologué. Pour en savoir plus sur les balises de service de groupes de sécurité réseau, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau](security-overview.md#service-tags). Si vous ne souhaitez pas que le trafic soit dirigé vers le réseau virtuel homologué, sélectionnez **Désactivé**. Vous pouvez sélectionner **Désactivé** si vous avez homologué un réseau virtuel avec un autre réseau virtuel, mais souhaitez occasionnellement désactiver le flux de trafic entre les deux réseaux virtuels. Il se peut que vous trouviez plus pratique d’activer ou de désactiver les homologations que de les supprimer et recréer. Lorsque ce paramètre est désactivé, le trafic ne passe plus entre les réseaux virtuels homologues.
    - **Autoriser le trafic transféré :** Cochez cette case pour autoriser le flux du trafic *transféré* par une appliance virtuelle réseau d’un réseau virtuel (ne provenant pas du réseau virtuel) vers ce réseau virtuel via un peering. Par exemple, considérez trois réseaux virtuels nommés Spoke1, Spoke2 et Hub. Une homologation existe entre chaque réseau virtuel spoke et le réseau virtuel Hub, mais les homologations n’existent pas entre les réseaux virtuels spoke. Une appliance virtuelle réseau est déployée dans le réseau virtuel Hub et des itinéraires définis par l’utilisateur sont appliqués à chaque réseau virtuel spoke acheminant le trafic entre les sous-réseaux via l’appliance virtuelle réseau. Si cette case n’est pas cochée pour l’appairage entre chaque réseau virtuel spoke et le réseau virtuel Hub, le trafic ne passe pas entre les réseaux virtuels spoke, car le hub ne transfère pas le trafic entre les réseaux virtuels. Si l’activation de cette fonctionnalité autorise le transfert du trafic via l’homologation, elle n’a pas pour effet de créer des itinéraires définis par l’utilisateur ou des appliances virtuelles. Les itinéraires définis par l’utilisateur et les appliances virtuelles sont créés séparément. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Il est inutile de cocher ce paramètre si le trafic est transféré entre les réseaux virtuels par le biais d’une passerelle VPN Azure.
    - **Autoriser le transit par passerelle :** Cochez cette case si une passerelle de réseau virtuel est attachée à ce réseau virtuel, et si vous souhaitez autoriser le trafic en provenance du réseau virtuel appairé via la passerelle. Par exemple, ce réseau virtuel peut être attaché à un réseau local via une passerelle de réseau virtuel. La passerelle peut être une passerelle ExpressRoute ou VPN. L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance du réseau virtuel homologué passe par la passerelle attachée à ce réseau virtuel vers le réseau local. Si vous cochez cette case, le réseau virtuel homologué ne peut pas avoir de passerelle configurée. La case à cocher **Utiliser des passerelles distantes** doit être activée pour le réseau virtuel homologué lors de la configuration de l’homologation de l’autre réseau virtuel à ce réseau virtuel. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel homologué continue d’être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel. Si l’appairage est entre un réseau virtuel (Resource Manager) et un réseau virtuel (classique), la passerelle doit se trouver dans le réseau virtuel (Resource Manager).

       En plus de transférer le trafic vers un réseau local, une passerelle VPN peut transférer le trafic réseau entre les réseaux virtuels qui sont homologués avec le réseau virtuel dans lequel la passerelle se trouve, sans que les réseaux virtuels n’aient besoin d’être homologués entre eux. L’utilisation d’une passerelle VPN pour le transfert du trafic est utile lorsque vous souhaitez utiliser une passerelle VPN dans un réseau virtuel Hub (consultez l’exemple de hub-and-spoke décrit pour **Autoriser le trafic transféré**) afin d’acheminer le trafic entre des réseaux virtuels spoke qui ne sont pas appairés. Pour en savoir plus sur l’autorisation d’utilisation d’une passerelle pour le transit, consultez [Configurer une passerelle VPN pour le transit dans un appairage de réseaux virtuels](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ce scénario nécessite l’implémentation d’itinéraires définis par l’utilisateur qui spécifient la passerelle de réseau virtuel en tant que type de tronçon suivant. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Vous ne pouvez spécifier une passerelle VPN en tant que type de tronçon suivant uniquement dans un itinéraire défini par l’utilisateur, vous ne pouvez pas spécifier une passerelle ExpressRoute en tant que type de tronçon suivant dans un itinéraire défini par l’utilisateur.

    - **Utiliser des passerelles distantes :** Cochez cette case pour autoriser le trafic en provenance de ce réseau virtuel à passer par une passerelle de réseau virtuel attachée au réseau virtuel avec lequel vous effectuez le peering. Par exemple, le réseau virtuel avec lequel vous opérez l’homologation a une passerelle VPN attachée qui permet la communication avec un réseau local.  L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance de ce réseau virtuel passe par la passerelle VPN attachée au réseau virtuel homologué. Si vous cochez cette case, le réseau virtuel homologué doit avoir une passerelle de réseau virtuel attachée, et la case à cocher **Autoriser le transit par passerelle** doit être activée pour ce réseau. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel homologué peut toujours être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel.
    Une seule homologation pour ce réseau virtuel peut avoir ce paramètre activé.

        Vous ne pouvez pas utiliser de passerelles distantes si vous avez déjà configuré une passerelle dans votre réseau virtuel. Pour en savoir plus sur l’utilisation d’une passerelle pour le transit, consultez [Configurer une passerelle VPN pour le transit dans un appairage de réseaux virtuels](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

6. Pour ajouter l’homologation au réseau virtuel que vous avez sélectionné, cliquez sur **OK**.

Pour obtenir des instructions pas à pas sur l’implémentation de l’homologation entre des réseaux virtuels dans différents abonnements et modèles de déploiement, consultez la section [Étapes suivantes](#next-steps).

### <a name="commands"></a>Commandes

- **Azure CLI** : [az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell** : [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Afficher ou modifier les paramètres d’homologation

Avant de modifier une homologation, familiarisez-vous avec les exigences et contraintes ainsi qu’avec les [autorisations nécessaires](#permissions).

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel dont vous souhaitez modifier les paramètres d’homologation.
3. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
4. Sélectionnez l’homologation que vous souhaitez afficher ou dont vous voulez modifier les paramètres.
5. Modifiez le paramètre approprié. Lisez les options de chaque paramètre de l’[étape 5](#add-peering) de Créer un appairage.
6. Sélectionnez **Enregistrer**.

**Commandes**

- **Azure CLI** : [az network vnet peering list](/cli/azure/network/vnet/peering) pour afficher la liste des homologations d’un réseau virtuel, [az network vnet peering show](/cli/azure/network/vnet/peering) pour afficher les paramètres d’une homologation spécifique et [az network vnet peering update](/cli/azure/network/vnet/peering) pour modifier les paramètres de l’homologation.|
- **PowerShell** : [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) pour afficher les paramètres d’appairage et [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) pour modifier les paramètres.

## <a name="delete-a-peering"></a>Supprimer une homologation

Avant de supprimer une homologation, vérifiez que votre compte possède les [autorisations nécessaires](#permissions).

En cas de suppression d’une homologation, le trafic en provenance d’un réseau virtuel n’est plus acheminé vers le réseau virtuel homologué. Lorsque des réseaux virtuels déployés via le Gestionnaire de ressources sont homologués, chaque réseau virtuel a une homologation à l’autre réseau virtuel. Si la suppression de l’homologation d’un réseau virtuel désactive la communication entre les réseaux virtuels, elle ne supprime pas l’homologation de l’autre réseau virtuel. L’état d’homologation pour l’homologation existant dans l’autre réseau virtuel est **Déconnectée**. Vous ne pouvez pas recréer l’homologation tant que vous n’avez pas recréé l’homologation dans le premier réseau virtuel et que l’état d’homologation des deux réseaux virtuels n’est pas *Connectée*.

Si vous souhaitez que les réseaux virtuels communiquent occasionnellement, au lieu de supprimer une homologation, vous pouvez définir le paramètre **Autoriser l’accès au réseau virtuel** sur **Désactivé**. Pour savoir comment procéder, consultez l’étape 6 de la section Créer un appairage de cet article. Il se peut que vous trouviez plus facile de désactiver et activer l’accès réseau que de supprimer et recréer des homologations.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel pour lequel vous souhaitez supprimer une homologation.
3. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
4. Du côté droit de l’homologation que vous souhaitez supprimer, sélectionnez **...** , **Supprimer**, puis **Oui** pour supprimer l’homologation du premier réseau virtuel.
5. Suivez les étapes précédentes pour supprimer l’homologation de l’autre réseau virtuel dans l’homologation.

**Commandes**

- **Azure CLI** : [az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell** : [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Exigences et contraintes

- <a name="cross-region"></a>Vous pouvez appairer des réseaux virtuels dans la même région ou dans différentes régions. L’appairage de réseaux virtuels dans des régions différentes est également appelé *Global VNet Peering*. 
- Lors de la création d’un Peering mondial, les réseaux virtuels appairés peuvent se trouver dans n’importe quelle région de clouds publics Azure, dans des régions de clouds Azure Chine ou Azure Government. Vous ne pouvez pas appairer entre plusieurs clouds. Par exemple, un réseau virtuel dans le cloud public Azure ne peut pas être appairé à celui d’un cloud Azure Chine.
- Les ressources situées dans un réseau virtuel ne peuvent pas communiquer avec l’adresse IP frontale d’un équilibreur de charge interne Azure dans un réseau virtuel appairé à l’échelle mondiale. La prise en charge d’un équilibreur de charge de base n’existe que dans la même région. La prise en charge d’un équilibreur de charge de base existe pour les appairages VNet Peering et Global VNet Peering. Les services qui utilisent un équilibreur de charge de base qui ne fonctionne pas sur un appairage Global VNet Peering sont détaillés [ici.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Vous pouvez utiliser des passerelles distantes ou autoriser un transit par passerelle dans des réseaux virtuels appairés à l’échelle mondiale et en local.
- Les réseaux virtuels peuvent être dans des abonnements identiques ou différents. Quand vous appairez des réseaux virtuels de différents abonnements, les deux abonnements peuvent être associés au même locataire Azure Active Directory ou à un locataire différent. Si vous n’avez pas encore de locataire AD, vous pouvez rapidement en [créer un](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). La prise en charge du peering entre réseaux virtuels à partir d’abonnements associés à différents locataires Azure Active Directory n’est pas disponible dans le portail. Vous pouvez utiliser l’interface CLI, PowerShell ou des modèles.
- Les réseaux virtuels que vous homologuez doivent avoir des espaces d’adressage IP qui ne se chevauchent pas.
- Il n’est pas possible d’ajouter ou de supprimer des plages d’adresses dans l’espace d’adressage d’un réseau virtuel après que celui-ci a été homologué avec un autre réseau virtuel. Pour ajouter ou supprimer des plages d’adresses, supprimez l’homologation, ajoutez ou supprimez les plages d’adresses, puis recréez l’homologation. Pour ajouter ou supprimer des plages d’adresses dans des réseaux virtuels, voir [Gérer les réseaux virtuels](manage-virtual-network.md).
- Vous pouvez homologuer deux réseaux virtuels déployés via le Gestionnaire de ressources, ou homologuer un réseau virtuel déployé via le Gestionnaire de ressources avec un réseau virtuel déployé via le modèle de déploiement classique. Vous ne pouvez pas homologuer deux réseaux virtuels créés via le modèle de déploiement classique. Si vous n’êtes pas familiarisé avec les modèles de déploiement Azure, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour connecter deux réseaux virtuels créés via le modèle de déploiement classique.
- Lors de l’homologation de deux réseaux virtuels créés via le Gestionnaire de ressources, une homologation doit être configurée pour chaque réseau virtuel dans l’homologation. L’un des états suivants s’affiche pour l’homologation : 
  - *Date :* Lorsque vous créez l’homologation au deuxième réseau virtuel à partir du premier réseau virtuel, l’état d’homologation est *Initiée*. 
  - *Connecté :* Lorsque vous créez l’homologation à partir du deuxième réseau virtuel au premier réseau virtuel, l’état d’homologation est *Connectée*. Si vous affichez l’état d’homologation pour le premier réseau virtuel, vous voyez que son état est passé de *Initiée* à *Connectée*. L’homologation n’est pas correctement établie tant que l’état d’homologation pour les deux homologations de réseau virtuel est *Connectée*.
- Lors de l’homologation d’un réseau virtuel créé via le Gestionnaire de ressources avec un réseau virtuel créé via le modèle de déploiement classique, vous configurez une homologation uniquement pour le réseau virtuel est déployé via le Gestionnaire de ressources. Vous ne pouvez pas configurer d’homologation pour un réseau virtuel (classique), ou entre deux réseaux virtuels déployés via le modèle de déploiement classique. Lorsque vous créez l’homologation à partir du réseau virtuel (Gestionnaire des ressources) au réseau virtuel (classique), l’état d’homologation est *Mise à jour*, puis passe rapidement à *Connectée*.
- Une homologation est établie entre deux réseaux virtuels. Les homologations ne sont pas transitives. Imaginons que vous créez des homologations entre :
  - VirtualNetwork1 et VirtualNetwork2
  - VirtualNetwork2 et VirtualNetwork3

  Il n’existe aucune homologation entre VirtualNetwork1 et VirtualNetwork3 via VirtualNetwork2. Si vous souhaitez créer une homologation de réseaux virtuels entre VirtualNetwork1 et VirtualNetwork3, vous devez créer une homologation entre VirtualNetwork1 et VirtualNetwork3.
- Vous ne pouvez pas résoudre des noms dans des réseaux virtuels homologués en utilisant une résolution de noms Azure par défaut. Pour résoudre des noms dans d’autres réseaux virtuels, vous devez utiliser [Azure DNS pour les domaines privés](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou un serveur DNS personnalisé. Pour savoir comment configurer votre propre serveur DNS, consultez [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Les ressources des réseaux virtuels homologués dans la même région peuvent communiquer entre elles avec les mêmes bande passante et latence que si elles étaient dans le même réseau virtuel. Toutefois, chaque taille de machine virtuelle a sa propre bande passante réseau maximale. Pour en savoir plus sur la bande passante réseau maximale pour différentes tailles de machine virtuelle, consultez les tailles de machine virtuelle pour [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Un réseau virtuel peut être homologué ainsi qu’être connecté à un autre réseau virtuel avec une passerelle de réseau virtuel Azure. Lorsque des réseaux virtuels sont connectés via une homologation et une passerelle, le trafic entre les réseaux virtuels passe par l’homologation plutôt que par la passerelle.
- Les clients VPN point à site doivent être retéléchargés une fois que le peering de réseaux virtuels a été configuré avec succès pour garantir le téléchargement des nouvelles routes sur le client.
- Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une homologation de réseau virtuel. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Autorisations

Les comptes pouvant être utilisés avec l’appairage de réseaux virtuels doivent avoir les rôles suivants :

- [Contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) : pour un réseau virtuel déployé via Resource Manager.
- [Contributeur de réseaux classiques](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor) : pour un réseau virtuel déployé via le modèle de déploiement classique.

Si votre compte n’a pas l’un des rôles ci-dessus, il doit avoir un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) auquel sont assignées les actions appropriées répertoriées dans le tableau suivant :

| Action                                                          | Nom |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Action requise pour créer un appairage entre un réseau virtuel A et un réseau virtuel B. Le réseau virtuel A doit être un réseau virtuel (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Action requise pour créer un appairage entre un réseau virtuel B (Resource Manager) et un réseau virtuel A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Action requise pour créer un appairage entre un réseau virtuel B (classique) et un réseau virtuel A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Action requise pour lire un appairage de réseaux virtuels   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Action requise pour supprimer un appairage de réseaux virtuels |

## <a name="next-steps"></a>Étapes suivantes

- Une homologation est générée entre des réseaux virtuels créés via des modèles de déploiement identiques ou différents qui existent dans des abonnements identiques ou différents. Suivez un didacticiel pour l’un des scénarios suivants :

  |Modèle de déploiement Azure             | Abonnement  |
  |---------                          |---------|
  |Les deux modèles Resource Manager              |[Identique](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Différent](create-peering-different-subscriptions.md)|
  |Un modèle Resource Manager, un modèle classique  |[Identique](create-peering-different-deployment-models.md)|
  |                                   |[Différent](create-peering-different-deployment-models-subscriptions.md)|

- Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Créez un appairage de réseaux virtuels avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou à l’aide des [modèles Azure Resource Manager](template-samples.md)
- Créez et appliquez une [stratégie Azure](policy-samples.md) pour des réseaux virtuels
