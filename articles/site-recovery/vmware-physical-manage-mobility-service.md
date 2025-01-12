---
title: Gérer l’agent Mobilité sur les serveurs pour la récupération d'urgence des serveurs physiques et des machines virtuelles VMware à l'aide d'Azure Site Recovery | Microsoft Docs
description: Gérez l'agent du service Mobilité pour la récupération d'urgence des serveurs physiques et des machines virtuelles VMware sur Azure à l’aide du service Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 0a8b3a8bcfc2aa8270d7be140a94e5b83973f3e5
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972132"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Gérer l’agent Mobilité sur les ordinateurs protégés

Quand vous utilisez Azure Site Recovery pour la récupération d'urgence de machines virtuelles VMware et de serveurs physiques sur Azure, vous devez configurer l’agent de mobilité sur votre serveur. L’agent de mobilité coordonne la communication entre votre ordinateur protégé, le serveur de configuration/serveur de traitement scale-out et gère la réplication des données. Cet article récapitule les tâches courantes de gestion de l’agent de mobilité après son déploiement.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mettre à jour le service de mobilité à partir du portail Azure

1. Avant de commencer, veillez à ce que le serveur de configuration, les serveurs de processus de scale-out et les serveurs cibles maîtres qui font partie de votre déploiement soient mis à jour avant de procéder à la mise à jour du service Mobilité sur les machines protégées.
2. Sur le portail, ouvrez le coffre > **Éléments répliqués**.
3. Si le serveur de configuration correspond à la dernière version, une notification doit s’afficher et indiquer « Une nouvelle mise à jour de l’agent de réplication Site Recovery est disponible. Cliquez pour installer. »

     ![Fenêtre Éléments répliqués](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Cliquez sur la notification, puis, dans **Mise à jour de l’agent**, sélectionnez les machines sur lesquelles vous souhaitez mettre à niveau le service Mobilité. Cliquez ensuite sur **OK**.

     ![Éléments répliqués - Liste des machines virtuelles](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. La tâche Mettre à jour le service Mobilité est alors lancée pour chacune des machines sélectionnées.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mettre à jour le service de mobilité via le script powershell sur le serveur Windows

Utiliser le script suivant pour mettre à niveau le service de mobilité sur un serveur via la cmdlet power shell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Mettre à jour le compte utilisé pour l’installation Push du service Mobilité

Au moment du déploiement de Site Recovery, pour activer l’installation Push du service Mobilité, vous avez spécifié un compte que le serveur de processus Site Recovery utilise afin d'accéder aux machines et installer le service lorsque de la réplication est activée pour la machine. Pour mettre à jour les informations d’identification de ce compte, suivez [ces instructions](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Désinstaller le service Mobilité

### <a name="on-a-windows-machine"></a>Sur une machine Windows

Désinstallez le service à partir de l’interface utilisateur ou d’une invite de commandes.

- **À partir de l’interface utilisateur** : dans le Panneau de configuration de la machine, sélectionnez **Programmes**. Sélectionnez **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery** > **Désinstaller**.
- **À partir d’une invite de commandes** : sur la machine, ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Exécutez la commande suivante : 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Sur une machine Linux
1. Sur la machine Linux, connectez-vous en tant qu’utilisateur **racine**.
2. Dans un terminal, accédez à /usr/local/ASR.
3. Exécutez la commande suivante :
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Installer le fournisseur VSS Site Recovery sur la machine source

Le fournisseur VSS Site Recovery est requis sur la machine source pour générer des points de cohérence d’application. Si l’installation du fournisseur n’a pas réussi par le biais de l’installation push, suivez les instructions ci-dessous pour l’installer manuellement.

1. Ouvrez la fenêtre admin cmd.
2. Accédez à l’emplacement d’installation du service Mobilité. (Par exemple C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Exécutez le script InMageVSSProvider_Uninstall.cmd. Le service sera alors désinstallé s’il existe déjà.
4. Exécutez le script InMageVSSProvider_Install.cmd pour installer le fournisseur VSS manuellement.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la reprise d'activité pour les machines virtuelles VMware](vmware-azure-tutorial.md)
- [Configurer la reprise d'activité pour les serveurs physiques](physical-azure-disaster-recovery.md)
