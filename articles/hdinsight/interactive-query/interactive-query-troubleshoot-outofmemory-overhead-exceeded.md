---
title: Les jointures dans Apache Hive provoquent une erreur OutOfMemory dans Azure HDInsight
description: Traitement des erreurs OutOfMemory « Limite de la surcharge GC dépassée »
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 45b19bdfc12313974252c81c2597645b1ceb2018
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668657"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scénario : Les jointures dans Apache Hive provoquent une erreur OutOfMemory dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Le comportement par défaut pour les jointures Apache Hive consiste à charger l’intégralité du contenu d’une table dans la mémoire afin qu’une jointure puisse être effectuée sans avoir à effectuer une étape de mappage/réduction. Si la table Hive est trop grande pour tenir dans la mémoire, la requête peut échouer.

## <a name="cause"></a>Cause :

Lors de l’exécution de jointures dans une ruche de taille suffisante, l’erreur suivante se produit :

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Résolution :

Empêchez Hive de charger les tables en mémoire sur les jointures (au lieu d’effectuer une étape de mappage/réduction) en définissant la valeur de configuration Hive suivante :

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Étapes suivantes

Si la définition de cette valeur n’a pas résolu votre problème, envisagez l’une des solutions suivantes...

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
