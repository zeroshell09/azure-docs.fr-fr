---
title: Résolution des problèmes de l’outil Azure Import-Export | Microsoft Docs
description: Découvrez certains des problèmes courants que observés avec l’outil Azure Import-Export et comment les gérer.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320446"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Résolution des problèmes associés à l’outil d’importation/d’exportation Azure
L’outil Microsoft Azure Import/Export renvoie des messages d’erreur s’il rencontre des problèmes. Cette rubrique répertorie certains des problèmes courants que les utilisateurs peuvent rencontrer.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Une session de copie échoue, que faire ?  
 Lorsqu’une session de copie échoue, il existe deux possibilités :  
  
 Si une nouvelle tentative est possible, par exemple si le partage réseau était déconnecté pendant une courte période et qu’il est de nouveau en ligne, vous pouvez reprendre la session de copie. Si l’erreur n’est pas récupérable, par exemple si vous avez spécifié un mauvais répertoire de fichier source dans les paramètres de ligne de commande, vous devez abandonner la session de copie. Consultez la page [Préparer des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import-v1.md) pour plus d’informations sur la reprise et l’abandon de sessions de copie.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Je n’arrive pas à reprendre ou à abandonner une session de copie.  
 S’il s’agit de la première session de copie d’un lecteur, le message d’erreur doit indiquer : « La première session de copie ne peut pas être reprise ou abandonnée ». Dans ce cas, vous pouvez supprimer l’ancien fichier journal et réexécuter la commande.  
  
 Si la session de copie n’est pas la première du lecteur, elle peut toujours être reprise ou abandonnée.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>J’ai perdu le fichier journal, est-il quand même possible de créer le travail ?  
 Le fichier journal d’un lecteur contient les informations complètes de copie des données sur ce lecteur ; il est nécessaire pour ajouter d’autres fichiers au lecteur et sera utilisé pour créer un travail d’importation. Si le fichier journal est perdu, vous devrez répéter toutes les sessions de copie du lecteur.  
  
## <a name="next-steps"></a>Étapes suivantes
 
* [Configuration de l’outil Azure Import/Export](../storage-import-export-tool-setup-v1.md)   
* [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Consultation de l’état du travail avec les fichiers journaux de copie](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Réparation d’un travail d’importation](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Réparation d’un travail d’exportation](../storage-import-export-tool-repairing-an-export-job-v1.md)
