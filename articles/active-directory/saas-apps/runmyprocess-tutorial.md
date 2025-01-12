---
title: 'Didacticiel : Intégration d’Azure Active Directory à RunMyProcess | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880414"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Didacticiel : Intégrer RunMyProcess à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer RunMyProcess à Azure Active Directory (Azure AD). Quand vous intégrez RunMyProcess à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à RunMyProcess.
* Permettre à vos utilisateurs de se connecter automatiquement à RunMyProcess avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement RunMyProcess pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* RunMyProcess prend en charge l’authentification unique lancée par le **fournisseur de services**

## <a name="adding-runmyprocess-from-the-gallery"></a>Ajout de RunMyProcess à partir de la galerie

Pour configurer l’intégration de RunMyProcess à Azure AD, vous devez ajouter RunMyProcess à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **RunMyProcess** dans la zone de recherche.
1. Sélectionnez **RunMyProcess** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec RunMyProcess à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur RunMyProcess associé.

Pour configurer et tester l’authentification unique Azure AD avec RunMyProcess, suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique RunMyProcess](#configure-runmyprocess-sso)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test RunMyProcess](#create-runmyprocess-test-user)** pour avoir un équivalent de B.Simon dans RunMyProcess lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **RunMyProcess**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’[équipe de support technique RunMyProcess](mailto:support@runmyprocess.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer RunMyProcess**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Configurer l’authentification unique RunMyProcess

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre client RunMyProcess en tant qu’administrateur.

1. Dans le volet de navigation à gauche, cliquez sur **Compte** et sélectionnez **Configuration**.

    ![Configurer l’authentification unique côté application](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Accédez à la section **Méthode d’authentification** et suivez la procédure ci-dessous :

    ![Configurer l’authentification unique côté application](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Pour **Method**, sélectionnez **SSO with Samlv2**.

    b. Dans la zone de texte **SSO redirect** (Redirection de l’authentification unique), collez la valeur de **URL de connexion**, que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Logout redirect** (Redirection de la déconnexion), collez la valeur de **URL de déconnexion**, que vous avez copiée à partir du portail Azure.

    d. Dans la zone de texte **Name ID Format** (Format d’identificateur de nom), tapez **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** comme valeur de **Format de l’identificateur du nom**.

    e. Dans le Bloc-notes, ouvrez le fichier de certificat téléchargé à partir du portail Azure, copiez le contenu du fichier de certificat, puis collez-le dans la zone de texte **Certificat**.

    f. Cliquez sur l’icône **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RunMyProcess.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **RunMyProcess**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-runmyprocess-test-user"></a>Créer un utilisateur de test de RunMyProcess

Pour permettre aux utilisateurs Azure AD de se connecter à RunMyProcess, vous devez les provisionner dans RunMyProcess. Dans le cas de RunMyProcess, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise RunMyProcess en tant qu’administrateur.

1. Dans le volet de navigation à gauche, cliquez sur **Compte** et sélectionnez **Utilisateurs**, puis cliquez sur **Nouvel utilisateur**.

    ![Nouvel utilisateur](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nouvel utilisateur")

1. Dans la section **User Settings** , procédez comme suit :

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Tapez le nom et l’adresse électronique d’un compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Name** et **E-mail**.

    b. Sélectionnez des options pour **IDE language**, **Language** et **Profile**.

    c. Sélectionnez **Send account creation e-mail to me**.

    d. Cliquez sur **Enregistrer**.

    > [!NOTE]
    > Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par RunMyProcess, pour approvisionner des comptes utilisateur Azure Active Directory.

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette RunMyProcess dans le volet d’accès, vous devez être connecté automatiquement à l’application RunMyProcess pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)