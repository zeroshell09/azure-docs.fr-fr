---
title: 'Didacticiel : Intégration de l’authentification unique (SSO) Azure Active Directory à Oracle Fusion ERP | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Oracle Fusion ERP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7586369-c1d6-4e83-a33e-5e87a68a7722
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ba929ab13864664a823ba969ecb472812d946a9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989543"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-fusion-erp"></a>Didacticiel : Intégration de l’authentification unique Azure Active Directory à Oracle Fusion ERP

Dans ce tutoriel, vous allez découvrir comment intégrer Oracle Fusion ERP à Azure Active Directory (Azure AD). Quand vous intégrez Oracle Fusion ERP à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Oracle Fusion ERP.
* Autoriser les utilisateurs à se connecter automatiquement à Oracle Fusion ERP avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Oracle Fusion ERP pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test.

* Oracle Fusion ERP prend en charge l’authentification unique initiée par le **fournisseur de services**.

## <a name="adding-oracle-fusion-erp-from-the-gallery"></a>Ajout d’Oracle Fusion ERP à partir de la galerie

Pour configurer l’intégration d’Oracle Fusion ERP à Azure AD, vous devez ajouter Oracle Fusion ERP à votre liste d’applications SaaS managées, à partir de la galerie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Oracle Fusion ERP** dans la zone de recherche.
1. Sélectionnez **Oracle Fusion ERP** dans le volet des résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-oracle-fusion-erp"></a>Configurer et tester l’authentification unique Azure AD pour Oracle Fusion ERP

Configurez et testez l’authentification unique Azure AD avec Oracle Fusion ERP à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Oracle Fusion ERP associé.

Pour configurer et tester l’authentification unique Azure AD avec Oracle Fusion ERP, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique Oracle Fusion ERP](#configure-oracle-fusion-erp-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test Oracle Fusion ERP](#create-oracle-fusion-erp-test-user)** pour avoir un équivalent de B.Simon dans Oracle Fusion ERP qui soit lié à sa représentation dans Azure AD.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Oracle Fusion ERP**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://<SUBDOMAIN>.fa.em2.oraclecloud.com/fscmUI/faces/AtkHomePageWelcome`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant : `https://<SUBDOMAIN>.login.em2.oraclecloud.com:443/oam/fed`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique Oracle Fusion ERP](mailto:jinshu.peethambaran@oracle.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **XML de métadonnées de fédération**, puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

1. Dans la section **Configurer Oracle Fusion ERP**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Oracle Fusion ERP.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Oracle Fusion ERP**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-oracle-fusion-erp-sso"></a>Configurer l’authentification unique Oracle Fusion ERP

Pour configurer l’authentification unique côté **Oracle Fusion ERP**, vous devez envoyer le **XML des métadonnées de fédération** téléchargé et les URL copiées dans le portail Azure à l’[équipe du support technique Oracle Fusion ERP](mailto:jinshu.peethambaran@oracle.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-oracle-fusion-erp-test-user"></a>Créer un utilisateur de test Oracle Fusion ERP

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Oracle Fusion ERP. Collaborez avec l’[équipe du support technique Oracle Fusion ERP](mailto:jinshu.peethambaran@oracle.com) pour ajouter des utilisateurs dans la plateforme Oracle Fusion ERP. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Oracle Fusion ERP dans le volet d’accès, vous devez être connecté automatiquement à l’application Oracle Fusion ERP pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de tutoriels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Essayer la solution ERP Oracle Fusion avec Azure AD](https://aad.portal.azure.com/)
