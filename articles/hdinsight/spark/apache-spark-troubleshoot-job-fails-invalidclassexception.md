---
title: La tâche Apache Spark échoue avec InvalidClassException, incompatibilité de version de classe, dans Azure HDInsight
description: La tâche Apache Spark échoue avec InvalidClassException, incompatibilité de version de classe, dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 6f81ed121c98c8428e710082aae7ab75b672335e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707414"
---
# <a name="scenario-apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Scénario : La tâche Apache Spark échoue avec InvalidClassException, incompatibilité de version de classe, dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous essayez de créer une tâche Apache Spark dans un cluster Spark 2.x. La tâche échoue avec une erreur semblable à la suivante :

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Cause :

Cette erreur peut être due à l’ajout d’un fichier jar supplémentaire à la configuration `spark.yarn.jars`, qui est un fichier jar « grisé » qui inclut une version différente du package `commons-lang3` et qui introduit une incompatibilité de classe. Par défaut, Spark 2.1/2/3 utilise la version 3.5 de `commons-lang3`.

## <a name="resolution"></a>Résolution :

Vous pouvez soit supprimer le fichier jar, soit recompiler le fichier jar personnalisé (AzureLogAppender) et utiliser [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) pour déplacer les classes.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
