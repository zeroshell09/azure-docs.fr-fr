---
title: 'CLI Azure Service Fabric : sfctl mesh volume | Microsoft Docs'
description: Décrit les commandes sfctl mesh volume de l'interface de ligne de commande CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 4e2413a152048e08d6218783891c20b66ac2e618
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035000"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Obtenir et supprimer des ressources de volume.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime une ressource de volume. |
| list | Répertorie toutes les ressources de volume. |
| show | Récupère la ressource de volume portant le nom spécifié. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Supprime une ressource de volume.

Supprime la ressource de volume identifiée par le nom.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Nom du volume. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Répertorie toutes les ressources de volume.

Récupère les informations relatives à toutes les ressources de volume d'un groupe de ressources donné. Les informations incluent la description et d'autres propriétés du volume.

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Récupère la ressource de volume portant le nom spécifié.

Récupère les informations relatives à la ressource de volume portant le nom spécifié. Les informations incluent la description et d'autres propriétés du volume.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name -n [obligatoire] | Nom du volume. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).