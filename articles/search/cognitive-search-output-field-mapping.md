---
title: Mapper des champs d’entrée enrichis avec la recherche cognitive sur des champs de sortie - Recherche Azure
description: Extrayez et enrichissez des champs de données sources, puis mappez-les à des champs de sortie dans un index Recherche Azure.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 0e422d2453fe12280da9e9b0b5dc7aa391f97b9f
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186405"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Guide pratique pour mapper des champs enrichis sur un index pouvant faire l’objet d’une recherche

Dans cet article, vous allez apprendre à mapper des champs d’entrée enrichis sur des champs de sortie dans un index pouvant faire l’objet d’une recherche. Une fois que vous avez [défini un ensemble de compétences](cognitive-search-defining-skillset.md), vous devez mapper les champs de sortie de n’importe quelle compétence qui fournit directement des valeurs sur un champ donné dans votre index de recherche. Les mappages de champs sont requis pour le déplacement du contenu depuis les documents enrichis vers l’index.


## <a name="use-outputfieldmappings"></a>Utiliser outputFieldMappings
Pour mapper les champs, ajoutez `outputFieldMappings` à la définition de l’indexeur comme indiqué ci-dessous :

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Le corps de la demande est structuré comme suit :

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Pour chaque mappage de champ de sortie, définissez le nom du champ enrichi (sourceFieldName) et le nom du champ tel que référencé dans l’index (targetFieldName).

Le chemin dans un sourceFieldName peut représenter un élément ou plusieurs éléments. Dans l’exemple ci-dessus, ```/document/content/sentiment``` représente une valeur numérique unique, tandis que ```/document/content/organizations/*/description``` représente plusieurs descriptions d’organisation. Dans les cas où il existe plusieurs éléments, ceux-ci sont « aplatis » en un tableau qui contient chacun des éléments. Plus concrètement, si nous considérons l’exemple ```/document/content/organizations/*/description```, les données du champ *descriptions* ressembleraient à un tableau de descriptions aplati avant d’être indexées :

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez mappé les champs enrichis sur des champs pouvant faire l’objet d’une recherche, vous pouvez définir les attributs de chacun des champs pouvant faire l’objet d’une recherche [dans le cadre de la définition de l’index](search-what-is-an-index.md).

Pour en savoir plus sur le mappage de champs, consultez [Mappages de champs dans les indexeurs Recherche Azure](search-indexer-field-mappings.md).
