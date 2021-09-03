---
title: Analyses et ingestion
description: Cet article explique les analyses et l’ingestion dans Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 140e87f4e03081825fe75ba73b7c5ebd33b20ada
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122527763"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Analyses et ingestion dans Azure Purview

Cet article fournit une vue d’ensemble de la fonctionnalité d’analyse et d’ingestion dans Azure Purview. Ces fonctionnalités connectent votre compte Purview à vos sources pour alimenter la carte de données et le catalogue de données afin que vous puissiez commencer à explorer et à gérer vos données par le biais de portée.

## <a name="scanning"></a>Analyse

Une fois les sources de données [enregistrées](manage-data-sources.md) dans votre compte Purview, l'étape suivante consiste à analyser les sources de données. Le processus d’analyse établit une connexion à la source de données et capture les métadonnées techniques telles que les noms, la taille de fichier, les colonnes, etc. Il extrait également le schéma pour les sources de données structurées, applique des classifications sur les schémas et [applique des étiquettes de sensibilité si votre compte Purview est connecté à un Microsoft 365 Security and Compliance Center (SCC)](create-sensitivity-label.md). Le processus d’analyse peut être déclenché pour s’exécuter immédiatement, ou planifié pour s’exécuter régulièrement pour maintenir à jour votre compte Purview.

Pour chaque analyse, vous pouvez appliquer des personnalisations afin d’analyser uniquement les sources de données dont vous avez besoin.

### <a name="scope-your-scan"></a>Définir la portée de votre analyse

Lorsque vous analysez une source, vous avez la possibilité d’analyser l’intégralité de la source de données ou de choisir uniquement des entités spécifiques (dossiers/tables) à analyser. Les options disponibles dépendent de la source que vous scannez et peuvent être définies pour des analyses ponctuelles et planifiées.

Par exemple, lors de la [création et de l’exécution d’une analyse pour une Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan), vous pouvez choisir les tables à analyser ou sélectionner l’intégralité de la base de données.

### <a name="scan-rule-set"></a>Ensemble de règles d’analyse

Un ensemble de règles d’analyse détermine les types d’informations qu’une analyse recherchera lorsqu’elle sera exécutée sur l’une de vos sources. Les règles disponibles dépendent du type de source que vous analysez, mais incluent des éléments tels que les [types de fichiers](sources-and-scans.md#file-types-supported-for-scanning) que vous devez analyser et les types de [classification](supported-classifications.md) dont vous avez besoin.

Il existe déjà des [ensembles de règles d'analyse système](create-a-scan-rule-set.md#system-scan-rule-sets) pour de nombreux types de sources de données, mais vous pouvez également [créer vos propres ensembles de règles d'analyse](create-a-scan-rule-set.md) pour adapter vos analyses à votre organisation.

## <a name="ingestion"></a>Ingestion

Les métadonnées techniques ou les classifications identifiées par le processus d’analyse sont ensuite envoyées à l’ingestion. Le processus d’ingestion est chargé de remplir la cartographie de données et est géré par Purview.  L'ingestion analyse l'entrée de l'analyse, [applique des modèles d'ensemble de ressources](concept-resource-sets.md#how-azure-purview-detects-resource-sets), renseigne les informations de [lignage](concept-data-lineage.md) disponibles, puis charge automatiquement la cartographie des données. Les ressources/schémas peuvent être découverts ou organisés uniquement une fois l’ingestion terminée. Ainsi, si votre analyse est terminée mais que vous n’avez pas vu vos ressources dans la cartographie des données ou le catalogue, vous devez attendre la fin du processus d’ingestion.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations ou pour obtenir des instructions spécifiques pour l’analyse des sources, suivez les liens ci-dessous.

* Pour comprendre les ensembles de ressources, consultez notre [article sur les ensembles de ressources](concept-resource-sets.md).
* [Comment inscrire et analyser une base de données Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
