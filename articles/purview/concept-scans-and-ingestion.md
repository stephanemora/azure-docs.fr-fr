---
title: Analyses et ingestion
description: Cet article explique les analyses et l’ingestion dans Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 42162519e9e8f3835498d8955adbd7c254775dd9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211712"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Analyses et ingestion dans Azure Purview

Cet article fournit une vue d’ensemble de la fonctionnalité d’analyse et d’ingestion dans Azure Purview. Ces fonctionnalités connectent votre compte Purview à vos sources pour alimenter la carte de données et le catalogue de données afin que vous puissiez commencer à explorer et à gérer vos données par le biais de portée.

## <a name="scanning"></a>Analyse

Une fois les sources de données [enregistrées](manage-data-sources.md) dans votre compte Purview, l'étape suivante consiste à analyser les sources de données. Le processus d’analyse établit une connexion à la source de données et capture les métadonnées techniques telles que les noms, la taille de fichier, les colonnes, etc. Il extrait également le schéma pour les sources de données structurées, applique des classifications sur les schémas et [applique des étiquettes de sensibilité si votre compte Purview est connecté à un Microsoft 365 Security and Compliance Center (SCC)](create-sensitivity-label.md). Le processus d’analyse peut être déclenché pour s’exécuter immédiatement, ou planifié pour s’exécuter régulièrement pour maintenir à jour votre compte Purview.

Pour chaque analyse, vous pouvez appliquer des personnalisations afin d’analyser uniquement les sources de données dont vous avez besoin.

### <a name="choose-an-authentication-method-for-your-scans"></a>Choisir une méthode d’authentification pour vos analyses

Purview est sécurisé par défaut. Aucun mot de passe ou secret n’est stocké directement dans Purview : vous devez donc choisir une méthode d’authentification pour vos sources. Il existe quatre façons d’authentifier votre compte Purview, mais toutes les méthodes ne sont pas prises en charge pour chaque source de données.
 - Identité managée
 - Principal de service
 - Authentification SQL
 - Clé de compte ou authentification de base

Chaque fois que cela est possible, une identité gérée est la méthode d’authentification par défaut, car elle élimine la nécessité de stocker et de gérer les informations d’identification pour les sources de données individuelles. Cela peut réduire le temps que vous et votre équipe consacrez à configurer et à résoudre les problèmes d’authentification pour les analyses. Lorsque vous activez une identité gérée pour votre compte Purview, une identité est créée dans Azure Active Directory et est liée au cycle de vie de votre compte. 

### <a name="scope-your-scan"></a>Définir la portée de votre analyse

Lorsque vous analysez une source, vous avez la possibilité d’analyser l’intégralité de la source de données ou de choisir uniquement des entités spécifiques (dossiers/tables) à analyser. Les options disponibles dépendent de la source que vous scannez et peuvent être définies pour des analyses ponctuelles et planifiées.

Par exemple, lors de la [création et de l’exécution d’une analyse pour une Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan), vous pouvez choisir les tables à analyser ou sélectionner l’intégralité de la base de données.

### <a name="scan-rule-set"></a>Ensemble de règles d’analyse

Un ensemble de règles d’analyse détermine les types d’informations qu’une analyse recherchera lorsqu’elle sera exécutée sur l’une de vos sources. Les règles disponibles dépendent du type de source que vous analysez, mais incluent des éléments tels que les [types de fichiers](sources-and-scans.md#file-types-supported-for-scanning) que vous devez analyser et les types de [classification](supported-classifications.md) dont vous avez besoin.

Il existe déjà des [ensembles de règles d'analyse système](create-a-scan-rule-set.md#system-scan-rule-sets) pour de nombreux types de sources de données, mais vous pouvez également [créer vos propres ensembles de règles d'analyse](create-a-scan-rule-set.md) pour adapter vos analyses à votre organisation.

### <a name="schedule-your-scan"></a>Planifier votre analyse

Purview vous permet de choisir entre une analyse hebdomadaire ou mensuelle à un moment précis. Les analyses hebdomadaires peuvent être appropriées pour les sources de données avec des structures activement en cours de développement ou fréquemment modifiées. La numérisation mensuelle est plus appropriée pour les sources de données qui changent rarement. Une bonne pratique consiste à collaborer avec l’administrateur de la source que vous souhaitez analyser afin d’identifier une heure à laquelle les demandes de calcul sur la source sont faibles.

### <a name="how-scans-detect-deleted-assets"></a>Comment les analyses détectent les éléments supprimés

Un catalogue Azure Purview est conscient de l’état d’un magasin de données uniquement lorsqu’il exécute une analyse. Pour que le catalogue sache si un fichier, une table ou un conteneur a été supprimé(e), il compare la sortie de la dernière analyse à la sortie de l’analyse actuelle. Par exemple, supposons que la dernière fois que vous avez analysé un compte Azure Data Lake Storage Gen2, il incluait un dossier nommé *dossier1*. Lorsque le même compte est analysé à nouveau, *dossier1* est manquant. Par conséquent, le catalogue part du principe que le dossier a été supprimé.

#### <a name="detecting-deleted-files"></a>Détection des fichiers supprimés

La logique de détection des fichiers manquants fonctionne pour plusieurs analyses effectuées par le même utilisateur et par différents utilisateurs. Supposons, par exemple, qu’un utilisateur exécute une analyse ponctuelle sur un magasin de données Data Lake Storage Gen2 sur les dossiers A, B et C. Plus tard, un autre utilisateur du même compte exécute une autre analyse ponctuelle sur les dossiers C, D et E du même magasin de données. Étant donné que le dossier C a été analysé deux fois, le catalogue le vérifie à la recherche de suppressions possibles. Toutefois, les dossiers A, B, D et E n’ont été analysés qu’une seule fois. Le catalogue ne vérifie donc pas les éventuelles ressources supprimées.

Pour conserver les fichiers supprimés hors de votre catalogue, il est important d’exécuter des analyses régulières. L’intervalle d’analyse est important, car le catalogue ne peut pas détecter les ressources supprimées tant qu’une autre analyse n’est pas exécutée. Par conséquent, si vous exécutez des analyses une fois par mois sur un magasin spécifique, le catalogue ne peut pas détecter les ressources de données supprimées dans ce magasin tant que vous n’avez pas exécuté la prochaine analyse un mois plus tard.

Lorsque vous énumérez des magasins de données volumineux comme Data Lake Storage Gen2, il existe plusieurs façons (notamment les erreurs d’énumération et les événements supprimés) de passer à côté des informations. Une analyse spécifique peut ne pas détecter lorsqu’un fichier a été créé ou supprimé. Par conséquent, sauf si le catalogue est certain qu’un fichier a été supprimé, il ne le supprime pas du catalogue. Cette stratégie signifie qu’il peut y avoir des erreurs lorsqu’un fichier qui n’existe pas dans le magasin de données analysé existe toujours dans le catalogue. Dans certains cas, il peut être nécessaire d’analyser un magasin de données deux ou trois fois avant de pouvoir détecter certaines ressources supprimées.

> [!NOTE]
> Les ressources marquées pour suppression sont supprimées après une analyse réussie. Les ressources supprimées peuvent toujours être visibles dans votre catalogue pendant un certain temps avant leur traitement et leur suppression.

## <a name="ingestion"></a>Ingestion

Les métadonnées techniques ou les classifications identifiées par le processus d’analyse sont ensuite envoyées à l’ingestion. Le processus d’ingestion est chargé de remplir la cartographie de données et est géré par Purview.  L'ingestion analyse l'entrée de l'analyse, [applique des modèles d'ensemble de ressources](concept-resource-sets.md#how-azure-purview-detects-resource-sets), renseigne les informations de [lignage](concept-data-lineage.md) disponibles, puis charge automatiquement la cartographie des données. Les ressources/schémas peuvent être découverts ou organisés uniquement une fois l’ingestion terminée. Ainsi, si votre analyse est terminée mais que vous n’avez pas vu vos ressources dans la cartographie des données ou le catalogue, vous devez attendre la fin du processus d’ingestion.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations ou pour obtenir des instructions spécifiques pour l’analyse des sources, suivez les liens ci-dessous.

* Pour comprendre les ensembles de ressources, consultez notre [article sur les ensembles de ressources](concept-resource-sets.md).
* [Comment inscrire et analyser une base de données Azure SQL Database](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
