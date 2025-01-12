---
title: Erreur 404 " introuvable " du serveur Jupyter en raison d’un " blocage d’API Cross Origin " dans Azure HDInsight
description: Erreur 404 " introuvable " du serveur Jupyter en raison d’un " blocage d’API Cross Origin " dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 57ecf081b48097b04d8379119d9a08f0b980494d
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68664872"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scénario : Erreur 404 " introuvable " du serveur Jupyter en raison d’un " blocage d’API Cross Origin " dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Lorsque vous accédez au service Jupyter sur HDInsight, un message d’erreur indiquant « Introuvable » s’affiche. Si vous consultez les journaux Jupyter, vous obtenez un résultat semblable à ce qui suit :

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Vous pouvez également voir une adresse IP dans le champ « Origine » dans le journal Jupyter.

## <a name="cause"></a>Cause :

Cette erreur peut avoir les causes suivantes :

- Si vous avez configuré des règles de groupe de sécurité réseau (NSG) pour limiter l’accès au cluster. La restriction de l’accès avec des règles NSG vous permet toujours d’accéder directement à Apache Ambari et à d’autres services à l’aide de l’adresse IP plutôt que du nom de cluster. Toutefois, lors de l’accès à Jupyter, vous pouvez voir une erreur 404 « Introuvable ».

- Si vous avez donné à votre passerelle HDInsight un nom DNS personnalisé autre que le `xxx.azurehdinsight.net` standard.

## <a name="resolution"></a>Résolution :

1. Modifiez les fichiers jupyter.py dans ces deux emplacements :

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Recherchez la ligne qui indique : `NotebookApp.allow_origin='\"https://{2}.{3}\"'`. Et remplacez-la par : `NotebookApp.allow_origin='\"*\"'`.

1. Redémarrez le service Jupyter à partir d’Ambari.

1. La saisie de `ps aux | grep jupyter` à l’invite de commandes doit indiquer qu’elle autorise toute URL s’y connecter.

Ce paramètre est moins sécurisé que celui que nous avions déjà mis en place. Mais l’accès au cluster est supposé restreint et un utilisateur extérieur est autorisé à se connecter au cluster car un groupe de sécurité réseau est en place.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
