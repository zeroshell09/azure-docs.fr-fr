---
title: 'Python : Opérations de gestion des comptes sur Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Découvrez comment traiter les opérations de gestion des comptes Azure Data Lake Storage Gen1 avec le Kit de développement logiciel (SDK) Python.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b6ef5a5c12bb766fb7106d5c7a8189c4b92980d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877255"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Opérations de gestion des comptes sur Azure Data Lake Storage Gen1 à l’aide de Python
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Découvrez comment utiliser le Kit de développement logiciel (SDK) Python pour Azure Data Lake Storage Gen1 afin d’effectuer des opérations simples de gestion des comptes (création de compte ou d’une liste des comptes existants, etc.). Pour obtenir des instructions sur l’exécution des opérations du système de fichiers sur Data Lake Storage Gen1 à l’aide de Python, consultez la section relative aux [opérations du système de fichiers sur Data Lake Storage Gen1 à l’aide de Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Prérequis

* **Python** Pour télécharger Python, accédez [ici](https://www.python.org/downloads/). Cet article utilise Python 3.6.2.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un groupe de ressources Azure**. Pour obtenir des instructions, consultez la page [Créer un groupe de ressources Azure](../azure-resource-manager/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Installer les modules

Pour utiliser Data Lake Storage Gen1 avec Python, vous devez installer trois modules.

* Le module `azure-mgmt-resource`, qui inclut des modules Azure pour Active Directory, etc.
* Le module `azure-mgmt-datalake-store`, qui inclut les opérations de gestion du compte Azure Data Lake Storage Gen1. Pour plus d’informations sur ce module, consultez les [informations de référence sur les modules de gestion Azure Data Lake Storage Gen1](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Le module `azure-datalake-store`, qui inclut les opérations de gestion du système de fichiers Azure Data Lake Storage Gen1. Pour plus d’informations sur ce module, consultez les [informations de référence sur les modules du système de fichiers azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

Utilisez les commandes suivantes pour installer les modules.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Créer une application Python

1. Utilisez l’IDE de votre choix pour créer une application Python, par exemple, **mysample.py**.

2. Ajoutez l’extrait de code suivant pour importer les modules requis

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Enregistrez les modifications apportées à mysample.py.

## <a name="authentication"></a>Authentication

Dans cette section, nous aborderons les différentes méthodes permettant de s’authentifier auprès d’Azure AD. Voici les options disponibles :

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application, consultez la rubrique [Authentification des utilisateurs finaux auprès de Data Lake Storage Gen1 avec Python](data-lake-store-end-user-authenticate-python.md).
* Pour en savoir plus sur l’authentification entre les services dans le cadre de votre application, consultez la rubrique [Authentification de service à service auprès de Data Lake Storage Gen1 à l’aide de Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Créer un client et un compte Data Lake Storage Gen1

L’extrait de code suivant crée dans un premier temps le client du compte Data Lake Storage Gen1. Il utilise l’objet client pour créer le compte Data Lake Storage Gen1. Enfin, l’extrait de code crée un objet client de système de fichiers.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Répertorier les comptes Data Lake Storage Gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Supprimer le compte Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Étapes suivantes
* [Opérations de système de fichiers dans Data Lake Storage Gen1 à l’aide de Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Voir aussi

* [Documents de référence sur la gestion de système de fichiers avec Azure Data Lake Store et Python](https://azure-datalake-store.readthedocs.io/en/latest)
* [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
