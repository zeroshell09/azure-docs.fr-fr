---
title: Intégrer vos machines virtuelles Azure Stack à Azure Sentinel | Microsoft Docs
description: Cet article vous montre comment approvisionner l’extension de machine virtuelle Azure Monitor, Update and Configuration Management sur des machines virtuelles Azure Stack et commencer à les surveiller avec Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: rkarlin
ms.openlocfilehash: caaf708b3efd8ffbe059f8ad249b7945d31fd7cc
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600500"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Connecter des machines virtuelles Azure Stack à Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Avec Azure Sentinel, vous pouvez surveiller vos machines virtuelles s’exécutant sur Azure et Azure Stack à un même emplacement. Pour intégrer vos machines Azure Stack à Azure Sentinel, vous devez d’abord ajouter l’extension de machine virtuelle à vos machines virtuelles Azure Stack existantes. 

Après avoir connecté des machines Azure Stack, vous pouvez faire votre choix parmi une galerie de tableaux de bord qui mettent en avant les insights provenant de vos sources de données. Ces tableaux de bord peuvent être aisément adaptés à vos besoins.



## <a name="add-the-virtual-machine-extension"></a>Ajouter l’extension de machine virtuelle 

Ajoutez l’extension de machine virtuelle **Azure Monitor, Update and Configuration Management** aux machines virtuelles en cours d’exécution sur votre système Azure Stack. 

1. Dans un nouvel onglet de navigateur, connectez-vous à votre [portail Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Accédez à la page **Machines virtuelles**, puis sélectionnez la machine virtuelle que vous souhaitez protéger avec Azure Sentinel. Pour plus d’informations sur la création d’une machine virtuelle sur Azure Stack, consultez [Créer une machine virtuelle Windows Server avec le portail Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [Créer une machine virtuelle serveur Linux sur le portail Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Sélectionnez **Extensions**. La liste des extensions de machine virtuelle installées sur cette machine virtuelle s’affiche.
4. Cliquez sur l’onglet **Ajouter**. Le panneau de menus **Nouvelle ressource** s’ouvre et affiche la liste des extensions de machine virtuelle disponibles. 
5. Sélectionnez l’extension **Azure Monitor, Update, and Configuration Management** et cliquez sur **Créer**. La fenêtre de configuration **Installer l’extension** s’ouvre.

   ![Paramètres d’Azure Monitor, Update, and Configuration Management](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Si l’extension **Azure Monitor, Update and Configuration Management** n’est pas listée dans votre place de marché, contactez votre opérateur Azure Stack pour qu’elle soit disponible.

6. Dans le menu Azure Sentinel, sélectionnez **Paramètres d’espace de travail** suivi d’**Avancé**, puis copiez l’**ID d'espace de travail** et la **Clé de l’espace de travail (clé primaire)** . 
1. Dans la fenêtre **Installer l’extension** d’Azure Stack, collez-les dans les champs indiqués, puis cliquez sur **OK**.
1. Une fois l’installation de l’extension terminée, son état s’affiche en tant qu’**Approvisionnement réussi**. Il peut s’écouler une heure avant que la machine virtuelle n’apparaisse dans le portail Azure Sentinel.

Pour plus d’informations sur l’installation et la configuration de l’agent pour Windows, consultez la page [Connecter des ordinateurs Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Pour plus d’informations sur la résolution des problèmes lié à l’agent Linux, consultez [Résoudre les problèmes liés à l’agent Linux Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

Dans le portail Azure Sentinel sur Azure, la fenêtre **Machines Virtuelles** affiche une vue d’ensemble de toutes les machines virtuelles et de tous les ordinateurs ainsi que du statut correspondant. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous n’en avez plus besoin, vous pouvez supprimer l’extension de la machine virtuelle par le biais du portail Azure Stack.

Pour supprimer l’extension :

1. Ouvrez le **portail Azure Stack**.
2. Accédez à **Machines virtuelles**, sélectionnez la machine virtuelle dont vous souhaitez supprimer l’extension.
3. Sélectionnez **Extensions**, puis sélectionnez l’extension **Microsoft.EnterpriseCloud.Monitoring**.
4. Cliquez sur **Désinstaller**, puis confirmez votre choix.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
- Diffusez des données à partir d’[appliances de format d’erreur courants](connect-common-event-format.md) vers Azure Sentinel.
