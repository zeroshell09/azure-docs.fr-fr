---
title: Informations de référence sur les API - Content Moderator
titleSuffix: Azure Cognitive Services
description: Découvrez les différentes API de modération du contenu et de révision de Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: sajagtap
ms.openlocfilehash: 3ad911a95dbe6209fcf55adcac3cf2937b06d1ff
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565620"
---
# <a name="content-moderator-api-reference"></a>Informations de référence des API de Content Moderator

Vous pouvez commencer à utiliser les API Azure Content Moderator comme suit :

- Dans le portail Azure, [abonnez-vous à l’API Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Consultez [Essayer Content Moderator sur le web](quick-start.md) pour vous inscrire à l’[outil de révision de Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>API de modération

Vous pouvez utiliser les API suivantes de Content Moderator pour configurer votre flux de travail après modération.

| Description | Informations de référence |
| -------------------- |-------------|
| **API Modération des images**<br /><br />Analysez des images et détectez tout contenu potentiellement pour adultes ou choquant à l’aide de balises, d’indices de confiance et d’autres informations extraites. <br /><br />Utilisez ces informations pour publier, rejeter ou réviser le contenu de votre flux de travail après modération. <br /><br />| [Informations de référence de l’API Modération des images](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Informations de référence de l’API Modération des images")   |
| **API Modération du texte**<br /><br />Analysez le contenu du texte. Les vulgarités et les données personnelles sont retournées. <br /><br />Utilisez ces informations pour publier, rejeter ou réviser le contenu de votre flux de travail après modération.<br /><br /> | [Informations de référence de l’API Modération du texte](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Informations de référence de l’API Modération du texte")   |
| **API Modération des vidéos**<br /><br />Analysez des vidéos et détectez tout contenu potentiellement pour adultes ou choquant. <br /><br />Utilisez ces informations pour publier, rejeter ou réviser le contenu de votre flux de travail après modération.<br /><br /> | [Présentation de l’API Modération des vidéos](video-moderation-api.md "Présentation de l’API Modération des vidéos")   |
| **API Gestion des listes**<br /><br />Créez et gérez des listes d’exclusions ou d’inclusions personnalisées de texte et d’images. Si elles sont activées, les opérations **Image - Match** (Image - Correspondance) et **Text - Screen** (Texte - Détecter) effectuent une correspondance approximative du contenu envoyé par rapport à vos listes personnalisées. <br /><br />Pour plus d’efficacité, vous pouvez ignorer l’étape de modération basée sur l’apprentissage automatique.<br /><br /> | [Informations de référence de l’API Gestion des listes](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Informations de référence de l’API Gestion des listes")   |

## <a name="review-apis"></a>Consultation des API

Les composants des API de révision sont les suivants :

| Description | Informations de référence |
| -------------------- |-------------|
| **Tâches**<br /><br /> Lancez des flux de travail de modération par analyse et révision du contenu de texte et d’image. Un travail de modération analyse votre contenu à l’aide de l’API Modération des images ou de l’API Modération du texte. Les travaux de modération utilisent les flux de travail définis et par défaut pour générer des révisions. <br /><br />Une fois qu’un modérateur a examiné les balises affectées automatiquement ainsi que les données de prédiction et soumis une décision en matière de modération du contenu, l’API Review (Révision) soumet toutes les informations à votre point de terminaison d’API.<br /><br /> | [Informations de référence des travaux](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Informations de référence des travaux")   |
| **Révisions**<br /><br />Utilisez l’outil de révision pour créer directement des révisions d’image ou de texte pour les modérateurs.<br /><br /> Une fois qu’un modérateur a examiné les balises affectées automatiquement ainsi que les données de prédiction et soumis une décision en matière de modération du contenu, l’API Review (Révision) soumet toutes les informations à votre point de terminaison d’API.<br /><br /> | [Informations de référence sur l’API Review (Révision)](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Informations de référence sur l’API Review (Révision)")   |
| **Flux de travail**<br /><br />Créez et mettez à jour des flux de travail personnalisés, et obtenez des détails sur ceux qui sont créés par votre équipe. Définissez des flux de travail à l’aide de l’outil de révision. <br /> <br />Les flux de travail utilisent généralement Content Moderator, mais ils peuvent également utiliser d’autres API qui sont disponibles sous forme de connecteurs dans l’outil de révision.<br /><br /> | [Informations de référence des flux de travail](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Informations de référence des flux de travail")   |