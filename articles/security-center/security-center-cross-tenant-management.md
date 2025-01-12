---
title: Gestion multilocataire dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer la collecte des données dans Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: v-mohabe
ms.openlocfilehash: d6b5b528c3021bfb62bc30ad5910524db36e7e95
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950545"
---
# <a name="cross-tenant-management-in-security-center"></a>Gestion multilocataire dans Security Center

La gestion multilocataire permet d’afficher et de gérer la posture de sécurité de plusieurs locataires dans Security Center, en tirant parti de la [gestion déléguée des ressources Azure](../lighthouse/concepts/azure-delegated-resource-management.md). Gérez efficacement plusieurs locataires à partir d’une même vue, sans avoir à vous connecter à l’annuaire de chaque locataire.

- Les fournisseurs de services peuvent gérer la posture de sécurité des ressources pour plusieurs clients, à partir de leur propre locataire.

- Les équipes de sécurité qui comprennent plusieurs locataires peuvent afficher et gérer leur posture de sécurité à partir d’un même emplacement.

## <a name="set-up-cross-tenant-management"></a>Configurer la gestion multilocataire

Configurez la gestion multilocataire en déléguant à votre propre locataire l’accès aux ressources des locataires managés, en utilisant la [gestion des ressources déléguée Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> La gestion des ressources déléguées Azure est l’un des principaux composants d’Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Fonctionnement de la gestion multilocataire dans Security Center

Vous pouvez examiner et gérer les abonnements de plusieurs locataires de la même façon que vous géreriez plusieurs abonnements dans un seul locataire.

Dans la barre de menus supérieure, cliquez sur l’icône de filtre, puis sélectionnez les abonnements de l’annuaire de chaque client que vous souhaitez voir.

  ![Filtrer les locataires](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Les vues et les actions sont plus ou moins identiques. Voici quelques exemples :

- **Gérer les stratégies de sécurité** : Dans une vue, vous pouvez gérer la posture de sécurité de nombreuses ressources à l’aide de [stratégies](tutorial-security-policy.md), appliquer les recommandations de sécurité, ainsi que collecter et gérer les données liées à la sécurité.
- **Améliorer le degré de sécurisation et la posture de conformité** : La visibilité multilocataire vous permet de voir la posture de sécurité globale de tous vos locataires, et ainsi, de déterminer où et comment améliorer le [degré de sécurisation](security-center-secure-score.md) et la [posture de conformité](security-center-compliance-dashboard.md) pour chacun d’entre eux.
- **Appliquer les recommandations** : Vous pouvez surveiller et appliquer une [recommandation](security-center-recommendations.md) à toutes les ressources des différents locataires en même temps. Ensuite, vous pouvez immédiatement vous attaquer aux vulnérabilités qui présentent le risque le plus élevé parmi tous les locataires.
- **Gérer les alertes** : Détectez les [alertes](security-center-alerts-overview.md) sur les différents locataires. Appliquez les recommandations concernant les ressources non conformes à l’aide des [étapes de correction](security-center-managing-and-responding-alerts.md).

- **Gérer les fonctionnalités avancées de défense cloud et bien plus encore** : Gérez les divers services de détection des menaces et de protection contre les menaces, comme l’[accès juste-à-temps (JIT) aux machines virtuelles](security-center-just-in-time.md), le [renforcement du réseau adaptatif](security-center-adaptive-network-hardening.md), les [contrôles d’application adaptatifs ](security-center-adaptive-application.md), etc.
 
## <a name="next-steps"></a>Étapes suivantes
Cet article explique le fonctionnement de la gestion multilocataire dans Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Renforcer votre posture de sécurité avec Azure Security Center](security-center-monitoring.md) : découvrez comment superviser l’intégrité de vos ressources Azure.
* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
