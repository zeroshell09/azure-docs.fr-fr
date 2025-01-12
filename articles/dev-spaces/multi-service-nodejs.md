---
title: Exécution de plusieurs services dépendants à l’aide de Node.js et de VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/21/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: 136d48f1c420ac71896eaafa0daab476c7fba6fa
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503077"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Développement multiservice avec Azure Dev Spaces

Dans ce tutoriel, vous allez apprendre à développer des applications multiservices à l’aide d’Azure Dev Spaces, et découvrir certains des avantages de Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Appeler un service en cours d’exécution dans un conteneur distinct

Dans cette section, vous allez créer un deuxième service, `mywebapi`, et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous effectuerez le débogage dans les deux conteneurs.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Ouvrir l’exemple de code pour *mywebapi*
Vous devez déjà avoir l’exemple de code de `mywebapi` pour ce guide dans un dossier nommé `samples` (dans le cas contraire, accédez à https://github.com/Azure/dev-spaces et sélectionnez **Cloner ou télécharger** pour télécharger le référentiel GitHub.). Le code de cette section se trouve dans `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le dossier `mywebapi` dans une *fenêtre VS Code distincte*.
1. Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Cette commande ne doit pas être confondue avec la commande `azds prep`, qui configure le projet pour le déploiement.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Le message *Écoute sur le port 80* s’affiche dans la console de débogage quand le service est prêt.
1. Notez l’URL du point de terminaison qui doit ressembler à `http://localhost:<portnumber>`. **Conseil : La barre d’état de VS Code devient orange et affiche une URL sur laquelle vous pouvez cliquer.** Le conteneur semble s’exécuter en local, mais en réalité, il s’exécute dans votre environnement de développement dans Azure. L’adresse localhost est utilisée car `mywebapi` n’a pas défini de points de terminaison publics et est accessible uniquement à partir de l’instance Kubernetes. Pour des raisons pratiques et pour faciliter l’interaction avec le service privé à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure.
1. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost. Vous devez voir une réponse du service `mywebapi` («Hello from mywebapi »).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une demande de *webfrontend* à *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`.
1. Basculer vers la fenêtre VS Code pour `webfrontend`.
1. Ajoutez ces lignes de code en haut de `server.js` :
    ```javascript
    var request = require('request');
    ```

3. *Remplacez* le code par le gestionnaire GET `/api`. Lors du traitement d’une demande, cette dernière effectue ensuite un appel à `mywebapi`, puis retourne les résultats des deux services.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Supprimez* la ligne `server.close()` à la fin de `server.js`

L’exemple de code précédent transfère l’en-tête `azds-route-as` de la requête entrante à la requête sortante. Vous verrez ultérieurement de quelle façon cette fonction aide les équipes au niveau du développement collaboratif.

### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans le gestionnaire `/` GET par défaut [ à la ligne 8 de `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. Dans le projet `webfrontend`, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `http://mywebapi`.
1. Appuyez sur F5 dans le projet `webfrontend`.
1. Ouvrez l’application web et parcourez le code dans les deux services. L’application web doit afficher un message concaténé par les deux services : « Hello from webfrontend and Hello from mywebapi. »

### <a name="well-done"></a>C’est terminé !
Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le développement en équipe dans Dev Spaces](team-development-nodejs.md)
