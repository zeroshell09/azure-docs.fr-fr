---
title: Adresses IP du service Gestion des API Azure | Microsoft Docs
description: Apprenez à récupérer les adresses IP d'un service Gestion des API Azure et leurs modifications.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 8d7346bb61fad09e3f7c9098809463285ef57e93
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242482"
---
# <a name="ip-addresses-of-azure-api-management"></a>Adresses IP du service Gestion des API Azure

Cet article explique comment récupérer les adresses IP d'un service Gestion des API Azure. Les adresses IP peuvent être publiques ou privées si le service se trouve dans un réseau virtuel.

Vous pouvez utiliser des adresses IP pour créer des règles de pare-feu, filtrer le trafic entrant à destination des services principaux, ou limiter le trafic sortant.

## <a name="ip-addresses-of-api-management-service"></a>Adresses IP du service Gestion des API

Chaque instance du service Gestion des API dans les niveaux Développeur, De base, Standard ou Premium a des adresses IP publiques propres cette instance de service (elles ne sont pas partagées avec d’autres ressources). 

Vous pouvez récupérer les adresses IP à partir du tableau de bord de vue d’ensemble de votre ressource dans le portail Azure.

![Adresse IP du service Gestion des API](media/api-management-howto-ip-addresses/public-ip.png)

Vous pouvez également les récupérer par programmation à l'aide de l'appel d'API suivant :

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Les adresses IP publiques seront contenues dans la réponse :

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Dans les [déploiements multirégionaux](api-management-howto-deploy-multi-region.md), chaque déploiement régional possède une adresse IP publique.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Adresses IP du service Gestion des API dans un réseau virtuel

Si votre service Gestion des API se trouve dans un réseau virtuel, il dispose de deux types d'adresses IP : publiques et privées.

Les adresses IP publiques sont utilisées pour les communications internes sur le port `3443` - afin de gérer la configuration (par exemple, via Azure Resource Manager). En outre, lorsqu'une requête est envoyée du service Gestion des API vers un serveur principal public (Internet), une adresse IP publique est présentée comme origine de la requête.

Les adresses IP virtuelles privées sont utilisées pour se connecter aux points de terminaison (passerelles, portail des développeurs et plan de gestion pour un accès direct à l'API) du service Gestion des API depuis le réseau. Vous pouvez les utiliser pour configurer des enregistrements DNS au sein du réseau.

Vous verrez des adresses des deux types sur le portail Azure et dans la réponse de l'appel d'API :

![Gestion des API dans une adresse IP de réseau virtuel](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Adresses IP d'un service Gestion des API de niveau Consommation

Si votre service Gestion des API est un service de niveau Consommation, il ne possède pas d'adresse IP dédiée. Le service de niveau Consommation est exécuté sur une infrastructure partagée et sans adresse IP déterministe. 

Pour limiter le trafic, vous pouvez utiliser la plage d'adresses IP des centres de données Azure. Pour connaître la procédure à suivre, reportez-vous à l'[article de la documentation consacré à Azure Functions](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses).

## <a name="changes-to-the-ip-addresses"></a>Modification des adresses IP

Aux niveaux Développeur, De base, Standard et Premium d'un service Gestion des API, les adresses IP publiques sont statiques pendant toute la durée de vie du service, avec les exceptions suivantes :

* Le service est supprimé, puis recréé.
* L’abonnement au service est [suspendu](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [fait l’objet d’un avertissement ](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)(par exemple pour non-paiement), puis réactivé.
* Le Réseau virtuel Azure est ajouté au service ou supprimé de celui-ci.

Dans les [déploiements multirégionaux](api-management-howto-deploy-multi-region.md), l'adresse IP régionale change si une région est libérée, puis rétablie.
