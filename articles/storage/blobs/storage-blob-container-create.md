---
title: Créer ou supprimer un conteneur d’objets BLOB avec .NET - Stockage Azure
description: Découvrez comment créer ou supprimer les conteneurs d’objets BLOB dans votre compte de stockage Azure à l’aide de la bibliothèque de client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 99773b83f15ea4b386c1078eed510fc233322f0f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985690"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Créer ou supprimer un conteneur dans le stockage Azure avec .NET

Les objets BLOB dans le stockage Azure sont organisés en conteneurs. Avant de pouvoir télécharger un objet BLOB, vous devez d’abord créer un conteneur. Cet article explique comment créer ou supprimer les conteneurs à l’aide de la [bibliothèque de client de stockage Azure pour .NET](/dotnet/api/overview/azure/storage/client).

## <a name="name-a-container"></a>Nommer un conteneur

Un nom de conteneur doit être un nom DNS valide, car il fait partie de l’URI unique utilisé pour adresser le conteneur ou ses objets BLOB. Suivez ces règles lorsque vous nommez un conteneur :

- Les noms de conteneur doivent comprendre entre 3 et 63 caractères.
- Les noms de conteneur doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres minuscules, des chiffres et des tirets (-).
- Au moins deux tirets consécutifs ne sont pas autorisés dans les noms de conteneurs.

L’URI d’un conteneur est au format suivant :

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Créez un conteneur.

Pour créer un conteneur, utilisez l’une des méthodes suivantes :

- [Créer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Les méthodes **Créer** et **CreateAsync** lèvent une exception si un conteneur portant le même nom existe déjà.

Les méthodes **CreateIfNotExists** et **CreateIfNotExistsAsync** retournent une valeur booléenne indiquant si le conteneur a été créé. Si un conteneur portant le même nom existe déjà, ces méthodes retournent une mention **False** pour indiquer qu’un nouveau conteneur n’a pas été créé.

Les conteneurs sont créés immédiatement sous le compte de stockage. Il n’est pas possible d’imbriquer un conteneur sous un autre.

L’exemple suivant permet de créer un conteneur asynchrone :

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Créer le conteneur racine

Un conteneur racine sert de conteneur par défaut pour votre compte de stockage. Chaque compte de stockage peut avoir un conteneur racine, qui doit être nommé *$root*. Vous devez explicitement créer ou supprimer le conteneur racine.

Vous pouvez référencer un objet BLOB stocké dans le conteneur racine sans inclure le nom du conteneur racine. Le conteneur racine vous permet de référencer un objet BLOB au niveau supérieur de la hiérarchie du compte de stockage. Par exemple, vous pouvez référencer un objet BLOB qui réside dans le conteneur racine de la manière suivante:

`https://myaccount.blob.core.windows.net/default.html`

L’exemple suivant permet de créer un conteneur racine synchrone :

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Supprimer un conteneur

Pour supprimer un conteneur dans .NET, utilisez l’une des méthodes suivantes :

- [Supprimer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Les méthodes **Supprimer** et **DeleteAsync** lèvent une exception si le conteneur n’existe pas.

Les méthodes **DeleteIfNotExists** et **DeleteIfNotExistsAsync** retournent une valeur booléenne indiquant si le conteneur a été supprimé. Si le conteneur spécifié n’existe pas, ces méthodes retournent une mention **False** pour indiquer que le conteneur n’a pas été supprimé.

Après avoir supprimé un conteneur, vous ne pouvez pas créer un conteneur portant le même nom pendant au moins 30 secondes, voire plus. Lorsque le conteneur est en cours de suppression, une tentative de création d’un conteneur portant le même nom échoue avec le code d’erreur HTTP 409 (conflit). Toutes les autres opérations sur le conteneur ou les objets BLOB qu’il contient échouent avec le code d’erreur HTTP 404 (introuvable) pendant la suppression du conteneur.

L’exemple suivant supprime le conteneur spécifié et gère l’exception si le conteneur n’existe pas :

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

L’exemple suivant montre comment supprimer tous les conteneurs qui commencent par un préfixe spécifié. L’exemple interrompt le bail s’il existe déjà un bail sur le conteneur.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Voir aussi

- [Opération Créer le conteneur](/rest/api/storageservices/create-container)
- [Opération Supprimer le conteneur](/rest/api/storageservices/delete-container)
