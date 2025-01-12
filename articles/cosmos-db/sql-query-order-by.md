---
title: Clause ORDER BY dans Azure Cosmos DB
description: Découvrez la clause SQL ORDER BY pour Azure Cosmos DB. Utilisez SQL comme langage de requête JSON Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343153"
---
# <a name="order-by-clause"></a>Clause ORDER BY

La clause ORDER BY facultative spécifie l’ordre de tri des résultats retournés par la requête.

## <a name="syntax"></a>Syntaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Arguments
  
- `<sort_specification>`  
  
   Spécifie une propriété ou expression sur laquelle trier le jeu de résultats de requête. Une colonne de tri peut être spécifiée en tant qu’alias de nom ou de propriété.  
  
   Plusieurs propriétés peuvent être spécifiées. Les noms de propriété doivent être uniques. La séquence des propriétés de tri dans la clause ORDER BY détermine l’organisation du jeu de résultats trié. Autrement dit, le jeu de résultats est trié par la première propriété, puis cette liste triée est triée par la deuxième propriété, et ainsi de suite.  
  
   Les noms de propriétés référencés dans la clause ORDER BY doivent correspondre à une propriété dans la liste de sélection ou à une propriété définie dans la collection spécifiée dans la clause FROM sans ambiguïté.  
  
- `<sort_expression>`  
  
   Spécifie une ou plusieurs propriétés ou expressions sur lesquelles trier le jeu de résultats de requête.  
  
- `<scalar_expression>`  
  
   Consultez la section [Expressions scalaires](sql-query-scalar-expressions.md) pour plus d’informations.  
  
- `ASC | DESC`  
  
   Spécifie que les valeurs dans la colonne spécifiée doivent être triées dans l’ordre croissant ou décroissant. ASC effectue le tri de la valeur la plus basse à la valeur la plus élevée. DESC effectue le tri de la valeur la plus élevée à la valeur la plus basse. ASC est l’ordre de tri par défaut. Les valeurs NULL sont traitées comme les valeurs les plus basses possible.  
  
## <a name="remarks"></a>Remarques  
  
   La clause ORDER BY nécessite que la stratégie d’indexation comprenne un index pour les champs de tri. Le runtime de requête Azure Cosmos DB prend en charge le tri par rapport à un nom de propriété et non par rapport à des propriétés calculées. Azure Cosmos DB prend en charge plusieurs propriétés ORDER BY. Pour exécuter une requête avec plusieurs propriétés ORDER BY, vous devez définir un [index composite](index-policy.md#composite-indexes) sur les champs de tri.

## <a name="examples"></a>Exemples

Par exemple, voici une requête qui récupère les familles dans l’ordre croissant de la ville de résidence :

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Les résultats sont :

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

La requête suivante récupère les `id` de famille dans l’ordre de la date de création de leur élément. L’élément `creationDate` est un nombre représentant l’*heure d’époque*, ou le temps écoulé depuis le 1er janvier 1970 en secondes.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Les résultats sont :

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

De plus, vous pouvez trier par plusieurs propriétés. Une requête qui trie par plusieurs propriétés requiert un [index composite](index-policy.md#composite-indexes). Examinez la requête suivante :

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Cette requête récupère les `id` de famille dans l’ordre croissant du nom de la ville. Si plusieurs éléments ont le même nom de ville, la requête trie par la `creationDate` dans l’ordre décroissant.

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main](sql-query-getting-started.md)
- [Clause SELECT](sql-query-select.md)
- [Clause OFFSET LIMIT](sql-query-offset-limit.md)
