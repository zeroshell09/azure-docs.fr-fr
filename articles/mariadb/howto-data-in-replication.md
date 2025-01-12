---
title: Configurer la réplication des données entrantes dans Azure Database for MariaDB | Microsoft Docs
description: Cet article décrit comment configurer la réplication des données entrantes dans Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444808"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurer la réplication des données entrantes dans Azure Database for MariaDB

Cet article décrit comment configurer la réplication des données entrantes dans Azure Database for MariaDB en configurant les serveurs maître et réplica. Cet article suppose que vous ayez déjà utilisé des serveurs et des bases de données MariaDB.

Pour créer un réplica dans Azure Database for MariaDB, réplication des données entrantes synchronise les données provenant d’un serveur MariaDB maître qui s’exécute en local, dans des machines virtuelles ou dans des services de base de données.

> [!NOTE]
> Si votre serveur maître correspond à la version 10.2 ou ultérieure, nous vous recommandons de définir la réplication des données entrantes à l’aide de l'[ID de transaction global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Créer un serveur MariaDB à utiliser en tant que réplica

1. Créez un nouveau serveur Azure Database for MariaDB (par exemple, replica.mariadb.database.azure.com). Le serveur correspond au serveur réplica dans la réplication de données entrantes.

    Pour plus d'informations sur la création d'un serveur, consultez [Créer un serveur Azure Database for MariaDB à l’aide du portail Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Vous devez créer le serveur Azure Database for MariaDB dans les niveaux tarifaires Usage général ou Mémoire optimisée.

2. Créez des comptes d’utilisateurs identiques et les privilèges correspondants.
    
    Les comptes d’utilisateur ne sont pas répliqués à partir du serveur maître vers le serveur réplica. Pour fournir aux utilisateurs un accès au serveur réplica, vous devez créer manuellement tous les comptes et privilèges correspondants sur le nouveau serveur Azure Database for MariaDB.

## <a name="configure-the-master-server"></a>Configurer le serveur maître

Les étapes suivantes préparent et configurent le serveur MariaDB hébergé localement, dans une machine virtuelle ou dans un service de base de données cloud pour la réplication des données entrantes. Le serveur MariaDB correspond au maître dans la réplication de données entrantes.

1. Activez la journalisation binaire.
    
    Pour vérifier que la journalisation binaire est activée sur le serveur maître, entrez la commande suivante :

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Si la variable [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) renvoie la valeur `ON`, la journalisation binaire est activée sur votre serveur.

   Si `log_bin` renvoie la valeur `OFF`, modifiez le fichier **my.cnf** pour permettre à `log_bin=ON` d'activer la journalisation binaire. Redémarrez le serveur pour que la modification prenne effet.

2. Configurez les paramètres du serveur maître.

    La réplication des données entrantes implique que le paramètre `lower_case_table_names` soit cohérent entre les serveurs maître et réplica. Par défaut, le paramètre `lower_case_table_names` est défini sur `1` dans Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Créez un nouveau rôle de réplication et définir des autorisations.

   Créez un compte d’utilisateur sur le serveur maître configuré avec des privilèges de réplication. Vous pouvez créer un compte à l’aide des commandes SQL ou MySQL Workbench. Si vous envisagez une réplication avec SSL, vous devez le spécifier lors de la création du compte d’utilisateur.
   
   Pour savoir comment ajouter des comptes d’utilisateurs sur votre serveur maître, consultez la [documentation MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Les commandes suivantes permettent au nouveau rôle de réplication d'accéder au serveur maître à partir de n’importe quelle machine, et pas seulement de celle qui héberge le serveur maître. Pour cet accès, spécifiez **syncuser\@'%'** dans la commande pour créer un utilisateur.
   
   Pour en savoir plus sur la documentation MariaDB, consultez la [définition des noms de compte](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Commande SQL**

   - Réplication avec SSL

       Pour exiger le protocole SSL pour toutes les connexions utilisateur, entrez la commande suivante pour créer un utilisateur :

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Réplication sans SSL

       Si le protocole SSL n’est pas requis pour toutes les connexions, entrez la commande suivante pour créer un utilisateur :
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Pour créer le rôle de réplication dans MySQL Workbench, dans le volet **Gestion**, sélectionnez **Utilisateurs et privilèges**. Sélectionnez ensuite **Ajouter un compte**.
 
   ![Utilisateurs et privilèges](./media/howto-data-in-replication/users_privileges.png)

   Entrez un nom d’utilisateur dans le champ **Nom de connexion**.

   ![Synchroniser l’utilisateur](./media/howto-data-in-replication/syncuser.png)
 
   Sélectionnez le panneau **Rôles administratifs**, puis dans la liste **Privilèges globaux**, sélectionnez **Subordonné de réplication**. Sélectionnez **Appliquer** pour créer le rôle de réplication.

   ![Subordonné de réplication](./media/howto-data-in-replication/replicationslave.png)


4. Définissez le serveur maître en mode lecture seule.

   Avant de vider une base de données, le serveur doit être placé en mode lecture seule. En mode lecture seule, le serveur maître ne peut traiter aucune transaction d’écriture. Pour éviter tout impact sur votre entreprise, planifiez la fenêtre en lecture seule lors d'une période creuse.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenez le nom actuel du fichier du journal binaire et le décalage.

   Pour déterminer le nom de fichier du journal binaire actuel et le décalage, exécutez la commande [`show master status`](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Les résultats doivent être similaires au tableau suivant :
   
   ![Résultats de l’état récapitulatif](./media/howto-data-in-replication/masterstatus.png)

   Notez le nom du fichier binaire car il sera utilisé lors des étapes ultérieures.
   
6. Obtenez la position de l'identificateur GTID (facultatif, requis pour la réplication avec GTID).

   Exécutez la fonction [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) pour obtenir la position de l'identificateur GTID pour le nom de fichier binlog correspondant et le décalage.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Vider et restaurer le serveur maître

1. Videz toutes les bases de données du serveur maître.

   Utilisez mysqldump pour vider toutes les bases de données du serveur maître. Il n’est pas nécessaire de vider les bibliothèques MySQL et de test.

    Pour plus d’informations, consultez [Vider et restaurer](howto-migrate-dump-restore.md).

2. Définissez le serveur maître en mode lecture/écriture.

   Une fois la base de données sauvegardée, remettez le serveur MariaDB maître en mode de lecture/écriture.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurez le fichier de vidage sur le nouveau serveur.

   Restaurez le fichier de vidage sur le serveur créé dans le service Azure Database for MariaDB. Consultez [Sauvegarder et restaurer](howto-migrate-dump-restore.md) pour savoir comment restaurer un fichier de vidage sur un serveur MariaDB.

   Si le fichier de vidage est volumineux, transférez-le vers une machine virtuelle dans Azure au sein de la même région que votre serveur réplica. Restaurez-le sur le serveur Azure Database for MariaDB à partir de la machine virtuelle.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Lier les serveurs maître et réplica pour démarrer la réplication des données entrantes

1. Définissez le serveur maître.

   Toutes les fonctions de réplication de données entrantes sont effectuées par des procédures stockées. Vous trouverez toutes les procédures dans [Data-in Replication Stored Procedures](reference-data-in-stored-procedures.md) (Procédures stockées de réplication de données entrantes). Les procédures stockées peuvent être exécutées dans l’interpréteur de commandes MySQL ou MySQL Workbench.

   Pour lier deux serveurs et démarrer une réplication, connectez-vous au serveur réplica cible dans le service Azure Database for MariaDB. Définissez ensuite l’instance externe en tant que serveur maître à l’aide de la procédure stockée `mysql.az_replication_change_master` ou `mysql.az_replication_change_master_with_gtid` sur le serveur Azure DB for MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   or
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host : nom d’hôte du serveur maître
   - master_user : nom d’utilisateur pour le serveur maître
   - master_password : mot de passe pour le serveur maître
   - master_log_file : nom de fichier du journal binaire à partir de l’exécution de `show master status`
   - master_log_pos : position du journal binaire à partir de l’exécution de `show master status`
   - master_gtid_pos : position de l'identificateur GTID à partir de l'exécution de `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca : contexte du certificat d’autorité de certification. Si vous n’utilisez pas le protocole SSL, transmettez une chaîne vide.*
    
    
    *Nous vous recommandons de transmettre le paramètre master_ssl_ca en tant que variable. Pour plus d'informations, consultez les exemples suivants.

   **Exemples**

   - Réplication avec SSL

       Créez la variable `@cert` en exécutant les commandes suivantes :

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       La réplication avec SSL est définie entre un serveur maître hébergé dans le domaine « companya.com » et un serveur réplica hébergé dans Azure Database for MariaDB. Cette procédure stockée est exécutée sur le réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Réplication sans SSL

       La réplication sans SSL est définie entre un serveur maître hébergé dans le domaine companya.com et un serveur réplica hébergé dans Azure Database for MariaDB. Cette procédure stockée est exécutée sur le réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Démarrez la réplication.

   Appelez la procédure stockée `mysql.az_replication_start` pour commencer la réplication.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Vérifiez l’état de la réplication.

   Appelez la commande [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) sur le serveur réplica pour afficher l’état de réplication.
    
   ```sql
   show slave status;
   ```

   Si l’état de `Slave_IO_Running` et `Slave_SQL_Running` est `yes` et la valeur de `Seconds_Behind_Master` est `0`, cela signifie que la réplication fonctionne. `Seconds_Behind_Master` indique que le retard du réplica. Si la valeur est différente de `0`, cela signifie que le réplica traite les mises à jour.

4. Mettez à jour les variables du serveur correspondant pour renforcer la sécurité de la réplication des données entrantes (uniquement requis pour la réplication sans GTID).
    
    En raison d’une limitation de la réplication native dans MariaDB, vous devez définir les variables [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) et [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) sur la réplication sans scénario GTID.

    Vérifiez les variables `sync_master_info` et `sync_relay_log_info` de votre serveur esclave pour vous assurer que la réplication des données entrantes est stable, et définissez les variables sur `1`.
    
## <a name="other-stored-procedures"></a>Autres procédures stockées

### <a name="stop-replication"></a>Arrêter la réplication

Pour arrêter la réplication entre le serveur maître et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Supprimer la relation de réplication

Pour supprimer la relation entre le serveur maître et le serveur réplica, utilisez la procédure stockée suivante :

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorer l'erreur de réplication

Pour ignorer une erreur de réplication et autoriser la réplication, utilisez la procédure stockée suivante :
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez plus en détail la [réplication des données entrantes](concepts-data-in-replication.md) pour Azure Database for MariaDB.
