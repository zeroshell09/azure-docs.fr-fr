---
title: azcopy make | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9afcd8de1af42424649dd8e44fc07f7bfd881257
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196735"
---
# <a name="azcopy-make"></a>azcopy make

Créez un conteneur ou un partage de fichiers.

## <a name="synopsis"></a>Synopsis

Créez un conteneur ou un partage de fichiers représenté par l’URL de la ressource donnée.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Exemples

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Options

|Option|Description|
|--|--|
|-h, --help|Affiche l’aide de la commande make. |
|--quota-gb uint32|Spécifie la taille maximale du partage en gigaoctets (Go) ; la valeur 0 signifie que vous acceptez le quota par défaut du service de fichiers.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
