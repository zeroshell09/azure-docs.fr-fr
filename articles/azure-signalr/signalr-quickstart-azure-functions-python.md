---
title: Démarrage rapide du service Azure SignalR serverless - Python
description: Un démarrage rapide pour utiliser le service Azure SignalR et Azure Functions afin de créer une salle de conversation.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: antchu
ms.openlocfilehash: fbae9d15ba8572a8ae2c493ec85eb0010c2664eb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886421"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Démarrage rapide : Créer une salle de conversation avec Azure Functions et SignalR Service en utilisant Python

Le service Azure SignalR vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce démarrage rapide, découvrez comment utiliser le service SignalR et Functions pour générer une application serverless de conversation en temps réel.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.

Assurez-vous qu’un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/) est installé.

Installez [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) (version 2.7.1505 ou ultérieure) pour exécuter des applications Azure Function Python en local.

Azure Functions nécessite [Python 3.6](https://www.python.org/downloads/).

Pour installer des extensions, Azure Functions Core Tools requiert actuellement que le [kit SDK .NET Core](https://www.microsoft.com/net/download) soit installé. Toutefois, aucune connaissance de .NET n’est requise pour générer des applications Azure Function Python.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Configurer et exécuter l’application de fonction Azure

1. Dans le navigateur dans lequel le portail Azure est ouvert, vérifiez que l’instance du service SignalR que vous avez déployée précédemment a bien été créée en recherchant son nom dans la zone de recherche en haut du portail. Sélectionnez l’instance pour l’ouvrir.

    ![Rechercher l’instance du service SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Sélectionnez **Clés** pour afficher les chaînes de connexion de l’instance du service SignalR.

1. Sélectionnez et copiez la chaîne de connexion principale.

    ![Créer un service SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Dans votre éditeur de code, ouvrez le dossier *src/chat/python* dans le référentiel cloné.

1. Pour pouvoir développer et tester localement des fonctions Python, vous devez travailler dans un environnement Python 3.6. Exécutez les commandes suivantes pour créer et activer un environnement virtuel nommé `.venv`.

    **Linux ou macOS :**

    ```bash
    python3.6 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows :**

    ```powershell
    py -3.6 -m venv .venv
    .venv\scripts\activate
    ```

1. Renommez *local.settings.sample.json* en *local.settings.json*.

1. Dans le fichier **local.settings.json**, collez la chaîne de connexion dans la valeur du paramètre **AzureSignalRConnectionString**. Enregistrez le fichier .

1. Les fonctions Python sont organisées dans des dossiers. Deux fichiers se trouvent dans chaque dossier : le fichier *function.json* définit les liaisons utilisées dans la fonction et le fichier *\_\_init\_\_.py* correspond au corps de la fonction. Il existe deux fonctions déclenchées par HTTP dans cette application de fonction :

    - **negotiate** : utilise la liaison d’entrée *SignalRConnectionInfo* pour générer et retourner des informations de connexion valides.
    - **messages** : reçoit un message de conversation dans le corps de la demande et utilise la liaison de sortie *SignalR* pour diffuser le message à toutes les applications clientes connectées.

1. Dans le terminal, vérifiez que vous vous trouvez dans le dossier *src/chat/python*. Utilisez Azure Functions Core Tools pour installer les extensions requises pour exécuter l’application.

    ```bash
    func extensions install
    ```

1. Exécutez l’application de fonction.

    ```bash
    func start
    ```

    ![Exécuter Function App](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez généré et exécuté une application serverless en temps réel dans VS Code. Apprenez-en plus sur le déploiement d’Azure Functions à partir de VS Code.

> [!div class="nextstepaction"]
> [Déployer Azure Functions avec VS Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)