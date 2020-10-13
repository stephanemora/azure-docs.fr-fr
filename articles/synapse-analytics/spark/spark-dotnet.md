---
title: Utiliser .NET pour Apache Spark
description: Découvrez comment utiliser .NET et Apache Spark pour le traitement par lots, la diffusion en continu en temps réel, l’apprentissage automatique et l’écriture de requêtes ad hoc dans des blocs-notes Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: b445787c44efca4eb6bdee3e61eb39735e5c6b14
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259898"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Utiliser .NET pour Apache Spark avec Azure Synapse Analytics

[.NET pour Apache Spark](https://dot.net/spark) fournit un support .NET gratuit, open source et multiplateforme pour Spark. 

Il fournit des liaisons .NET pour Spark qui vous permettent d’accéder aux API Spark à l’aide des langages C# et F#. .NET pour Apache Spark vous permet également d’écrire et d’exécuter des fonctions définies par l’utilisateur pour Spark écrites en .NET. Les API .NET pour Spark vous donnent accès à tous les aspects des DataFrames Spark qui vous aident à analyser vos données, dont Spark SQL, Delta Lake et Diffusion en continu structurée.

Vous pouvez analyser des données avec .NET pour Apache Spark par le biais de définitions de programmes de traitement par lots Spark ou avec des blocs-notes Azure Synapse Analytics interactifs. Cet article explique comment utiliser .NET pour Apache Spark avec Azure Synapse à l’aide des deux techniques.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Envoyer des programmes de traitement par lots à l’aide de la définition de travail Spark

Consultez le tutoriel pour apprendre à utiliser Azure Synapse Analytics pour [créer des définitions de travaux Apache Spark pour des pools Synapse Spark](apache-spark-job-definitions.md). Si vous n’avez pas empaqueté votre application pour l’envoyer à Azure Synapse, effectuez les étapes suivantes.

1. Exécutez les commandes suivantes pour publier votre application. Veillez à remplacer *mySparkApp* par le chemin d’accès de votre application.

   **Sur Windows :**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

   **Sur Linux :**

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET pour Apache Spark dans des blocs-notes Azure Synapse Analytics 

Les blocs-notes constituent une excellente option pour le prototypage de vos pipelines et scénarios .NET pour Apache Spark. Vous pouvez commencer à utiliser, comprendre, filtrer, afficher et visualiser vos données rapidement et efficacement. 

Les ingénieurs de données, chercheurs de données, analystes d’entreprise et ingénieurs d’apprentissage automatique sont tous en mesure de collaborer sur un document interactif partagé. Vous voyez les résultats immédiats de l’exploration de données et pouvez visualiser vos données dans le même bloc-notes.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Comment utiliser .NET pour les blocs-notes Apache Spark

Lorsque vous créez un bloc-notes, vous choisissez un noyau de langage pour exprimer votre logique métier. La prise en charge du noyau est disponible pour plusieurs langages, dont C#.

Pour utiliser .NET pour Apache Spark dans votre bloc-notes Azure Synapse Analytics, sélectionnez **.NET Spark (C#)** en tant que noyau, puis attachez le bloc-notes à un pool Spark existant.

Le bloc-notes .NET Spark est basé sur des expériences .NET interactives et fournit des expériences C# interactives avec la possibilité d’utiliser .NET pour Spark sans configuration avec la variable de session Spark `spark` prédéfinie.

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET pour les fonctionnalités du noyau c# Apache Spark

Lorsque vous utilisez .NET pour Apache Spark dans le bloc-notes Azure Synapse Analytics, vous disposez des fonctionnalités suivantes :

* CODE HTML déclaratif : Générez une sortie à partir de vos cellules à l’aide de la syntaxe HTML, par exemple, des en-têtes, des listes à puces, voire l’affichage d’images.
* Instructions C# simples (affectations, impression sur la console, levée d’exceptions, etc.).
* Blocs de code C# multilignes (instructions if, boucles foreach, définitions de classe, etc.).
* Accès à la bibliothèque C# standard (System, LINQ, Enumerables, etc.).
* Prise en charge de [caractéristiques du langage C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* « Spark » en tant que variable prédéfinie pour vous donner accès à votre session Apache Spark.
* Prise en charge de la définition de [fonctions .NET définies par l’utilisateur qui peuvent s’exécuter dans Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Prise en charge de la visualisation de la sortie de vos travaux Spark à l’aide de différents graphiques (en courbes, à barres, etc.) et dispositions (simple, superposée, etc.) à l’aide de la bibliothèque `XPlot.Plotly`.
* Possibilité d’inclure des packages NuGet dans votre bloc-notes C#.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation .NET pour Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET interactif](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
