---
title: 'Tutoriel : Héberger un site web statique dans le stockage Blob - Stockage Azure'
description: Découvrez comment configurer un compte de stockage pour l’hébergement de sites web statiques et comment déployer un site web statique sur Stockage Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 4cc38b952e773002e0b5f6b2311187f8d3d46400
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855388"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutoriel : Héberger un site web statique dans le stockage Blob

Ce tutoriel est la première partie d’une série d’étapes. Vous y découvrirez comment créer et déployer un site web statique sur Stockage Azure. Une fois que vous aurez terminé, vous aurez un site web statique accessible par tous. 

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Configurer l’hébergement de site web statique.
> * Déployer un site web Hello World.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ce tutoriel utilise [Visual Studio Code](https://code.visualstudio.com/download), outil gratuit pour les programmeurs qui s’en servent pour créer le site web statique et le déployer sur un compte Stockage Azure.

Après avoir installé Visual Studio Code, installez l’extension de préversion Stockage Azure. Cette extension intègre la fonctionnalité de gestion de Stockage Azure avec Visual Studio Code. Vous utiliserez l’extension pour déployer votre site web statique sur Stockage Azure. Pour installer l’extension :

1. Lancez Visual Studio Code.
2. Dans la barre d’outils, cliquez sur **Extensions**. Recherchez *Stockage Azure*, puis sélectionnez l’extension **Stockage Azure** dans la liste. Ensuite, cliquez sur le bouton **Installer** pour installer l’extension.

    ![Installer l’extension Stockage Azure dans VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="configure-static-website-hosting"></a>Configurer l’hébergement de site web statique

La première étape consiste à configurer votre compte de stockage pour héberger un site web statique dans le portail Azure. Quand vous configurez votre compte pour l’hébergement de site web statique, Stockage Azure crée automatiquement un conteneur nommé *$web*. Le conteneur *$web* contiendra les fichiers de votre site web statique. 

1. Ouvrez le [portail Azure](https://portal.azure.com/) dans votre navigateur web. 
1. Recherchez votre compte de stockage et affichez la vue d’ensemble du compte.
1. Sélectionnez **Site web statique** pour afficher la page de configuration des sites web statiques.
1. Sélectionnez **Activé** pour activer l’hébergement de site web statique pour le compte de stockage.
1. Dans le champ **Nom du document d’index**, spécifiez *index.html* comme page d’index par défaut. La page d’index par défaut s’affiche quand un utilisateur accède à la racine de votre site web statique.  
1. Dans le champ **Chemin du document d’erreur**, spécifiez *404.html* comme page d’erreur par défaut. La page d’erreur par défaut s’affiche quand un utilisateur tente d’accéder à une page qui n’existe pas dans votre site web statique.
1. Cliquez sur **Enregistrer**. Le portail Azure affiche maintenant le point de terminaison de votre site web statique. 

    ![Activer l’hébergement de site web statique pour un compte de stockage](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Déployer un site web Hello World

Maintenant, créez une page web Hello World avec Visual Studio Code et déployez-la sur le site web statique hébergé dans votre compte Stockage Azure.

1. Créez un dossier vide nommé *mywebsite* sur votre système de fichiers local. 
1. Lancez Visual Studio Code, puis ouvrez le dossier que vous venez de créer à partir du volet **Explorer**.

    ![Ouvrir le dossier dans Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Créez le fichier d’index par défaut dans le dossier *mywebsite* et nommez-le *index.html*.

    ![Créer le fichier d’index par défaut dans Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Ouvrez *index.html* dans l’éditeur, collez le texte suivant dans le fichier et enregistrez-le :

    ```
    <h1>Hello World!</h1>
    ```

1. Créez le fichier d’erreur par défaut et nommez-le *404.html*.
1. Ouvrez *404.html* dans l’éditeur, collez le texte suivant dans le fichier et enregistrez-le :

    ```
    <h1>404</h1>
    ```

1. Cliquez avec le bouton droit sous le dossier *mywebsite* dans le volet **Explorer** et sélectionnez **Déployer sur le site web statique** pour déployer votre site web. Vous serez invité à vous connecter à Azure pour récupérer une liste d’abonnements.

1. Sélectionnez l’abonnement contenant le compte de stockage pour lequel vous avez activé l’hébergement de site web statique. Ensuite, sélectionnez le compte de stockage quand vous y êtes invité.

Visual Studio Code va maintenant charger vos fichiers sur votre point de terminaison web et afficher la barre d’état de réussite. Lancez le site web pour l’afficher dans Azure.

![Afficher le déploiement de site web statique dans Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Vous avez terminé le tutoriel et déployé un site web statique sur Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de ce tutoriel, vous avez découvert comment configurer votre compte Stockage Azure pour l’hébergement de site web statique, et comment créer et déployer un site web statique sur un point de terminaison Azure.

Passez maintenant à la deuxième partie, où vous allez configurer un domaine personnalisé avec SSL pour votre site web statique avec Azure CDN.

> [!div class="nextstepaction"]
> [Utiliser Azure CDN pour activer un domaine personnalisé avec SSL pour un site web statique](storage-blob-static-website-custom-domain.md)
