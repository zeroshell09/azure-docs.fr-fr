---
title: Filtres dans les vues d’Azure Monitor | Microsoft Docs
description: Un filtre dans une vue d’Azure Monitor permet aux utilisateurs de filtrer les données au moyen d’une valeur d’une propriété particulière, sans modifier la vue proprement dite.  Cet article décrit comment utiliser un filtre et en ajouter un à une vue personnalisée.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551728"
---
# <a name="filters-in-azure-monitor-views"></a>Filtres dans les vues d’Azure Monitor
Un **filtre** dans une [vue d’Azure Monitor](view-designer.md) permet aux utilisateurs de filtrer les données au moyen d’une valeur d’une propriété particulière, sans modifier la vue proprement dite.  Par exemple, vous pouvez autoriser les utilisateurs de votre vue à filtrer l’affichage des données uniquement à partir d’un ordinateur ou d’un ensemble d’ordinateurs particulier.  Vous pouvez créer plusieurs filtres sur une seule et unique vue pour permettre aux utilisateurs d’effectuer un filtrage avec plusieurs propriétés.  Cet article décrit comment utiliser un filtre et en ajouter un à une vue personnalisée.

## <a name="using-a-filter"></a>Utilisation d’un filtre
Cliquez sur l’intervalle de temps des dates en haut de la vue pour ouvrir la liste déroulante où vous pouvez changer l’intervalle de temps des dates pour la vue.

![Exemple de filtre](media/view-designer-filters/filters-example-time.png)

Cliquez sur le **+** pour ajouter un filtre à l’aide des filtres personnalisés définis pour la vue. Sélectionner une valeur pour le filtre dans la liste déroulante ou tapez une valeur. Continuez à ajouter des filtres en cliquant sur le **+** . 


![Exemple de filtre](media/view-designer-filters/filters-example-custom.png)

Si vous supprimez toutes les valeurs d’un filtre, celui-ci n’est plus appliqué.


## <a name="creating-a-filter"></a>Création d’un filtre

Créez un filtre à partir de l’onglet **Filtres** lors de la [modification d’une vue](view-designer.md).  Le filtre est global pour la vue et s’applique à toutes ses parties.  

![Paramètres de filtre](media/view-designer-filters/filters-settings.png)

Le tableau suivant décrit les paramètres d’un filtre.

| Paramètre | Description |
|:---|:---|
| Nom du champ | Nom du champ utilisé pour le filtrage.  Ce champ doit correspondre au champ de résumé dans **Requête pour des valeurs**. |
| Requêtes pour des valeurs | Requête à exécuter pour remplir la liste déroulante de filtre pour l’utilisateur.  Cette requête doit utiliser [Résumer](/azure/kusto/query/summarizeoperator) ou [Distinguer](/azure/kusto/query/distinctoperator) pour fournir des valeurs uniques pour un champ particulier et cela doit correspondre au **Nom du champ**.  Vous pouvez utiliser [Tri](/azure/kusto/query/sortoperator) pour trier les valeurs affichées à l’utilisateur. |
| Tag | Nom du champ qui est utilisé dans les requêtes prenant en charge le filtre et qui s’affiche également à l’utilisateur. |

### <a name="examples"></a>Exemples

Le tableau suivant présente quelques exemples de filtres communs.  

| Nom du champ | Requêtes pour des valeurs | Tag |
|:--|:--|:--|
| Ordinateur   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Ordinateurs |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modifier des requêtes de vue

Pour qu’un filtre soit mis en vigueur, vous devez modifier les requêtes dans la vue pour filtrer avec les valeurs sélectionnées.  Si vous ne modifiez pas les requêtes dans la vue, les valeurs sélectionnées par l’utilisateur n’auront aucun effet.

La syntaxe pour l’utilisation d’une valeur de filtre dans une requête est : 

    where ${filter name}  

Par exemple, si votre vue comprend une requête qui retourne des événements et utilise un filtre nommé _Computers_, vous pouvez utiliser la requête suivante.

    Event | where ${Computers} | summarize count() by EventLevelName

Si vous avez ajouté un autre filtre nommé Severity, vous pouvez utiliser la requête suivante pour utiliser les deux filtres.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [Parties de visualisation](view-designer-parts.md) que vous pouvez ajouter à votre vue personnalisée.
