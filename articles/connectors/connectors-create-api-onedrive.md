---
title: Se connecter à OneDrive – Azure Logic Apps | Microsoft Docs
description: Charger et gérer des fichiers avec les API REST de OneDrive et Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106225"
---
# <a name="get-started-with-the-onedrive-connector"></a>Prise en main du connecteur OneDrive
Connexion à OneDrive pour gérer vos fichiers, y compris le téléchargement de fichiers, la suppression de fichiers, et plus encore. 

Avec OneDrive, vous pouvez effectuer les opérations suivantes : 

* Créer votre flux de travail en stockant des fichiers dans OneDrive, ou mettre à jour des fichiers existants dans OneDrive. 
* Utiliser des déclencheurs pour lancer votre flux de travail lorsqu’un fichier est créé ou mis à jour dans votre OneDrive.
* Utiliser des actions pour créer un fichier, supprimer un fichier et bien plus encore. Par exemple, lorsqu’un nouveau courrier électronique Office 365 est reçu avec une pièce jointe (déclencheur), créer un nouveau fichier dans OneDrive (action).

Cette rubrique décrit comment utiliser le connecteur OneDrive dans une application logique et répertorie les déclencheurs et les actions.

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../logic-apps/logic-apps-overview.md) et [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Connexion à OneDrive
Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à OneDrive, vous devez préalablement disposer d’une *connexion* OneDrive. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ensuite, dans OneDrive, entrez les informations d’identification de votre compte OneDrive pour créer la connexion.

### <a name="create-the-connection"></a>Créer la connexion
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. Les déclencheurs « interrogent » le service à l’intervalle et à la fréquence de votre choix. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Dans l’application logique, saisissez « onedrive » pour obtenir la liste des déclencheurs :  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Sélectionnez **Quand un fichier est modifié**. Si une connexion existe déjà, sélectionnez le bouton Afficher le sélecteur pour sélectionner un dossier.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Si vous êtes invité à vous connecter, entrez les informations de connexion pour créer la connexion. [Créer la connexion](connectors-create-api-onedrive.md#create-the-connection) dans cet article répertorie les étapes. 
   
   > [!NOTE]
   > Dans cet exemple, l’application logique s’exécute lorsqu’un fichier est mis à jour dans le dossier que vous avez choisi. Pour consulter les résultats de ce déclencheur, ajoutez une autre action qui vous envoie un courrier électronique. Par exemple, ajoutez l’action Office 365 Outlook *Envoyer un courrier électronique* qui vous avertit par e-mail quand un fichier est mis à jour. 

3. Sélectionnez le bouton **Modifier**, puis renseignez les valeurs **Fréquence** et **Intervalle**. Par exemple, si vous souhaitez que le déclencheur interroge le service toutes les 15 minutes, définissez le champ **Fréquence** sur **Minute**, et le champ **Intervalle** sur **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

## <a name="use-an-action"></a>Utilisation d’une action
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Choisissez **Ajouter une action**.
3. Dans la zone de texte, saisissez « onedrive » pour obtenir la liste de toutes les actions disponibles.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. Dans notre exemple, choisissez **OneDrive - Créer un fichier**. Si une connexion existe déjà, sélectionnez le **chemin du dossier** où placer le fichier, entrez le **nom de fichier** et choisissez le **contenu du fichier** souhaité :  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-onedrive.md#create-the-connection) de cet article décrit ces propriétés. 
   
   > [!NOTE]
   > Dans cet exemple, nous allons créer un nouveau fichier dans un dossier OneDrive. Vous pouvez utiliser les résultats d’un autre déclencheur pour créer le fichier OneDrive. Par exemple, ajoutez le déclencheur Outlook Office 365 *Lorsqu’un nouveau courrier électronique arrive*. Puis ajoutez l’action OneDrive *Créer un fichier* qui utilise les champs Pièces jointes et Type de contenu d’une instruction ForEach pour créer le fichier dans OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).