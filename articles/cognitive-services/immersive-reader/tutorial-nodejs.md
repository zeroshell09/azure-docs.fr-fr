---
title: 'Didacticiel : Lancer le Lecteur immersif à l’aide de Node.js'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez créer une application Node.js qui lance le Lecteur immersif.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ea9728ac54ae357925bd666ffecf270172137f77
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991111"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Didacticiel : Lancer le lecteur immersif (Node.js)

Dans la [présentation](./overview.md), vous avez appris ce qu’est le Lecteur immersif et comment il implémente des techniques éprouvées pour améliorer la compréhension de la lecture pour les apprenants en langue, les lecteurs émergents et les étudiants présentant des difficultés d’apprentissage. Dans ce tutoriel, vous allez créer une application web Node.js qui lance le Lecteur immersif. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une application web Node.js avec Express
> * Obtenir un jeton d’accès
> * Lancer le Lecteur immersif avec un exemple de contenu
> * Spécifier la langue de votre contenu
> * Spécifier la langue de l’interface du Lecteur immersif
> * Lancer le Lecteur immersif avec du contenu mathématique

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Ressource Lecteur immersif configurée pour l’authentification Azure Active Directory (Azure AD). Suivez [ces instructions](./azure-active-directory-authentication.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’environnement. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* [Node.js](https://nodejs.org/) et [Yarn](https://yarnpkg.com)
* Un IDE tel que [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Créer une application web Node.js avec Express

Créez une application web Node.js avec l’outil `express-generator`.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installez les dépendances yarn et ajoutez les dépendances `request` et `dotenv`, qui seront utilisées ultérieurement dans ce tutoriel.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquérir un jeton d’authentification Azure AD

Ensuite, écrivez une API back-end pour récupérer un jeton d’authentification Azure AD.

Vous avez besoin de certaines valeurs de l’étape prérequise de configuration de l’authentification Azure AD ci-dessus pour cette partie. Reportez-vous au fichier texte que vous avez enregistré pour cette session.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Une fois que vous avez ces valeurs, créez un fichier nommé _.env_, puis collez-y le code suivant, en fournissant les valeurs de vos propriétés personnalisées ci-dessus.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Veillez à ne pas valider ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics.

Ensuite, ouvrez _app.js_ et ajoutez le code suivant au début du fichier. Cela charge les propriétés définies dans le fichier .env en tant que variables d’environnement dans Node.

```javascript
require('dotenv').config();
```

Ouvrez le fichier _routes\index.js_ et l’importation suivante au début du fichier :

```javascript
var request = require('request');
```

Ensuite, ajoutez le code suivant directement sous cette ligne. Ce code crée un point de terminaison d’API qui acquiert un jeton d’authentification Azure AD à l’aide de votre mot de passe de principal du service, puis retourne ce jeton. Il existe également un deuxième point de terminaison pour récupérer le sous-domaine.

```javascript
router.get('/getimmersivereadertoken', function(req, res) {
  request.post ({
          headers: {
              'content-type': 'application/x-www-form-urlencoded'
          },
          url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
          form: {
              grant_type: 'client_credentials',
              client_id: process.env.CLIENT_ID,
              client_secret: process.env.CLIENT_SECRET,
              resource: 'https://cognitiveservices.azure.com/'
          }
      },
      function(err, resp, token) {
          if (err) {
              return res.status(500).send('CogSvcs IssueToken error');
          }

          return res.send(JSON.parse(token).access_token);
      }
  );
});

router.get('/subdomain', function (req, res) {
    return res.send(process.env.SUBDOMAIN);
});
```

Le point de terminaison d’API **getimmersivereadertoken** doit être sécurisé derrière une certaine forme d’authentification (par exemple [OAuth](https://oauth.net/2/)) pour empêcher les utilisateurs non autorisés d’obtenir des jetons à utiliser auprès de vos services Facturation et Lecteur immersif ; ce travail dépasse le cadre de ce tutoriel.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lancer le Lecteur immersif avec un exemple de contenu

1. Ouvrez _views\layout.pug_ et ajoutez le code suivant sous la balise `head`, avant la balise `body`. Ces balises `script` chargent le [SDK du Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk) et jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Ouvrez _views\index.pug_, puis remplacez son contenu par le code suivant. Ce code remplit la page avec quelques exemples de contenu et ajoute un bouton qui lance le Lecteur immersif.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.

        function getImmersiveReaderTokenAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/getimmersivereadertoken',
                    type: 'GET',
                    success: token => {
                        resolve(token);
                    },
                    error: err => {
                        console.log('Error in getting token!', err);
                        reject(err);
                    }
                });
            });
        }

        function getSubdomainAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/subdomain',
                    type: 'GET',
                    success: subdomain => { resolve(subdomain); },
                    error: err => { reject(err); }
                });
            });
        }

        async function launchImmersiveReader() {
            const content = {
                title: document.getElementById('title').innerText,
                chunks: [{
                    content: document.getElementById('content').innerText + '\n\n',
                    lang: 'en'
                }]
            };

            const token = await getImmersiveReaderTokenAsync();
            const subdomain = await getSubdomainAsync();

            ImmersiveReader.launchAsync(token, subdomain, content);
        }
    ```

3. Notre application web est maintenant prête. Démarrez l’application en exécutant :

    ```bash
    npm start
    ```

4. Ouvrez votre navigateur et accédez à _http://localhost:3000_ . Vous devez voir le contenu ci-dessus dans la page. Cliquez sur le bouton **Lecteur immersif** pour lancer le Lecteur immersif avec votre contenu.

## <a name="specify-the-language-of-your-content"></a>Spécifier la langue de votre contenu

Le Lecteur immersif prend en charge de nombreuses langues différentes. Vous pouvez spécifier la langue de votre contenu en suivant les étapes ci-dessous.

1. Ouvrez _views\index.pug_ et ajoutez le code suivant sous la balise `p(id=content)` que vous avez ajoutée à l’étape précédente. Ce code ajoute du contenu en espagnol à votre page.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Dans le code JavaScript, ajoutez le code suivant au-dessus de l’appel à `ImmersiveReader.launchAsync`. Ce code transmet le contenu espagnol au Lecteur immersif.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Accédez à nouveau à _http://localhost:3000_ . Vous devriez voir le texte en espagnol sur la page, et lorsque vous cliquez sur le **Lecteur immersif**, le texte s’y affiche également.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Spécifier la langue de l’interface du Lecteur immersif

Par défaut, la langue de l’interface du Lecteur immersif correspond aux paramètres de langue du navigateur. Vous pouvez également spécifier la langue de l’interface du Lecteur immersif avec le code suivant.

1. Dans _views\index.pug_, remplacez l’appel à `ImmersiveReader.launchAsync(token, content)` par le code ci-dessous.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Accédez à _http://localhost:3000_ . Lorsque vous lancez le Lecteur immersif, l’interface s’affiche en français.

## <a name="launch-the-immersive-reader-with-math-content"></a>Lancer le Lecteur immersif avec du contenu mathématique

Vous pouvez inclure du contenu mathématique dans le Lecteur immersif à l’aide de [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modifiez _views\index.pug_ pour inclure le code suivant au-dessus de l’appel à `ImmersiveReader.launchAsync` :

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Accédez à _http://localhost:3000_ . Lorsque vous lancez le Lecteur immersif et faites défiler vers le bas, vous voyez la formule mathématique.

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [SDK Lecteur Immersif](https://github.com/microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)
* Voir des exemples de code sur [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
