---
title: 'Démarrage rapide : Bibliothèque de client QnA Maker pour Python'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la bibliothèque de client QnA Maker pour Python. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: diberry
ms.openlocfilehash: 57407846ba2b1a71ceb91678c3ec4587d99814ad
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947296"
---
# <a name="quickstart-qna-maker-client-library-for-python"></a>Démarrage rapide : Bibliothèque de client QnA Maker pour Python

Découvrez comment utiliser la bibliothèque de client QnA Maker pour Python. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit. 

Utilisez la bibliothèque de client QnA Maker pour Python afin de :

* Créer une base de connaissances 
* Gérer une base de connaissances
* Publier une base de connaissances

[Documentation de référence](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Exemples Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-qna-maker-azure-resource"></a>Créer une ressource Azure QnA Maker

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour QnA Maker en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. 

Après avoir obtenu une clé à partir de votre ressource, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la ressource nommées `QNAMAKER_KEY` et `QNAMAKER_HOST`. Utilisez les valeurs de la clé et de l’hôte qui se trouvent sur le portail Azure.

### <a name="install-the-python-library-for-qna-maker"></a>Installer la bibliothèque Python pour QnA Maker

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Modèle objet

Créez un objet [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python).

Une fois le client créé, utilisez la propriété [Knowledge base](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) pour créer, gérer et publier votre base de connaissances. 

Pour les opérations immédiates, une méthode retourne généralement un objet JSON indiquant l’état. Pour les opérations de longue durée, la réponse est l’ID d’opération. Appelez la méthode [client.Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.operations%28class%29?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) avec l’ID d’opération pour déterminer l’[état de la requête](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python). 

 
## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client QnA Maker pour Python :

* [Créer une base de connaissances](#create-a-knowledge-base)
* [Mettre à jour une base de connaissances](#update-a-knowledge-base)
* [Publier une base de connaissances](#publish-a-knowledge-base)
* [Télécharger une base de connaissances](#download-a-knowledge-base)
* [Supprimer une base de connaissances](#delete-a-knowledge-base)
* [Obtenir l’état d’une opération](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python dans votre éditeur ou IDE favori. Importez ensuite les bibliothèques suivantes.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.


|Variable d’environnement|Variable Python|Exemples|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|GUID de 32 caractères|
|`QNAMAKER_HOST`|`host`|`https://westus.api.cognitive.microsoft.com`|
||||

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Authentifier le client

Ensuite, créez un objet CognitiveServicesCredentials avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python).


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

 Utilisez l’objet client pour obtenir un objet [knowledge base operations](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python).

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python).
* Pour les **fichiers**, utilisez l'objet [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python). 
* Pour les **URL**, utilisez une liste de chaînes.

Appelez la méthode [create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-), puis passer l’ID d’opération retourné à la méthode [Operations.getDetails](#get-status-of-an-operation) pour interroger l’état. 

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]


## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en passant l’ID de celle-ci et un [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) contenant des objets DTO [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) et [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) à la méthode [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-). Utilisez la méthode [Operation.getDetail](#get-status-of-an-operation) pour déterminer si la mise à jour a réussi.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur un point de terminaison. 

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Télécharger une base de connaissances

Utilisez la méthode [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) pour télécharger la base de données sous la forme d’une liste de [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Ceci n'est _pas_ équivalent à l’exportation à partir de la page **Paramètres** du portail QnA Maker, car le résultat de cette méthode n’est pas un fichier TSV.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) avec comme paramètre l’ID de la base de connaissances. 

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation%28class%29?view=azure-python) est retournée. Utilisez l’ID de l’opération pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine. 

L’appel _setTimeout_ dans le bloc de code suivant est utilisé pour simuler le code asynchrone. Remplacez cela par la logique de nouvelle tentative. 

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python knowledgebase_quickstart.py` à partir de votre répertoire d’application.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Tutoriel : Créer et interroger une base de connaissances](../tutorials/create-publish-query-in-portal.md)

* [Qu’est-ce que l’API QnA Maker ?](../Overview/overview.md)
* [Modifier une base de connaissances](../how-to/edit-knowledge-base.md)
* [Obtenir une analyse de l'utilisation](../how-to/get-analytics-knowledge-base.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py).