---
title: MapReduce avec Apache Hadoop sur HDInsight
description: Apprenez à exécuter des tâches MapReduce sur Apache Hadoop dans des clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: a1bb7a6737115f903391997a5430c32f9a40465f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207113"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Utiliser MapReduce dans Apache Hadoop sur HDInsight

Apprenez à exécuter des tâches MapReduce sur des clusters HDInsight.

## <a id="data"></a>Exemple de données

HDInsight propose différents exemples de jeux de données, qui sont stockés dans les répertoires `/example/data` et `/HdiSamples`. Ces répertoires sont disponibles dans le stockage par défaut de votre cluster. Dans ce document, nous utilisons le fichier `/example/data/gutenberg/davinci.txt`. Ce fichier contient les carnets de Léonard De Vinci.

## <a id="job"></a>Exemple MapReduce

Un exemple d’application de comptage de mots MapReduce est inclus dans votre cluster HDInsight. Cet exemple se trouve dans `/example/jars/hadoop-mapreduce-examples.jar` sur le stockage par défaut pour votre cluster.

Le code Java suivant est la source de l’application MapReduce contenue dans le fichier `hadoop-mapreduce-examples.jar` :

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Pour savoir comment écrire vos propres applications MapReduce, consultez le document suivant :

* [Développement d’applications MapReduce en Java pour HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Exécution de la tâche MapReduce

HDInsight peut exécuter des tâches HiveQL de différentes manières. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Élément à utiliser...** | **...pour faire cela** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Utilisation de la commande Hadoop via **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Envoyer la tâche à distance à l'aide de **REST** |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Envoyer la tâche à distance à l'aide de **Windows PowerShell** |Linux ou Windows |Windows |

## <a id="nextsteps"></a>Étapes suivantes

Pour savoir comment utiliser les données dans HDInsight, consultez les documents suivants :

* [Développement de programmes MapReduce en Java pour HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Utilisation d’Apache Hive avec HDInsight][hdinsight-use-hive]

* [Utilisation d’Apache Pig avec HDInsight][hdinsight-use-pig]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs
