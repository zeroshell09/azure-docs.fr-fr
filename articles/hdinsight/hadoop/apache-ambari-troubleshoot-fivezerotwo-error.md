---
title: Erreur 502 de l’interface utilisateur Apache Ambari dans Azure HDInsight
description: Erreur 502 de l’interface utilisateur Apache Ambari dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 1b79ef044271c926bf8c4255d0cee67943a0e58b
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886480"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scénario : Erreur 502 de l’interface utilisateur Apache Ambari dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Lorsque vous essayez d’accéder à l’interface utilisateur Apache Ambari pour votre cluster HDInsight, vous recevez un message similaire à celui-ci : « 502 - Le serveur web a reçu une réponse non valide lors de son utilisation en tant que passerelle ou serveur proxy. »

## <a name="cause"></a>Cause :

En général, le code d’état HTTP 502 signifie que le serveur Ambari ne fonctionne pas correctement sur le nœud principal actif. Il existe quelques causes principales possibles.

## <a name="resolution"></a>Résolution :

Dans la plupart des cas, pour atténuer le problème, vous pouvez redémarrer le nœud principal actif. Vous pouvez aussi choisir une machine virtuelle de taille supérieure pour votre nœud principal.

### <a name="ambari-server-failed-to-start"></a>Échec du démarrage du serveur Ambari

Vous pouvez vérifier les journaux du serveur Ambari pour savoir pourquoi ce dernier n’a pas pu démarrer. L’une des raisons courantes est l’erreur de vérification de la cohérence de la base de données. Vous pouvez trouver ce renseignement dans ce fichier journal : `/var/log/ambari-server/ambari-server-check-database.log`.

Si vous avez apporté des modifications au nœud de cluster, annulez-les. Utilisez toujours l’interface utilisateur Ambari pour modifier les configurations associées à Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Le serveur Ambari utilise la totalité du processeur

Dans de rares cas, nous avons vu que le processus du serveur Ambari est proche en permanence de 100 % de la capacité du processeur. En guise d’atténuation, vous pouvez établir une connexion SSH au nœud principal actif, puis arrêter le processus du serveur Ambari et le redémarrer.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Serveur Ambari arrêté par le débogueur de mémoire insuffisante

Dans certains scénarios, votre nœud principal ne dispose pas de suffisamment de mémoire et le débogueur de mémoire insuffisante Linux commence à sélectionner les processus à arrêter. Vous pouvez vérifier cette situation en recherchant l’ID de processus AmbariServer, qui doit être introuvable. Examinez ensuite `/var/log/syslog` et recherchez quelque chose qui ressemble à ce qui suit :

```
Jul 27 15:29:30 hn0-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Identifiez ensuite les processus qui utilisent les mémoires et essayez d’approfondir la cause principale.

### <a name="other-issues-with-ambari-server"></a>Autres problèmes liés au serveur Ambari

Il est rare que le serveur Ambari ne puisse pas traiter la requête entrante. Vous pouvez trouver plus d’informations en recherchant toute erreur dans les journaux du serveur Ambari. L’un de ces cas est une erreur semblable à celle-ci :

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
