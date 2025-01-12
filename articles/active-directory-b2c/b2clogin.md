---
title: Définir les URL de redirection sur b2clogin.com – Azure Active Directory B2C
description: Découvrez l’utilisation de b2clogin.com dans vos URL de redirection pour Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533749"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Paramétrer les URL de redirection sur b2clogin.com pour Azure Active Directory B2C

Quand vous configurez un fournisseur d’identité pour l’inscription et la connexion à votre application Azure AD B2C (Azure Active Directory B2C) , vous devez spécifier une URL de redirection. Vous ne devez plus faire référence à *login.microsoftonline.com* dans vos applications et API. Utilisez à la place *b2clogin.com* pour toutes les nouvelles applications, et migrez les applications existantes de *login.microsoftonline.com* vers *b2clogin.com*.

## <a name="benefits-of-b2clogincom"></a>Avantages de b2clogin.com

Quand vous utilisez *b2clogin.com* comme URL de redirection :

* L’espace utilisé dans l’en-tête de cookie par les services Microsoft est réduit.
* Vos URL de redirection n’ont plus besoin de contenir de référence à Microsoft.
* Le code côté client JavaScript est pris en charge (actuellement en [préversion](user-flow-javascript-overview.md)) dans les pages personnalisées. En raison de restrictions de sécurité, le code JavaScript et les éléments de formulaire HTML sont supprimés des pages personnalisées si vous utilisez *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Vue d’ensemble des modifications nécessaires

Vous pouvez être amené à apporter plusieurs modifications avant de migrer vos applications vers *b2clogin.com* :

* Modifiez l’URL de redirection dans les applications de votre fournisseur d’identité pour faire référence à *b2clogin.com*.
* Mettez à jour vos applications Azure AD B2C pour qu’elles utilisent *b2clogin.com* dans leurs références de flux utilisateur et de point de terminaison de jeton.
* Mettez à jour les **origines autorisées** que vous avez définies dans les paramètres CORS de [personnalisation de l’interface utilisateur](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="change-identity-provider-redirect-urls"></a>Modifier les URL de redirection des fournisseurs d’identité

Sur le site web de chaque fournisseur d’identité sur lequel vous avez créé une application, modifiez toutes les URL approuvées de sorte que les redirections s’effectuent vers `your-tenant-name.b2clogin.com` au lieu de *login.microsoftonline.com*.

Vous pouvez utiliser deux formats différents pour vos URL de redirection b2clogin.com. L’avantage du premier est que « Microsoft » ne figure pas dans l’URL, car il utilise l’ID de locataire (GUID) et non le nom de domaine votre locataire :

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Le deuxième utilise le nom de domaine de votre locataire sous la forme `your-tenant-name.onmicrosoft.com`. Par exemple :

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Pour les deux formats :

* Remplacez `{your-tenant-name}` par le nom de votre locataire Azure AD B2C.
* Supprimez l’élément `/te` s’il figure dans l’URL.

## <a name="update-your-applications-and-apis"></a>Mettre à jour vos applications et API

Le code de vos applications et API Azure AD B2C peut faire référence à `login.microsoftonline.com` à plusieurs endroits. Par exemple, votre code peut contenir des références à des flux utilisateur et à des points de terminaison de jeton. Mettez à jour les éléments suivants pour qu’ils fassent plutôt référence à `your-tenant-name.b2clogin.com` :

* Point de terminaison d’autorisation
* Point de terminaison de jeton
* Émetteur de jeton

Par exemple, le point de terminaison d’autorité de la stratégie d’inscription/connexion de Contoso serait désormais :

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Bibliothèque d’authentification Microsoft (MSAL)

### <a name="validateauthority-property"></a>Propriété ValidateAuthority

Si vous utilisez [MSAL.NET][msal-dotnet] v2 ou une version antérieure, définissez la propriété **ValidateAuthority** sur `false` à l’instanciation du client pour autoriser les redirections vers *b2clogin.com*. Ce paramètre n’est pas obligatoire pour MSAL.NET v3 et les versions ultérieures.

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Si vous utilisez [MSAL pour JavaScript][msal-js] :

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md