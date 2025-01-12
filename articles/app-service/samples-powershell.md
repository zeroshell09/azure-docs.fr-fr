---
title: Exemples Azure PowerShell - App Service | Microsoft Docs
description: Exemples Azure PowerShell - App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 322820976f7f693ff09c071fc28f1ef3d1d472cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074077"
---
# <a name="powershell-samples-for-azure-app-service"></a>Exemples PowerShell pour Azure App Service

Le tableau suivant contient des liens vers des scripts PowerShell créés à l’aide d’Azure PowerShell.

| | |
|-|-|
|**Créer une application**||
| [Créer une application avec un déploiement à partir de GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application App Service qui extrait du code à partir de GitHub. |
| [Créer une application avec un déploiement continu à partir de GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application App Service qui déploie du code en continu à partir de GitHub. |
| [Créer une application et déployer du code avec FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service et charge des fichiers à partir d’un répertoire local via FTP. |
| [Créer une application et déployer le code à partir d’un dépôt Git local](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service et configure la transmission de code de type push à partir d’un dépôt Git local. |
| [Créer une application et déployer le code dans un environnement de préproduction](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service avec un emplacement de déploiement pour les modifications de code intermédiaires. |
|**Configurer l’application**||
| [Mapper un domaine personnalisé à une application](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application App Service et mappe un nom de domaine personnalisé à celle-ci. |
| [Lier un certificat SSL personnalisé à une application](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application App Service et lie le certificat SSL d’un nom de domaine personnalisé à celle-ci. |
|**Mettre à l’échelle une application**||
| [Mettre à l’échelle une application manuellement](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service et la met à l’échelle entre deux instances. |
| [Mettre à l’échelle une application dans le monde entier avec une architecture haute disponibilité](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée deux applications App Service dans deux régions géographiques différentes et les rend disponibles par le biais d’un point de terminaison unique à l’aide d’Azure Traffic Manager. |
|**Connecter l’application aux ressources**||
| [Connecter une application à une instance SQL Database](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application App Service et une base de données SQL, puis ajoute la chaîne de connexion de base de données aux paramètres d’application. |
| [Connecter une application à un compte de stockage](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une application App Service et un compte de stockage, puis ajoute la chaîne de connexion de stockage aux paramètres d’application. |
|**Sauvegarder et restaurer une app**||
| [Sauvegarder une application](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service et une sauvegarde unique pour celle-ci. |
| [Créer une sauvegarde planifiée pour une application](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service et une sauvegarde planifiée pour celle-ci. |
| [Supprimer une sauvegarde d’application](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Supprime une sauvegarde existante d’une application. |
| [Restaurer une application à partir d’une sauvegarde](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaure une application à partir d’une sauvegarde. |
| [Restaurer une sauvegarde dans plusieurs abonnements](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaure une application web à partir d’une sauvegarde dans un autre abonnement. |
|**Surveiller l’application**||
| [Superviser une application avec les journaux d’activité de serveur web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une application App Service, active sa journalisation et télécharge les journaux d’activité sur votre ordinateur local. |
| | |
