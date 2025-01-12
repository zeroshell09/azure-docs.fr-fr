---
title: 'Chargement en bloc dans Apache Phoenix à l’aide de psql : Azure HDInsight'
description: Utilisez l’outil psql pour charger des données de chargement en bloc dans des tables Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: fe6705dc3dedd521357f924dd433c7eacf88ba84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718628"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Charger des données en bloc dans Apache Phoenix à l’aide de psql

[Apache Phoenix](https://phoenix.apache.org/) est une couche de base de données relationnelle massivement parallèle open source basée sur [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix fournit des requêtes de type SQL sur HBase. Phoenix utilise des pilotes JDBC pour permettre aux utilisateurs de créer, supprimer et modifier des tables SQL, index, vues et séquences, ainsi que des lignes d’upsert individuellement et en bloc. Phoenix utilise une compilation native noSQL au lieu de MapReduce pour compiler les requêtes, pour créer des applications à faible latence sur HBase. Phoenix ajoute des co-processeurs pour prendre en charge le code fourni par le client en cours d’exécution dans l’espace d’adressage du serveur, en exécutant le code colocalisé avec les données. Cela réduit le transfert de données client/serveur.  Pour utiliser des données à l’aide de Phoenix dans HDInsight, créez tout d’abord des tables, puis chargez des données dans celles-ci.

## <a name="bulk-loading-with-apache-phoenix"></a>Chargement en masse avec Apache Phoenix

Plusieurs méthodes permettent d’obtenir des données dans HBase, notamment à l’aide d’API clientes, une tâche MapReduce avec TableOutputFormat ou en entrant les données manuellement à l’aide du shell HBase. Phoenix propose deux méthodes pour charger des données CSV dans des tables Phoenix : un outil de chargement client nommé `psql`et un outil de chargement en bloc basé sur MapReduce.

L’outil `psql` est monothread et parfaitement adapté au chargement de mégaoctets ou de gigaoctets de données. Tous les fichiers CSV à charger doivent avoir l’extension de fichier « .csv ».  Vous pouvez également spécifier des fichiers de script SQL dans la ligne de commande `psql` avec l’extension de fichier « .sql ».

Le chargement en bloc avec MapReduce est utilisé pour des volumes plus importants de données, généralement dans des scénarios de production, car MapReduce utilise plusieurs threads.

Avant de commencer à charger des données, vérifiez que Phoenix est activé et que les paramètres de délai d’expiration de requête sont tel que prévu.  Accédez au tableau de bord [Apache Ambari](https://ambari.apache.org/) de votre cluster HDInsight, sélectionnez HBase, puis sélectionnez l’onglet Configuration.  Faites défiler vers le bas pour vérifier si Apache Phoenix est défini sur `enabled` comme illustré :

![Paramètres de cluster HDInsight Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Utiliser `psql` pour charger des tables en bloc

1. Créez une nouvelle table, puis enregistrez votre requête avec le nom de fichier `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Copiez votre fichier CSV (exemple de contenu illustré) en tant que `customers.csv` dans un répertoire `/tmp/` pour charger dans votre nouvelle table.  Utilisez la commande `hdfs` pour copier le fichier CSV à l’emplacement source de votre choix.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Créez une requête SQL SELECT pour vérifier si les données d’entrée ont été correctement chargées, puis enregistrez votre requête avec le nom de fichier `listCustomers.sql`. Vous pouvez utiliser n’importe quelle requête SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Chargez les données en bloc en ouvrant une *nouvelle* fenêtre de commande Hadoop. Modifiez tout d’abord l’emplacement du répertoire d’exécution avec la commande `cd`, puis utilisez l’outil `psql` (commande Python `psql.py`). 

    L’exemple suivant implique que vous avez copié le fichier `customers.csv` d’un compte de stockage dans votre répertoire temporaire local à l’aide de `hdfs` comme à l’étape 2 ci-dessus.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Pour déterminer le nom `ZookeeperQuorum`, recherchez la chaîne de quorum [Apache ZooKeeper](https://zookeeper.apache.org/) dans le fichier `/etc/hbase/conf/hbase-site.xml` avec le nom de propriété `hbase.zookeeper.quorum`.

5. Lorsque l’opération `psql` est terminée, vous devez voir un message dans votre fenêtre de commande :

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Utiliser MapReduce pour charger des tables en bloc

Pour un chargement à un débit supérieur distribué sur le cluster, utilisez l’outil de chargement MapReduce. Ce chargeur convertit d’abord toutes les données dans HFiles, puis fournit le HFiles créé à HBase.

1. Lancez le chargeur CSV MapReduce à l’aide de la commande `hadoop` avec le jar client Phoenix :

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Créez une nouvelle table avec une instruction SQL, comme avec `CreateCustomersTable.sql` dans l’étape 1 précédente.

3. Pour vérifier le schéma de votre table, exécutez `!describe inputTable`.

4. Déterminez le chemin d’accès à l’emplacement de vos données d’entrée, comme dans l’exemple de fichier `customers.csv`. Les fichiers d’entrée peuvent se trouver dans votre compte de stockage WASB/ADLS. Dans cet exemple de scénario, les fichiers d’entrée se trouvent dans le répertoire `<storage account parent>/inputFolderBulkLoad`.

5. Accédez au répertoire d’exécution de la commande de chargement en bloc MapReduce :

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Recherchez votre valeur `ZookeeperQuorum` dans `/etc/hbase/conf/hbase-site.xml`, avec le nom de propriété `hbase.zookeeper.quorum`.

7. Configurez le chemin de classe et exécutez la commande d’outil `CsvBulkLoadTool` :

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Pour utiliser MapReduce avec Azure Data Lake Storage, recherchez le répertoire racine Data Lake Storage, qui correspond à la valeur `hbase.rootdir` dans `hbase-site.xml`. Dans la commande suivante, le répertoire racine Data Lake Storage est `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Dans cette commande, spécifiez les dossiers d’entrée et de sortie Data Lake Storage en tant que paramètres :

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Recommandations

* Utilisez le même support de stockage pour les dossiers d’entrée et de sortie, Stockage Azure (WASB) ou Azure Data Lake Storage (ADL). Pour transférer des données entre Stockage Azure et Data Lake Storage, utilisez la commande `distcp` :

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Utilisez des nœuds de travail de plus grande taille. Les processus de mappage de la copie en bloc MapReduce produisent de grandes quantités de sortie temporaire qui saturent l’espace non DFS disponible. Pour une grande quantité de chargement en bloc, utilisez un plus grand nombre de nœuds de travail et de plus grande taille. Le nombre de nœuds de travail que vous allouez à votre cluster affecte directement la vitesse de traitement.

* Fractionnez les fichiers d’entrée en segments de 10 Go environ. Le chargement en bloc est une opération grande consommatrice du stockage, le fractionnement de vos fichiers d’entrée en plusieurs segments offre donc de meilleures performances.

* Évitez les zones réactives du serveur de la région. Si votre clé de ligne augmente de manière monolithique, les écritures séquentielles HBase peuvent entraîner un « hotspotting » du serveur de la région. Un *Salting* de la clé de ligne réduit les écritures séquentielles. Phoenix fournit une méthode transparente de « salting » de la clé de ligne avec un octet de « salting » pour une table particulière, comme indiqué ci-dessous.

## <a name="next-steps"></a>Étapes suivantes

* [Chargement de données en bloc avec Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Utiliser Apache Phoenix avec les clusters Apache HBase basés sur Linux dans HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Tables « salted »](https://phoenix.apache.org/salted.html)
* [Grammaire Apache Phoenix](https://phoenix.apache.org/language/index.html)
