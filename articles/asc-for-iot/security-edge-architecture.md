---
title: Comprendre le module de sécurité Azure Security Center pour l’IoT pour IoT Edge (préversion) | Microsoft Docs
description: Découvrez l’architecture et les fonctionnalités du module de sécurité Azure Security Center pour l’IoT pour IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596861"
---
# <a name="azure-iot-edge-security-module"></a>Module de sécurité Azure IoT Edge

> [!IMPORTANT]
> Le service Azure Security Center pour IoT pour IoT Edge est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) offre de puissantes fonctionnalités de gestion et d’exécution de flux de travail à la périphérie.
Le rôle clé d’IoT Edge dans les environnements IoT le rend particulièrement séduisant pour les personnes malveillantes.

Le module de sécurité Azure Security Center pour IoT constitue une solution de sécurité complète pour les appareils IoT Edge.
Le module Azure Security Center pour IoT collecte, agrège et analyse des données de sécurité brutes tirées du système d’exploitation et du système de conteneur pour produire des alertes et des recommandations de sécurité actionnables.

Tout comme les agents de sécurité Azure Security Center pour IoT pour les appareils IoT, le module Azure Security Center pour IoT Edge est hautement personnalisable grâce à son jumeau de module.
Pour plus d’informations, voir [Configurer un agent](how-to-agent-configuration.md).

Le module de sécurité Azure Security Center pour IoT pour IoT Edge offre les fonctionnalités suivantes :

- Collecte d’événements de sécurité bruts auprès du système d’exploitation sous-jacent (Linux) et des systèmes de conteneurs IoT Edge.
  
  Pour en savoir plus sur les collecteurs de données de sécurité disponibles, voir [Configuration de l’agent Azure Security Center pour IoT](how-to-agent-configuration.md).

- Analyse des manifestes de déploiement IoT Edge.

- Agrégation des événements de sécurité bruts dans des messages envoyés par un [hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Configuration à distance grâce au jumeau de module de sécurité.

  Pour en savoir plus, voir [Configurer un agent Azure Security Center pour IoT](how-to-agent-configuration.md).

Le module de sécurité Azure Security Center pour IoT pour IoT Edge s’exécute en mode privilégié sous IoT Edge.
Ce mode est nécessaire pour l’autoriser à effectuer un monitoring du système d’exploitation et d’autres modules IoT Edge.

## <a name="module-supported-platforms"></a>Plateformes prises en charge du module

Le module de sécurité Azure Security Center pour IoT pour IoT Edge n’est actuellement disponible que pour Linux. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert l’architecture et les fonctionnalités du module de sécurité Azure Security Center pour IoT pour IoT Edge.

Pour continuer votre prise en main du déploiement d’Azure Security Center pour IoT, lisez les articles suivants :

- Déployez le [module de sécurité pour IoT Edge](how-to-deploy-edge.md)
- Découvrez comment [configurer votre module de sécurité](how-to-agent-configuration.md)
- Révisez les [composants requis pour le service](service-prerequisites.md) Azure Security Center pour IoT
- Découvrez comment [activer le service Azure Security Center pour IoT dans votre hub IoT](quickstart-onboard-iot-hub.md).
- Trouvez des informations sur le service dans la [FAQ Azure Security Center pour IoT](resources-frequently-asked-questions.md)