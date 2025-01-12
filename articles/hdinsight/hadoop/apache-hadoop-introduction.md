---
title: Qu’est-ce que la pile de technologies Apache Hadoop ? - Azure HDInsight
description: Présentation de HDInsight ainsi que de la pile de technologies et des composants Apache Hadoop.
keywords: azure hadoop, hadoop azure, intro hadoop, introduction hadoop, pile de technologies hadoop, intro à hadoop, introduction à hadoop, qu’est-ce qu’un cluster hadoop, qu’est-ce un cluster hadoop, pourquoi hadoop est utilisé
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: 16845e5891117cc01bef5fae28cf4b36c0508651
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899554"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Qu’est-ce qu’Apache Hadoop dans Azure HDInsight ?

[Apache Hadoop](https://hadoop.apache.org/) était l’infrastructure open source d’origine de traitement et d’analyse distribués des jeux de données volumineuses sur des clusters. L’écosystème Hadoop comprend des logiciels et des utilitaires associés, notamment Apache Hive, Apache HBase, Spark, Kafka et bien d’autres encore.

Azure HDInsight est un service cloud d’analytique complètement managé, exhaustif et open source pour les entreprises. Le type de cluster Apache Hadoop dans Azure HDInsight vous permet d’utiliser HDFS, la gestion de ressources YARN et un modèle de programmation MapReduce simple pour traiter et analyser des lots de données en parallèle.

Pour voir les piles de composants de technologie Hadoop disponibles sur HDInsight, consultez [Composants et versions disponibles avec HDInsight](../hdinsight-component-versioning.md). Pour plus d’informations sur Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a id="whatis"></a>Qu’est-ce que MapReduce ?

Apache Hadoop MapReduce est une infrastructure logicielle qui permet d’écrire des tâches traitant d’importantes quantités de données. Les données d’entrée sont fractionnées en blocs indépendants. Chaque bloc est traité en parallèle sur les nœuds de votre cluster. Une tâche MapReduce se compose de deux fonctions :

* **Mappeur** : il consomme les données d’entrée, les analyse (généralement avec les opérations de tri et de filtre) et émet des tuples (paires clé-valeur).

* **Raccord de réduction** : il consomme les tuples émis par le Mappeur et effectue une opération de synthèse qui crée un résultat plus petit, combiné à partir des données du Mappeur.

Le résultat de ce travail indique le nombre d’occurrences de chaque mot dans le texte.

* Le mappeur prend chaque ligne du texte saisi en tant qu'entrée, et la divise en mots. Il émet une paire clé/valeur pour chaque occurrence de mot ou si le mot est suivi d’un 1. Le résultat est trié avant d'être envoyé au raccord de réduction.
* Ce dernier calcule la somme du compte de mots, puis émet une seule paire clé/valeur contenant le mot défini, suivi par la somme de ses occurrences.

MapReduce peut être implémenté dans plusieurs langues. Java est l'implémentation la plus courante. Ce langage est utilisé à titre d’exemple dans ce document.

## <a name="development-languages"></a>Langues de développement

Les langages ou infrastructures basés sur Java et la Machine virtuelle Java peuvent être exécutés directement comme une tâche MapReduce. L’exemple utilisé dans ce document est une application Java MapReduce. Les langages autres que Java, comme C#, Python ou des exécutables autonomes, doivent utiliser la **diffusion en continu Hadoop**.

La diffusion en continu Hadoop communique avec le mappeur et le raccord de réduction via STDIN et STDOUT. Le mappeur et le raccord de réduction lisent les données ligne par ligne depuis STDIN et écrivent la sortie dans STDOUT. Chaque ligne lue ou émise par le mappeur et le raccord de réduction doit être au format d’une paire clé / valeur, délimitée par un caractère de tabulation :

    [key]/t[value]

Pour plus d’informations, consultez [Diffusion en continu Hadoop](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Pour obtenir des exemples d’utilisation de diffusion en continu Hadoop avec HDInsight, consultez le document suivant :

* [Développement de tâches MapReduce C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cluster Apache Hadoop dans HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
