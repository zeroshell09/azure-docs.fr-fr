---
title: Vérifier l’instance de conteneur Détection de langue
titleSuffix: Azure Cognitive Services
description: Découvrez comment vérifier l’instance de conteneur Détection de langue.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 74d95d70438431495c9072e115854b1ccfa05bcb
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051551"
---
## <a name="verify-the-language-detection-container-instance"></a>Vérifier l’instance de conteneur Détection de langue

1. Sélectionnez l’onglet **Vue d’ensemble** et copiez l’adresse IP.
1. Ouvrez un nouvel onglet de navigateur et entrez l’adresse IP. Par exemple, entrez `http://<IP-address>:5000 (http://55.55.55.55:5000`. La page d’accueil du conteneur s’affiche, vous informant que le conteneur est en cours d’exécution.

    ![Affichez la page d’accueil du conteneur pour vérifier qu’elle est en cours d’exécution](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Veuillez sélectionner le lien **Description de l’API de service** pour accéder à la page Swagger des conteneurs.

1. Choisissez une des API **POST** et sélectionnez **Essayer**. Les paramètres sont affichés, y compris cet exemple d’entrée :

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Définissez **showStats** sur `true`.

1. Sélectionnez **Exécuter** pour déterminer le sentiment du texte.

    Le modèle empaqueté dans le conteneur génère un score compris entre 0 et 1, où 0 est un sentiment négatif et 1 est un sentiment positif.

    La réponse JSON retournée inclut le sentiment pour l’entrée de texte mise à jour :

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Nous pouvons maintenant corréler le document des données JSON de la charge utile de réponse avec les documents de la charge utile de la requête d’origine avec leur `id` correspondant. Chaque document est traité indépendamment et contient différentes statistiques telles que `characterCount` et `transactionCount`. De plus, chaque document résultant possède le tableau `detectedLanguages` avec les `name`, `iso6391Name` et `score` pour chaque langue détectée. Lorsque plusieurs langues sont détectées, `score` est utilisé pour déterminer la langue la plus probable.