---
title: 'CLI Azure Service Fabric : sfctl mesh service-replica | Microsoft Docs'
description: Décrit les commandes sfctl mesh service-replica de l’interface de ligne de commande (CLI) Service Fabric.
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
ms.openlocfilehash: 6819bb32eecf8477e2c0727b50641858db21c784
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035908"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Obtenir les détails du réplica et répertorier les réplicas d’un service donné dans une ressource d’application.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| list | Répertorie tous les réplicas d’un service. |
| show | Permet d’obtenir le réplica donné du service d’une application. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
Répertorie tous les réplicas d’un service.

Permet d’obtenir des informations sur tous les réplicas d’un service. Les informations incluent la description et d’autres propriétés du réplica du service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name --application-name [obligatoire] | Le nom de l’application. |
| --service-name                [obligatoire] | Nom du service. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Permet d’obtenir le réplica donné du service d’une application.

Récupère les informations relatives au réplica du service portant le nom spécifié. Les informations incluent la description et d’autres propriétés du réplica du service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name --application-name [obligatoire] | Le nom de l’application. |
| --name -n                     [obligatoire] | Le nom du réplica du service. |
| --service-name                [obligatoire] | Nom du service. |

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