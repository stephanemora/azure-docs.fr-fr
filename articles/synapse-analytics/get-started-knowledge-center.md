---
title: 'Tutoriel : Commencer à explorer le centre des connaissances Synapse'
description: Dans ce tutoriel, découvrez comment utiliser le centre des connaissances Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209200"
---
# <a name="explore-the-synapse-knowledge-center"></a>Explorer le centre des connaissances Synapse

Dans ce tutoriel, découvrez comment utiliser le centre des connaissances Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Accès au centre des connaissances

Il existe deux façons d’accéder au centre des connaissances dans Synapse Studio :

  1. Dans le hub Accueil, près du coin supérieur droit de la page, cliquez sur **Découvrir**.
  2. Dans la barre de menu située en haut, cliquez sur **?** , puis sur **Centre des connaissances**.

Choisissez l’une des méthodes et ouvrez le **centre des connaissances**.

## <a name="overview"></a>Vue d’ensemble

Le **centre des connaissances** vous permet d’effectuer trois opérations :
* **Use samples immediately (Utiliser des exemples immédiatement)** . Si vous souhaitez avoir un exemple rapide du fonctionnement de Synapse, choisissez cette option.
* **Parcourir la galerie**. Cette option vous permet de lier des exemples de jeux de données et d’ajouter des exemples de code sous la forme de scripts SQL, de notebooks et de pipelines.
* **Tour Synapse Studio (Visiter Synapse Studio)** . Cette option vous guide dans une brève présentation des composants de base de Synapse Studio. Elle est utile si vous n’avez jamais utilisé Synapse Studio.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Exploration du stockage Blob avec un pool SQL serverless

1. Accédez au **Centre des connaissances**, puis cliquez sur **Use samples immediately** (Utiliser des exemples immédiatement).
1. Sélectionnez **Query data with SQL** (Interroger des données avec SQL).
1. Cliquez sur **Use sample** (Utiliser un exemple).
1. Un nouvel exemple de script SQL s’ouvre.
1. Faites défiler jusqu’à la première requête (lignes 28 à 32), puis sélectionnez le texte de la requête.
1. Cliquez sur Exécuter. Cette opération exécute uniquement le code que vous avez sélectionné.

## <a name="loading-more-nyc-taxi-data"></a>Chargement d’autres données sur les taxis new-yorkais
1. Accédez au **Centre des connaissances**, puis cliquez sur **Browse gallery** (Parcourir la galerie).
1. Sélectionnez l’onglet **SQL scripts** (Scripts SQL) en haut.
1. Sélectionnez l’exemple d’ingestion de données **Load the New York Taxicab dataset** (Charger le jeu de données New York Taxicab), puis cliquez sur **Continue**.
1. Sous **Pool SQL**, choisissez **Sélectionner un pool existant**, puis **SQLPOOL1**. Sélectionnez ensuite la base de données **SQLPOOL1** créée précédemment.
1. Cliquez sur **Open Script** (Ouvrir le script).
1. Un nouvel exemple de script SQL s’ouvre.
1. Cliquez sur **Exécuter**.
1. Cette opération crée plusieurs tables pour toutes les données de taxi de New York et les charge à l’aide de la commande de copie T-SQL COPY. Si vous avez créé ces tables au cours des étapes de démarrage rapide précédentes, sélectionnez et exécutez uniquement le code permettant de créer (CREATE) et de copier (COPY) des tables qui n’existent pas.

    > [!NOTE] 
    > Quand vous utilisez la galerie d’exemples pour un script SQL avec un pool SQL dédié (anciennement SQL DW), vous pouvez uniquement utiliser un pool SQL dédié (anciennement SQL DW) existant.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)
