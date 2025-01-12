---
title: Utiliser Apache Kafka MirrorMaker - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur l’utilisation de Kafka MirrorMaker pour mettre en miroir un cluster Kafka dans Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: a7271eb6b8cbc8a117b5a8e75edfe02985ec3452
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821532"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Utiliser Kafka MirrorMaker avec Event Hubs pour Apache Kafka

Ce didacticiel indique comment mettre en miroir un répartiteur Kafka dans un Event Hub compatible avec Kafka à l’aide de Kafka MirrorMaker.

   ![Kafka MirrorMaker avec Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).


Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un espace de noms Event Hubs
> * Cloner l’exemple de projet
> * Configurer un cluster Kafka
> * Configurer Kafka MirrorMaker
> * Exécuter Kafka MirrorMaker

## <a name="introduction"></a>Introduction
Une considération majeure pour des applications à l’échelle du cloud moderne est la possibilité de mettre à jour, d’améliorer et de modifier une infrastructure sans interrompre le service. Ce didacticiel montre comment un hub d’événements compatible avec Kafka et Kafka MirrorMaker peut intégrer un pipeline Kafka existant dans Azure en « mettant en miroir » le flux d’entrée Kafka dans le service Event Hubs. 

Un point de terminaison Kafka Azure Event Hubs vous permet de vous connecter à des Azure Event Hubs (c’est-à-dire, des clients Kafka) en utilisant le protocole Kafka. En apportant des modifications minimales à une application Kafka, vous pouvez vous connecter à Azure Event Hubs et profiter des avantages de l’écosystème Azure. Les Event Hubs compatibles avec Kafka prennent actuellement en charge les versions 1.0 et ultérieures de Kafka.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants :

* Lisez l’article [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargé](https://maven.apache.org/download.cgi) et [installé](https://maven.apache.org/install.html) une archive binaire Maven.
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/downloads)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Un espace de noms Event Hubs est requis pour échanger avec tout service Event Hubs. Pour obtenir des instructions sur l’obtention d’un point de terminaison Kafka pour Event Hubs, voir [Création d’un Event Hub compatible avec Kafka](event-hubs-create.md). Veillez à copier la chaîne de connexion Event Hubs pour une utilisation ultérieure.

## <a name="clone-the-example-project"></a>Cloner l’exemple de projet

Maintenant que vous disposez d’une chaîne de connexion Event Hubs prenant en charge Kafka, clonez le dépôt Azure Event Hubs pour Kafka, puis accédez au sous-dossier `mirror-maker` :

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurer un cluster Kafka

Utilisez le [guide de démarrage rapide de Kafka](https://kafka.apache.org/quickstart) pour configurer un cluster avec les paramètres souhaités (ou utilisez un cluster Kafka existant).

## <a name="configure-kafka-mirrormaker"></a>Configurer Kafka MirrorMaker

Kafka MirrorMaker permet la « mise en miroir » d’un flux. Étant donné des clusters Kafka source et cible, MirrorMaker garantit que tous les messages envoyés au cluster source sont reçus à la fois par les clusters source et cible. Cet exemple montre comment mettre en miroir un cluster Kafka source avec un hub d’événements de destination prenant en charge Kafka. Ce scénario peut être utilisé pour envoyer des données d’un pipeline Kafka existant à Event Hubs sans interrompre le flux de données. 

Pour plus d’informations sur Kafka MirrorMaker, voir le [Guide Mise en miroir de Kafka/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Pour configurer Kafka MirrorMaker, attribuez-lui un cluster Kafka en tant que consommateur/source, et un hub d’événements prenant en charge Kafka en tant que producteur/destination.

#### <a name="consumer-configuration"></a>Configuration du consommateur

Mettez à jour le fichier de configuration du consommateur `source-kafka.config`, qui indique à MirrorMaker les propriétés du cluster Kafka source.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuration du producteur

Maintenant, mettez à jour le fichier de configuration du producteur `mirror-eventhub.config`, qui indique à MirrorMaker d’envoyer les données dupliquées (ou « en miroir ») au service Event Hubs. Modifiez en particulier `bootstrap.servers` et `sasl.jaas.config` pour pointer vers votre point de terminaison Kafka Event Hubs. Le service Event Hubs nécessite une communication sécurisée (SASL), qui est obtenue en définissant les trois dernières propriétés dans la configuration suivante : 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Exécuter Kafka MirrorMaker

Exécutez le script Kafka MirrorMaker à partir du répertoire racine Kafka en utilisant les fichiers de configuration récemment mis à jour. Veillez à copier les fichiers de configuration dans le répertoire Kafka racine, ou à mettre à jour leurs chemins dans la commande suivante.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pour vérifier que les événements atteignent le hub d’événements prenant en charge Kafka, consultez les statistiques d’entrée sur le [portail Azure](https://azure.microsoft.com/features/azure-portal/), ou exécutez un consommateur sur le hub d’événements.

Lorsque MirrorMaker est en cours d’exécution, tous les événements envoyés au cluster Kafka source sont reçus à la fois par le cluster Kafka et par le service de hub d’événements compatible avec Kafka en miroir. En utilisant MirrorMaker et un point de terminaison Kafka Event Hubs, vous pouvez migrer un pipeline Kafka existant vers le service Azure Event Hubs géré sans modifier le cluster existant ou interrompre le flux de données en cours.

## <a name="samples"></a>Exemples
Consultez les exemples suivants sur GitHub :

- [Exemple de code pour ce tutoriel sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker exécuté sur une instance de conteneur Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un espace de noms Event Hubs
> * Cloner l’exemple de projet
> * Configurer un cluster Kafka
> * Configurer Kafka MirrorMaker
> * Exécuter Kafka MirrorMaker

Pour plus d’informations sur Event Hubs et sur Event Hubs pour Kafka, consultez les articles suivants :  

- [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Créer un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create-kafka-enabled.md)
- [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Connecter Apache Spark à un Event Hub prenant en charge Kafka](event-hubs-kafka-spark-tutorial.md)
- [Connecter Apache Flink à un Event Hub prenant en charge Kafka](event-hubs-kafka-flink-tutorial.md)
- [Intégrer Kafka Connect à un Event Hub prenant en charge Kafka](event-hubs-kafka-connect-tutorial.md)
- [Connecter Akka Streams à un Event Hub prenant en charge Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorer des exemples sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
