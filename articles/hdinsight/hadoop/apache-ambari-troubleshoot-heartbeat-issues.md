---
title: Problèmes de pulsations Apache Ambari dans Azure HDInsight
description: Diverses raisons à l’origine des problèmes de pulsation Apache ambari dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: a74e826e9a4725c9b689dc97101b00615771d4d1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781334"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problèmes de pulsations Apache Ambari dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scénario : Utilisation élevée du processeur

### <a name="issue"></a>Problème

L’agent Ambari a une utilisation élevée du processeur, ce qui génère dans l’interface utilisateur Ambari des alertes pour certains nœuds indiquant que la pulsation de l’agent Ambari est perdue.

### <a name="cause"></a>Cause :

En raison de divers bogues, dans de rares cas, votre agent Ambari peut avoir un pourcentage élevé (proche de 100) d’utilisation du processeur.

### <a name="resolution"></a>Résolution :

1. Identifiez l’ID de processus (PID) de l’agent Ambari :

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Exécutez ensuite la commande suivante pour afficher l’utilisation de l’UC :

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Redémarrez l’agent Ambari pour atténuer le problème :

    ```bash
    service ambari-agent restart
    ```

1. Si le redémarrage ne fonctionne pas, arrêtez le processus de l’agent Ambari, puis démarrez-le :

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scénario : Agent Ambari non démarré

### <a name="issue"></a>Problème

L’agent Ambari n’a pas démarré, ce qui génère dans l’interface utilisateur Ambari des alertes pour certains nœuds indiquant que la pulsation de l’agent Ambari est perdue.

### <a name="cause"></a>Cause :

Les alertes sont provoquées par l’agent Ambari qui n’est pas en cours d’exécution.

### <a name="resolution"></a>Résolution :

1. Confirmez l’état de l’agent Ambari :

    ```bash
    service ambari-agent status
    ```

1. Vérifiez si les services de contrôleur de basculement sont en cours d’exécution :

    ```bash
    ps -ef | grep failover
    ```

    Si les services du contrôleur de basculement ne sont pas en cours d’exécution, il est probable qu’un problème empêche l’agent HDInsight de démarrer le contrôleur de basculement. Vérifiez le journal de l’agent HDInsight à partir du fichier `/var/log/hdinsight-agent/hdinsight-agent.out`.

---

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
