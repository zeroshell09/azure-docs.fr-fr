---
title: Préparation de l’environnement cible en vue de la réplication VMware sur Azure | Microsoft Docs
description: Cet article décrit comment préparer votre environnement cible Azure en vue d’une réplication de machines virtuelles VMware sur Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723789"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Préparer l’environnement cible pour la récupération d’urgence des machines virtuelles VMware ou serveurs physiques vers Azure

Cet article décrit comment préparer votre environnement cible Azure avant de commencer la réplication de machines virtuelles VMware ou de serveurs physiques sur Azure.

## <a name="prerequisites"></a>Prérequis

L’article suppose que :
- Vous avez créé un coffre Recovery Services sur le [portail Azure](https://portal.azure.com "portail Azure") pour protéger vos machines sources.
- Vous avez configuré votre environnement local pour répliquer les [machines virtuelles VMware](vmware-azure-set-up-source.md) ou [serveurs physiques](physical-azure-set-up-source.md) sources sur Azure.

## <a name="prepare-target"></a>Préparer la cible

Après avoir réalisé l’**étape 1 : sélection de l’objectif de la protection** et l’**étape 2 : préparation de la source**, vous passez à l’**étape 3 : cible**.

![Préparer la cible](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonnement :** dans le menu déroulant, sélectionnez l’abonnement vers lequel vous souhaitez répliquer vos machines virtuelles ou serveurs physiques.
2. **Modèle de déploiement :** sélectionnez le modèle de déploiement (Classique ou Resource Manager).

Selon le modèle de déploiement choisi, une validation est lancée afin de vérifier que vous disposez au moins d’un réseau virtuel dans l’abonnement cible pour y répliquer et basculer les machines virtuelles et serveurs physiques.

Une fois les validations terminées avec succès, cliquez sur OK pour passer à l’étape suivante.

Si vous n’avez pas de réseau virtuel, vous pouvez en créer un en cliquant sur le bouton **+ Réseau** en haut de la page.

## <a name="next-steps"></a>Étapes suivantes
[Configurer les paramètres de réplication](vmware-azure-set-up-replication.md)
