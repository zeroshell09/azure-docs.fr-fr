---
title: Différences propres au langage de requête de journal d'Azure Monitor | Microsoft Docs
description: Informations de référence sur le langage de requête Kusto utilisé par Azure Monitor. Inclut les éléments supplémentaires spécifiques à Azure Monitor et les éléments non pris en charge dans les requêtes de journal Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 9892b8884d901033a80fae4168f25da97edb5a68
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990179"
---
# <a name="azure-monitor-log-query-language-differences"></a>Différences propres au langage de requête de journal d'Azure Monitor

Bien que les [journaux d’activité d’Azure Monitor](log-query-overview.md) reposent sur [Azure Data Explorer](/azure/data-explorer) et utilisent le même [langage de requête Kusto](/azure/kusto/query), la version du langage présente quelques différences. Cet article identifie les éléments qui diffèrent entre la version du langage utilisée pour Data Explorer et celle utilisée pour les requêtes de journal Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Éléments KQL non pris en charge dans Azure Monitor
Les sections suivantes décrivent les éléments du langage de requête de Kusto qui ne sont pas pris en charge par Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instructions non prises en charge dans Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Paramètres de requête](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Fonctions non prises en charge dans Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Opérateurs non pris en charge dans Azure Monitor

* [Jointure entre plusieurs clusters](/azure/kusto/query/joincrosscluster)
* [opérateur externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-ins non pris en charge dans Azure Monitor

* [Plug-in Python](/azure/kusto/query/pythonplugin)
* [plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Opérateurs supplémentaires dans Azure Monitor
Les opérateurs suivants prennent en charge des fonctionnalités propres à Azure Monitor et ne sont pas disponibles en dehors d'Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des références aux différentes [ressources pour l'écriture de requêtes de journal Azure Monitor](query-language.md).
- Accédez à la [documentation de référence complète du langage de requête Kusto](/azure/kusto/query/).
