---
title: Suivi de la progression des projets de science des données - Team Data Science Process
description: Méthode de suivi de la progression d’un projet de science des données pour les responsables de groupe, les responsables d’équipe et les coordinateurs de projet spécialisés dans la science des données.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60252575"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Suivi de la progression des projets de science des données

Les responsables de groupe, d’équipe et de projet de science des données doivent suivre l’avancement de leurs projets, les tâches qui ont été effectuées et par qui, et les tâches qu’il reste à accomplir. 

## <a name="azure-devops-dashboards"></a>Tableaux de bord DevOps Azure
Si vous utilisez Azure DevOps, vous pouvez générer des tableaux de bord pour suivre les activités et les éléments de travail associés à un projet Agile donné. 

Pour plus d’informations sur la création et la personnalisation des tableaux de bord et des widgets sur Azure DevOps, consultez les ensembles d’instructions suivants :

- [Ajouter et gérer des tableaux de bord](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Ajouter des widgets à un tableau de bord](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Exemple de tableau de bord

Voici un exemple simple de tableau de bord, conçu pour effectuer le suivi des activités des sprints d’un projet de science des données Agile, ainsi que du nombre de validations pour les dépôts associés. Le panneau **supérieur gauche** affiche les informations suivantes :

- Le compte à rebours du sprint actuel 
- Le nombre de validations pour chaque dépôt au cours des 7 derniers jours
- L’élément de travail assigné à un utilisateur spécifique 

Les autres panneaux affichent le diagramme de flux cumulé, le burndown et le burnup d’un projet :

- **En bas à gauche** :  diagramme de flux cumulé indiquant la quantité de travail dans un état donné : approuvé (gris), validé (bleu) et effectué (vert).
- **En haut à droite** : burndown chart indiquant le travail restant à effectuer par rapport au temps restant.
- **En bas à droite**: burnup chart indiquant le travail qui a été effectué par rapport à la quantité de travail totale.

![dashboard](./media/track-progress/dashboard.png)

Pour obtenir une description de la génération de ces graphiques, consultez les guides de démarrage rapide et les didacticiels présentés dans [Tableaux de bord](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Étapes suivantes

Des procédures pas à pas illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. L’article [Exemples de procédures pas à pas](walkthroughs.md) les liste et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente. 
