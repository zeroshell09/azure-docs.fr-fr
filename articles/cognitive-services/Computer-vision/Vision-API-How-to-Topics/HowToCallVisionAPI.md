---
title: 'Exemple : Appeler l’API d’analyse d’image - Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Découvrez comment appeler l’API Vision par ordinateur à l’aide de REST dans Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 97b9e0defb3f349a6e202572bc0e3005d5d87e9c
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141200"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exemple : Appeler l’API Vision par ordinateur

Ce guide montre comment appeler l’API Vision par ordinateur à l’aide de REST. Les exemples sont écrits en C# avec la bibliothèque de client d’API Vision par ordinateur et en tant qu’appels HTTP POST/GET. Nous expliquerons en particulier :

- Comment obtenir les étiquettes, la description et les catégories.
- Comment obtenir les informations d’un domaine spécifique (« celebrities », dans notre cas).

## <a name="prerequisites"></a>Prérequis

- URL d’image ou chemin de l’image stockée localement.
- Méthodes d’entrée prises en charge : image Raw binaire sous forme de flux application/octet ou d’URL d’image
- Formats d’image pris en charge : JPEG, PNG, GIF, BMP
- Taille du fichier image : moins de 4 Mo
- Dimensions de l’image : plus de 50 x 50 pixels
  
Les exemples ci-dessous illustrent les fonctionnalités suivantes :

1. Analyse d’une image et obtention en retour d’un tableau d’étiquettes et d’une description.
2. Analyse d’une image avec un modèle spécifique à un domaine (ici, le modèle « celebrities ») et obtention du résultat correspondant au format JSON.

Les fonctionnalités sont présentées en deux options :

- **Option 1 :** Analyse délimitée - Analyser uniquement un modèle donné
- **Option 2 :** Analyse élargie - Analyser avec la [taxonomie des 86 catégories](../Category-Taxonomy.md) pour fournir des détails supplémentaires
  
## <a name="authorize-the-api-call"></a>Autoriser l’appel d’API

Chaque appel de l’API Vision par ordinateur nécessite une clé d’abonnement. Cette clé doit être transmise par le biais d’un paramètre de chaîne de requête ou spécifiée dans l’en-tête de requête.

Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé.

1. La clé d’abonnement est passée par le biais d’une chaîne de requête, comme dans l’exemple suivant d’API Vision par ordinateur :

    ```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. La clé d’abonnement peut également être passée en la spécifiant dans l’en-tête de requête HTTP :

    ```ocp-apim-subscription-key: <Your subscription key>```

1. Quand vous utilisez la bibliothèque de client, la clé d’abonnement est passée par le biais du constructeur de VisionServiceClient :

    ```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Charger une image dans le service API Vision par ordinateur et obtenir en retour les étiquettes, les descriptions et les noms des célébrités

La façon la plus simple d’effectuer l’appel d’API Vision par ordinateur est en chargeant une image directement. Cette méthode consiste à envoyer une requête « POST » avec le type de contenu application/octet-stream en même temps que les données lues à partir de l’image. Pour les étiquettes et la description, cette méthode de chargement est la même pour tous les appels d’API Vision par ordinateur. La seule différence concerne les paramètres de requête spécifiés par l’utilisateur. 

Voici comment obtenir les étiquettes (« Tags ») et la « Description » d’une image :

**Option 1 :** Obtenir la liste des étiquettes et une description

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Option 2** : Obtenir uniquement la liste des étiquettes ou uniquement la description :

###### <a name="tags-only"></a>Étiquettes seulement :

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Description uniquement :

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Obtenir une analyse spécifique au domaine (célébrités)

**Option 1 :** Analyse délimitée - Analyser uniquement un modèle donné
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Avec cette option, les autres paramètres de requête {visualFeatures, details} ne sont pas valides. Pour afficher la liste des modèles pris en charge, utilisez la commande suivante :

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Option 2 :** Analyse élargie - Analyser avec la [taxonomie des 86 catégories](../Category-Taxonomy.md) pour fournir des détails supplémentaires

Dans les applications où vous souhaitez obtenir une analyse d’image générique en plus des détails issus d’un ou de plusieurs modèles spécifiques à un domaine, nous étendons l’API v1 avec le paramètre de requête « models ».

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

Quand cette méthode est appelée, nous devons d’abord appeler le classifieur de 86 catégories. Si aucune des catégories ne correspond à celle d’un modèles connu ou correspondant, une deuxième session d’appels du classifieur est effectuée. Par exemple, si nous avons « details=all » ou si « details » inclut « celebrities », nous devons appeler le modèle celebrities après l’appel du classifieur de 86 catégories et l’obtention du résultat incluant la personne de la catégorie. Cela augmente la latence pour les utilisateurs intéressés par les célébrités, par rapport à l’option 1.

Tous les paramètres de requête v1 ont le même comportement dans ce cas.  Si « visualFeatures=categories » n’est pas spécifié, il est implicitement activé.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Récupérer et comprendre la sortie JSON pour l’analyse

Voici un exemple :

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Champ | Type | Contenu
------|------|------|
Balises  | `object` | Objet de premier niveau pour le tableau d’étiquettes
tags[].Name | `string`  | Mot clé d’un classifieur d’étiquettes
tags[].Score    | `number`  | Score de confiance, compris entre 0 et 1.
description  | `object` | Objet de premier niveau pour une description.
description.tags[] |    `string`    | Liste d’étiquettes.  Si le score de confiance est insuffisant pour pouvoir générer une légende, les étiquettes peuvent être les seules informations disponibles pour l’appelant.
description.captions[].text | `string`  | Expression décrivant l’image.
description.captions[].confidence   | `number`  | Score de confiance de l’expression.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Récupérer et comprendre la sortie JSON des modèles spécifiques au domaine

**Option 1 :** Analyse délimitée - Analyser uniquement un modèle donné

La sortie est un tableau d’étiquettes, similaire à l’exemple suivant :

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

**Option 2 :** Analyse élargie - Analyser avec la taxonomie des 86 catégories pour fournir des détails supplémentaires

Pour les modèles spécifiques au domaine qui utilisent l’option 2 (Analyse élargie), le type de retour des catégories est étendu. Exemple :

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Le champ « categories » est une liste d’une ou plusieurs catégories parmi les [86 catégories](../Category-Taxonomy.md) de la taxonomie d’origine. Notez également que chaque catégorie qui se termine par un trait de soulignement correspond à cette catégorie et à ses enfants (par exemple, people_ et people_group pour le modèle celebrities).

Champ   | Type  | Contenu
------|------|------|
categories | `object`   | Objet de premier niveau
categories[].name    | `string` | Nom issu de la taxonomie des 86 catégories
categories[].score  | `number`  | Score de confiance, compris entre 0 et 1
categories[].detail  | `object?`      | Objet de détail facultatif

Notez que si plusieurs catégories correspondent (par exemple, le classifieur de 86 catégories retourne un score pour people_ et people_young avec le modèle celebrities), les détails s’appliquent au niveau de correspondance le plus général (people_ dans cet exemple).

## <a name="errors-responses"></a>Réponses d’erreurs

Ces réponses sont identiques à celles de vision.analyze, avec en plus l’erreur NotSupportedModel (HTTP 400), qui peut être retournée dans les deux scénarios Option 1 et Option 2. Dans le scénario Option 2 (Analyse élargie), si un des modèles spécifiés dans les détails n’est pas reconnu, l’API retourne une erreur NotSupportedModel, même si un ou plusieurs des autres modèles sont valides.  Les utilisateurs peuvent appeler listModels pour savoir quels modèles sont pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser l’API REST, consultez les [Informations de référence sur l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
