---
title: Le travail de diffusion en continu Apache Spark qui lit des données à partir d’un cluster Apache Kafka échoue en générant une erreur NoClassDefFoundError dans Azure HDInsight
description: Le travail de diffusion en continu Apache Spark qui lit des données à partir d’un cluster Apache Kafka échoue en générant une erreur NoClassDefFoundError dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 986b1dd2e749a0968c744f861feb0ac0bf2376e8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700193"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>Scénario : Le travail de diffusion en continu Apache Spark qui lit des données à partir d’un cluster Apache Kafka échoue en générant une erreur NoClassDefFoundError dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Le cluster Apache Spark exécute une tâche de diffusion en continu Spark qui lit les données à partir d’un cluster Apache Kafka. La tâche de diffusion en continu Spark échoue si la compression du flux Kafka est activée. Dans ce cas, l’application Yarn de diffusion en continu Spark, application_1525986016285_0193, échoue en raison de l’erreur suivante :

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Cause :

Cette erreur peut être due à la spécification d’une version du fichier jar `spark-streaming-kafka` autre que celle du cluster Kafka que vous exécutez.

Par exemple, si vous exécutez un cluster Kafka version 0.10.1, la commande suivante génère une erreur :

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Résolution :

Utilisez la commande Spark-submit avec l’option `–packages` et assurez-vous que la version du fichier jar spark-streaming-kafka est identique à celle du cluster Kafka que vous exécutez.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, le support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
