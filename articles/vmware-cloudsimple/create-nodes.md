---
title: Approvisionner des nœuds pour la solution VMware de CloudSimple - Azure
description: Découvrez comment ajouter des nœuds à votre déploiement VMWare avec CloudSimple
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5806198968d98fea4c5cbf8731358ca4041f0935
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972884"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Provisionner des nœuds pour la solution Azure VMware de CloudSimple

Approvisionnez des nœuds sur le portail Azure. Vous pouvez ensuite configurer la capacité avec un paiement à l’utilisation pour votre environnement de cloud privé CloudSimple.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Ajouter un nœud à votre cloud privé CloudSimple

1. Sélectionnez **Tous les services**.
2. Recherchez **Nœuds CloudSimple**.

   ![Rechercher des nœuds CloudSimple](media/create-cloudsimple-node-search.png)

3. Sélectionnez **Nœuds CloudSimple**.
4. Cliquez sur **Ajouter** pour créer les nœuds.

    ![Ajouter des nœuds CloudSimple](media/create-cloudsimple-node-add.png)

5. Sélectionnez l’abonnement dans lequel vous souhaitez approvisionner des nœuds CloudSimple.
6. Sélectionnez le groupe de ressources pour vos nœuds. Pour ajouter un nouveau groupe de ressources, cliquez sur **Créer**.
7. Entrez le préfixe pour identifier les nœuds.
8. Sélectionnez l’emplacement des ressources de nœud.
9. Sélectionnez l’emplacement dédié où héberger les ressources de nœud.
10. Sélectionnez le type de nœud. Vous pouvez choisir l’[option CS28 ou CS36](cloudsimple-node.md). Cette dernière option inclut la capacité de calcul et de mémoire maximale.
11. Sélectionnez le nombre de nœuds à approvisionner.
12. Sélectionnez **Vérifier + créer**.
13. Passez en revue les paramètres. Pour modifier les paramètres, cliquez sur **Précédent**.
14. Sélectionnez **Create** (Créer).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cloud privé](create-private-cloud.md)
