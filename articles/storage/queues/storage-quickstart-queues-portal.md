---
title: Démarrage rapide Azure - Créer une file d’attente dans le stockage Azure à l’aide du portail Azure | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer une file d’attente, Ensuite, vous utilisez le portail Azure pour ajouter un message, en afficher les propriétés et l’enlever de la file d’attente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: d2f7b01c03b877a3dede496fc02f0d0cc9525cef
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721331"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Démarrage rapide : Créer une file d’attente et ajouter un message à l’aide du portail Azure

Ce guide de démarrage rapide explique comment utiliser le [portail Azure](https://portal.azure.com/) pour créer une file d’attente dans le stockage Azure et ajouter ou enlever des messages dans cette file d’attente.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Créer une file d’attente

Pour créer une file d’attente dans le portail Azure, effectuez les étapes suivantes :

1. Accédez à votre nouveau compte de stockage dans le portail Azure.
2. Dans le menu de gauche du compte de stockage, faites défiler jusqu’à la section **Service de File d’attente**, puis sélectionnez **Files d’attente**.
3. Sélectionnez le bouton **+ File d’attente**.
4. Entrez un nom pour votre nouvelle file d’attente. Le nom de la file d’attente doit être en minuscules et commencer par une lettre ou un chiffre. Il peut comporter uniquement des lettres, des chiffres et des tirets (-).
6. Sélectionnez **OK** pour créer la file d’attente.

    ![Capture d’écran montrant comment créer une file d’attente dans le portail Azure](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Ajouter un message

Ajoutez ensuite un message à la nouvelle file d’attente. La taille maximale d’un message est de 64 Ko.

1. Sélectionnez la nouvelle file d’attente dans la liste des files d’attente du compte de stockage.
1. Sélectionnez le bouton **+ Ajouter un message** pour ajouter un message à la file d’attente. Entrez un message dans le champ **Texte du message**. 
1. Spécifiez le délai d’expiration du message. Un message peut rester dans la file d’attente pendant une durée maximale de 7 jours.
1. Indiquez si le message doit être encodé au format Base64. L’encodage des données binaires est recommandé.
1. Sélectionnez le bouton **OK** pour ajouter le message.

    ![Capture d’écran montrant comment ajouter un message à une file d'attente](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Afficher les propriétés d’un message

Une fois que vous avez ajouté un message, le portail Azure affiche une liste de tous les messages dans la file d’attente. Vous pouvez voir l’ID du message, ainsi que le contenu, la date/l’heure d’ajout et le délai d’expiration du message. Vous pouvez également voir combien de fois ce message a été enlevé de la file d’attente.

![Capture d’écran montrant les propriétés des messages](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Enlever un message de la file d’attente

Vous pouvez enlever un message du haut de la file d’attente dans le portail Azure. Le message est alors supprimé. 

Cette opération d’enlèvement entraîne toujours la suppression du message le plus ancien dans la file d’attente. 

![Capture d’écran montrant comment enlever un message de la file d’attente dans le portail](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez vu comment créer une file d’attente, ajouter un message, afficher les propriétés d’un message et enlever un message de la file d’attente dans le portail Azure.

> [!div class="nextstepaction"]
> [Que sont les files d’attente Azure ?](storage-queues-introduction.md)
