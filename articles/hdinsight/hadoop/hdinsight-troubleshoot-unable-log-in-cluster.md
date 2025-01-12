---
title: Impossible de se connecter au cluster Azure HDInsight
description: Impossible de se connecter au cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: eb7249fc88f37e5c15447e5a8907468a851d2416
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737283"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scénario : Impossible de se connecter au cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Impossible de se connecter au cluster Azure HDInsight.

## <a name="cause"></a>Cause :

Les raisons peuvent varier. N’oubliez pas d’utiliser vos informations d’identification « connexion cluster » ou « HTTP » lorsque vous vous connectez aux tableaux de bord des clusters ou des applications. Lorsque vous vous connectez à distance, utilisez vos informations d’identification SSH (Secure Shell) ou du Bureau à distance.

## <a name="resolution"></a>Résolution :

Pour résoudre les problèmes courants, essayez une ou plusieurs des étapes suivantes.

* Essayez d’ouvrir le tableau de bord du cluster dans un nouvel onglet du navigateur, en mode de confidentialité.

* Si vous avez oublié vos informations d’identification SSH, [réinitialisez-les dans l’interface utilisateur d’Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
