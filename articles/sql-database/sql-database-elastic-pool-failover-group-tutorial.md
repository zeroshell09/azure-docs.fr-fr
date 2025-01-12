---
title: 'Didacticiel : Ajouter un pool élastique Azure SQL Database à un groupe de basculement | Microsoft Docs'
description: Ajoutez un pool élastique Azure SQL Database à un groupe de basculement à l’aide du portail Azure, de PowerShell ou d’Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 2d46e6f1d5c7079ab5bbfea39a85ea0a7592afc8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099284"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Didacticiel : Ajouter un pool élastique Azure SQL Database à un groupe de basculement

Configurez un groupe de basculement pour un pool élastique Azure SQL Database et tester le basculement à l’aide du portail Azure.  Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Créer une base de données unique Azure SQL Database
> - Ajouter la base de données unique à un pool élastique 
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md) pour un pool élastique entre deux serveurs SQL logiques
> - Tester le basculement

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants : 

- Un abonnement Azure. [Créez un compte gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas.


## <a name="1---create-a-single-database"></a>1 - Créer une base de données unique 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Ajouter la base de données unique au pool élastique

1. Dans le menu de gauche du portail Azure, sélectionnez **Azure SQL**. Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez **+Ajouter** pour ouvrir la page **Sélectionner l’option de déploiement SQL**. Vous pouvez afficher des informations supplémentaires sur les différentes bases de données en sélectionnant Afficher les détails sur la vignette Bases de données.
1. Sélectionnez **Pool élastique** dans la liste déroulante **Type de ressource** de la vignette **Bases de données SQL**. Sélectionnez **Créer** pour créer votre pool élastique. 

    ![Sélectionner un pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configurez votre pool élastique avec les valeurs suivantes :
   - **Nom** : spécifiez un nom unique pour votre pool élastique, par exemple `myElasticPool`. 
   - **Abonnement**: Sélectionnez votre abonnement dans la liste déroulante.
   - **Groupe de ressources** : sélectionnez `myResourceGroup` dans la liste déroulante, le groupe de ressources que vous avez créé dans la section 1. 
   - **Server** (Serveur) : sélectionnez le serveur créé dans la section 1 dans la liste déroulante.  

       ![Créer un serveur pour le pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Calcul + stockage** : sélectionnez **Configurer un pool élastique** pour configurer le calcul, le stockage, et ajouter votre base de données unique à votre pool élastique. Sous l’onglet **Paramètres du pool**, conservez la valeur par défaut Gen5, avec deux vCores et 32 Go. 

1. Dans la page **Configurer**, sélectionnez l’onglet **Bases de données **, puis choisissez d’** Ajouter la base de données**. Choisissez la base de données créée dans la section 1, puis sélectionnez **Appliquer** pour l’ajouter à votre pool élastique. Sélectionnez **Appliquer** à nouveau pour appliquer vos paramètres de pool élastique et fermez la page **Configurer**. 

    ![Ajouter la base de données SQL au pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Sélectionnez **Vérifier + créer** pour passer en revue les paramètres de votre pool élastique, puis sélectionnez **Créer** pour créer votre pool élastique. 


## <a name="3---create-the-failover-group"></a>3 - Créer le groupe de basculement 
Lors de cette étape, vous allez créer un [groupe de basculement](sql-database-auto-failover-group.md) entre un serveur SQL Azure existant et un nouveau serveur SQL Azure dans une autre région. Ensuite, vous ajouterez le pool élastique au groupe de basculement. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez le pool élastique créé dans la section précédente, par exemple `myElasticPool`. 
1. Dans le volet **Vue d’ensemble**, sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.
  
    ![Ouvrir le serveur pour le pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis sélectionnez **Ajouter un groupe** pour créer un groupe de basculement. 

    ![Ajouter un nouveau groupe de basculement](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Dans la page **Groupe de basculement**, entrez ou sélectionnez les valeurs suivantes, puis sélectionnez **Créer** :
    - **Nom du groupe de basculement** : Tapez un nom de groupe de basculement unique, par exemple `failovergrouptutorial`. 
    - **Serveur secondaire** : Sélectionnez l’option permettant de *configurer les paramètres requis*, puis choisissez de **Créer un serveur**. Vous pouvez également choisir un serveur existant en tant que serveur secondaire. Après avoir entré les valeurs suivantes pour votre nouveau serveur secondaire, sélectionnez **Sélectionner**. 
        - **Nom du serveur** : Tapez un nom unique pour le serveur secondaire, par exemple `mysqlsecondary`. 
        - **Connexion administrateur au serveur** : Tapez `azureuser`.
        - **Mot de passe** : tapez un mot de passe complexe qui répond aux exigences de mot de passe.
        - **Emplacement** : choisissez un emplacement dans la liste déroulante, tel que `East US`. Cet emplacement ne peut pas être le même que celui de votre serveur principal.

       > [!NOTE]
       > La connexion au serveur et les paramètres de pare-feu doivent correspondre à ceux de votre serveur principal. 
    
       ![Créer un serveur secondaire pour le groupe de basculement](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Sélectionnez **Bases de données au sein du groupe**, puis sélectionnez le pool élastique créé dans la section 2. Un avertissement doit s’afficher, vous invitant à créer un pool élastique sur le serveur secondaire. Sélectionnez l’avertissement, puis cliquez sur **OK** pour créer le pool élastique sur le serveur secondaire. 
        
    ![Ajouter un pool élastique à un groupe de basculement](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Sélectionnez **Sélectionner** pour appliquer les paramètres de votre pool élastique au groupe de basculement, puis sélectionnez **Créer** pour créer votre groupe de basculement. L’ajout du pool élastique au groupe de basculement démarre automatiquement le processus de géoréplication. 


## <a name="4---test-failover"></a>4 - Tester le basculement 
Dans cette étape, vous allez faire basculer votre groupe de basculement sur le serveur secondaire, puis effectuer une restauration automatique en utilisant le portail Azure. 

1. Dans le menu de gauche du **Portail Azure**, sélectionnez [Azure SQL](https://portal.azure.com). Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis tapez Azure SQL dans la zone de recherche. (Facultatif) Sélectionnez l’étoile en regard d’**Azure SQL** pour l’ajouter aux favoris et l’ajouter en tant qu’élément dans le volet de navigation de gauche. 
1. Sélectionnez le pool élastique créé dans la section précédente, par exemple `myElasticPool`. 
1. Sélectionnez le nom du serveur sous **Nom du serveur** pour ouvrir les paramètres du serveur.

    ![Ouvrir le serveur pour le pool élastique](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Sélectionnez **Groupes de basculement** dans le volet **Paramètres**, puis choisissez le groupe de basculement créé dans la section 2. 
  
   ![Sélectionner le groupe de basculement à partir du portail](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. 
1. Sélectionnez **Basculement** dans le volet des tâches pour faire basculer votre groupe de basculement contenant votre pool élastique. 
1. Sélectionnez **Oui** en réponse à l’avertissement qui signale que les sessions TDS seront déconnectées. 

   ![Basculer votre groupe de basculement contenant votre base de données SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Vérifiez quel est le serveur principal et quel est le serveur secondaire. Si le basculement a réussi, les deux serveurs doivent avoir échangé leur rôle. 
1. Sélectionnez **Basculement** à nouveau pour rétablir les paramètres d’origine du groupe de basculement. 

## <a name="clean-up-resources"></a>Supprimer des ressources 
Nettoyez les ressources en supprimant le groupe de ressources. 

1. Accédez à votre groupe de ressources sur le [portail Azure](https://portal.azure.com).
1. Sélectionnez **Supprimer le groupe de ressources** pour supprimer toutes les ressources du groupe ainsi que le groupe de ressources lui-même. 
1. Tapez le nom du groupe de ressources, `myResourceGroup`, dans la zone de texte, puis sélectionnez **Supprimer** pour supprimer le groupe de ressources.  


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une base de données unique Azure SQL Database à un groupe de basculement et vous avez testé le basculement. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créer une base de données unique Azure SQL Database
> - Ajouter la base de données unique à un pool élastique 
> - Créer un [groupe de basculement](sql-database-auto-failover-group.md) pour un pool élastique entre deux serveurs SQL logiques
> - Tester le basculement

Passez au tutoriel suivant sur la migration à l’aide de DMS.

> [!div class="nextstepaction"]
> [Tutoriel : Migrer SQL Server vers une base de données mise en pool à l’aide de DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
