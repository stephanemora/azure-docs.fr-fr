---
title: Visualiser les données avec Power BI Microsoft Azure
description: Visualiser les données SQL Data Warehouse avec Power BI
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350943"
---
# <a name="visualize-data-with-power-bi"></a>Visualiser des données avec Power BI
Ce didacticiel vous montre comment utiliser Power BI pour vous connecter SQL Data Warehouse et créer quelques visualisations de base.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Prérequis
Pour exécuter pas à pas ce didacticiel, vous avez besoin des éléments suivants :

* Un SQL Data Warehouse préchargé avec la base de données AdventureWorksDW. Pour provisionner un entrepôt de données, consultez [Créer un entrepôt de données SQL](create-data-warehouse-portal.md) et chargez l’exemple de données. Si vous disposez déjà d’un entrepôt de données, mais pas d’un exemple de données, vous pouvez [charger WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Connectez-vous à votre base de données
Pour ouvrir Power BI et vous connecter à votre base de données AdventureWorksDW :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Bases de données SQL** et choisissez votre base de données SQL Data Warehouse AdventureWorks.
   
    ![Recherche de votre base de données](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Cliquez sur le bouton Open in Power BI.
   
    ![Bouton Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. La page de connexion de la base de données SQL Data Warehouse devrait afficher l’adresse web de votre base de données. Cliquez sur Suivant.
   
    ![Connexion Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Entrez le nom d’utilisateur et le mot de passe du serveur SQL Azure.
   
    ![Connexion à Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Pour ouvrir la base de données, cliquez sur le jeu de données AdventureWorksDW dans le panneau gauche.
   
    ![Ouvrir AdventureWorksDW Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Créer un rapport
Vous êtes maintenant prêt à utiliser Power BI pour analyser vos exemples de données AdventureWorksDW. Pour effectuer l’analyse, AdventureWorksDW dispose d’une vue appelée AggregateSales. Cette vue contient quelques-unes des métriques essentielles à l’analyse des ventes de l’entreprise.

1. Pour créer une correspondance entre montant des ventes et code postal, dans le panneau de champs de droite, cliquez sur la vue AggregateSales afin de l’agrandir. Cliquez sur les colonnes PostalCode et SalesAmount pour les sélectionner.
   
    ![Power BI sélectionne AggregateSales.](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI reconnaît automatiquement les données géographiques et les intègre dans une carte.
   
    ![Carte Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Cette étape crée un graphique à barres qui affiche le montant des ventes en fonction du revenu client. Pour créer l’histogramme, accédez à la vue AggregateSales développée. Cliquez sur le champ SalesAmount. Faites glisser le champ Revenu du client sur la gauche et déposez-le dans Axe.
   
    ![Power BI sélectionne l’axe.](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Histogramme à gauche.
   
    ![Barre Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Cette étape crée un graphique en courbes qui indique le montant des ventes par date de commande. Pour créer le graphique en courbes, accédez à la vue AggregateSales développée. Cliquez sur SalesAmount et OrderDate. Dans la colonne Visualisations, cliquez sur l’icône de graphique en courbes, autrement dit la première icône de la deuxième ligne sous les visualisations.
   
    ![Power BI sélectionne le graphique en courbes.](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Vous disposez maintenant d’un rapport qui affiche trois présentations différentes des données.
   
    ![Ligne Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Vous pouvez enregistrer votre progression à tout moment en cliquant sur **Fichier**, puis en sélectionnant **Enregistrer**.

## <a name="using-direct-connect"></a>Utilisation de la connexion directe
Comme avec Azure SQL Database, la fonction de connexion directe de SQL Data Warehouse permet un pushdown logique, en complément des fonctionnalités analytiques de Power BI. Grâce à la connexion directe, les requêtes sont renvoyées vers votre solution Azure SQL Data Warehouse en temps réel lorsque vous explorez les données.  Combinée à la puissance de SQL Data Warehouse, cette fonctionnalité vous permet de créer des rapports dynamiques en quelques minutes pour plusieurs téraoctets de données. En outre, l’introduction du bouton Open in Power BI permet aux utilisateurs de connecter directement Power BI à leur solution SQL Data Warehouse sans collecter d’informations à partir d’autres sections d’Azure.

Quand vous utilisez la fonction de connexion directe :

* Spécifiez le nom complet du serveur lors de la connexion.
* Vérifiez que les règles de pare-feu relatives à la base de données sont configurées de sorte à autoriser l’accès aux services Azure.
* Chaque action, telle que la sélection d’une colonne ou l’ajout d’un filtre, interroge directement l’entrepôt de données.
* Les vignettes sont actualisées automatiquement toutes les 15 minutes environ.
* Le composant Questions et réponses n’est pas disponible pour les jeux de données de la fonction de connexion directe.
* Les modifications du schéma sont automatiquement incorporées.
* Toutes les requêtes de la fonction de connexion directe expirent au bout de 2 minutes.

Ces restrictions et remarques sont susceptibles de changer à mesure que les expériences s’améliorent.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous êtes familiarisé avec les exemples de données, vous pouvez découvrir les opérations de [développement](sql-data-warehouse-overview-develop.md) ou de [chargement](design-elt-data-loading.md). Vous pouvez également consulter le [site web de Power BI](https://www.powerbi.com/).
