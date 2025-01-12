---
title: Utilisation d’Akka Streams pour Apache Kafka - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur la connexion d’Akka Streams à un hub d’événements Apache Kafka.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 32d710464cf61f998e18af28887561cefd2b1b3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821553"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Utilisation d’Akka Streams avec Event Hubs pour Apache Kafka
Ce tutoriel vous montre comment connecter Akka Streams à des hubs d’évenements prenant en charge Kafka sans modifier vos protocoles clients ni exécuter vos propres clusters. Azure Event Hubs pour Kafka prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un espace de noms Event Hubs
> * Cloner l’exemple de projet
> * Exécuter le producteur d’Akka Streams 
> * Exécuter le consommateur d’Akka Streams

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vérifiez que les conditions préalables ci-dessous sont bien remplies :

* Lisez l’article [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargé](https://maven.apache.org/download.cgi) et [installé](https://maven.apache.org/install.html) une archive binaire Maven.
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/downloads)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Un espace de noms Event Hubs est requis pour échanger avec tout service Event Hubs. Pour obtenir des instructions sur l’obtention d’un point de terminaison Kafka pour Event Hubs, voir [Création d’un Event Hub prenant en charge Kafka](event-hubs-create-kafka-enabled.md). Veillez à copier la chaîne de connexion Event Hubs pour une utilisation ultérieure.

## <a name="clone-the-example-project"></a>Cloner l’exemple de projet

Maintenant que vous disposez d’une chaîne de connexion Event Hubs prenant en charge Kafka, clonez le référentiel Azure Event Hubs pour Kafka, puis accédez au sous-dossier `akka` :

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Exécuter le producteur d’Akka Streams

À l’aide de l’exemple du producteur de flux Akka fourni, envoyez des messages au service Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fournir un point de terminaison Event Hubs Kafka

#### <a name="producer-applicationconf"></a>Producer application.conf

Mettez à jour des valeurs `bootstrap.servers` et `sasl.jaas.config` dans`producer/src/main/resources/application.conf` pour diriger le producteur vers le point de terminaison Event Hubs Kafka avec l’authentification correcte.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Exécuter le producteur depuis la ligne de commande

Pour exécuter le producteur depuis la ligne de commande, générez le fichier JAR, puis exécutez depuis Maven (ou générez le fichier JAR avec Maven, puis exécutez dans Java en ajoutant le ou les fichiers JAR Kafka au paramètre classpath) :

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

le producteur va maintenant commencer à envoyer des événement à l’Event Hub prenant en charge Kafka au niveau de la rubrique `test` et imprimer les événements dans stdout.

## <a name="run-akka-streams-consumer"></a>Exécuter le consommateur d’Akka Streams

À l’aide de l’exemple de contrôle serveur consommateur fourni, recevez des messages à partir des Event Hubs prenant en charge Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fournir un point de terminaison Event Hubs Kafka

#### <a name="consumer-applicationconf"></a>Consumer application.conf

Mettez à jour des valeurs `bootstrap.servers` et `sasl.jaas.config` dans`consumer/src/main/resources/application.conf` pour diriger le contrôle serveur consommateur vers le point de terminaison Event Hubs Kafka avec l’authentification correcte.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Exécuter le contrôle serveur consommateur depuis la ligne de commande

Pour exécuter le contrôle serveur consommateur depuis la ligne de commande, générez le fichier JAR, puis exécutez depuis Maven (ou générez le fichier JAR avec Maven, puis exécutez dans Java en ajoutant le ou les fichiers JAR Kafka au paramètre classpath) :

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Si l’Event Hub prenant en charge Kafka a des événements (par exemple, si votre producteur est également en cours d’exécution), le contrôle serveur consommateur commence maintenant à recevoir les événements provenant de la rubrique `test`. 

Consultez le [Guide Kafka de flux Akka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) pour plus d’informations sur les flux Akka.

## <a name="next-steps"></a>Étapes suivantes
Dans le cadre de ce didacticiel, vous avez appris à connecter Akka Streams à des Event Hubs prenant en charge Kafka sans modifier vos protocoles clients ni exécuter vos propres clusters. Azure Event Hubs pour Kafka prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html). Dans ce didacticiel, vous avez effectué les opérations suivantes : 

> [!div class="checklist"]
> * Créer un espace de noms Event Hubs
> * Cloner l’exemple de projet
> * Exécuter le producteur d’Akka Streams 
> * Exécuter le consommateur d’Akka Streams

Pour plus d’informations sur Event Hubs et sur Event Hubs pour Kafka, consultez les articles suivants :  

- [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Créer un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create-kafka-enabled.md)
- [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mettre en miroir un répartiteur Kafka dans un Event Hub prenant en charge Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connecter Apache Spark à un Event Hub prenant en charge Kafka](event-hubs-kafka-spark-tutorial.md)
- [Connecter Apache Flink à un Event Hub prenant en charge Kafka](event-hubs-kafka-flink-tutorial.md)
- [Intégrer Kafka Connect à un Event Hub prenant en charge Kafka](event-hubs-kafka-connect-tutorial.md)
- [Explorer des exemples sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
