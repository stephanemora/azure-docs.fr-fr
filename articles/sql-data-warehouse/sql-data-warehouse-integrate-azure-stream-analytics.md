---
title: Utilisation d’Azure Stream Analytics
description: Conseils sur l’utilisation d’Azure Stream Analytics avec Azure SQL Data Warehouse pour le développement de solutions.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721198"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Utiliser Azure Stream Analytics avec Azure Synapse Analytics
Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Pour découvrir les principes de base de ce service, voir l’article [Présentation d’Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Vous pouvez ensuite apprendre à créer une solution de bout en bout avec Stream Analytics en suivant le didacticiel [Prise en main d’Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

Dans cet article, vous allez apprendre à utiliser votre base de données d'entrepôt de données en tant que récepteur de sortie pour vos travaux Stream Analytics.

## <a name="prerequisites"></a>Conditions préalables requises
Tout d’abord, exécutez les étapes suivantes dans le didacticiel [Prise en main d’Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .  

1. Création d’une entrée de hub d’événements
2. Configuration et démarrage de l’application de génération d’événements
3. Configuration d’un travail Stream Analytics
4. Spécification d’une entrée de travail et d’une requête

Ensuite, créez une base de données SQL Data Warehouse.

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Spécification de la sortie du travail : Base de données Azure SQL Data Warehouse
### <a name="step-1"></a>Étape 1
En haut de la page de votre travail Stream Analytics, cliquez sur **SORTIE**, puis sur **AJOUTER**.

### <a name="step-2"></a>Étape 2
sélectionner SQL Database

### <a name="step-3"></a>Étape 3 :
Entrez les valeurs ci-dessous dans la page suivante :

* *Alias de sortie* : entrez un nom convivial pour cette sortie de travail.
* *Abonnement*:
  * Si votre base de données SQL Data Warehouse se trouve dans le même abonnement que celui de la tâche Stream Analytics, sélectionnez Utiliser la base de données SQL de l’abonnement actuel.
  * Si votre base de données se trouve dans un autre abonnement, sélectionnez Utiliser la base de données SQL d’un autre abonnement.
* *Base de données* : spécifiez le nom d’une base de données de destination.
* *Nom du serveur* : spécifiez le nom du serveur pour la base de données que vous venez d’indiquer. Vous pouvez obtenir cette information dans le portail Azure.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Nom d’utilisateur* : entrez le nom d’utilisateur d’un compte disposant d’autorisations en écriture sur la base de données.
* *Mot de passe* : indiquez le mot de passe du compte d’utilisateur spécifié.
* *Table*: spécifiez le nom de la table cible dans la base de données.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Étape 4
Cliquez sur la coche pour ajouter cette sortie du travail et pour vérifier que Stream Analytics peut se connecter à la base de données.

Dès que la connexion à la base de données est établie, une notification s’affiche dans le portail. Vous pouvez cliquez sur Tester pour tester la connexion à la base de données.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une vue d’ensemble de l’intégration, consultez [Intégrer d’autres services](sql-data-warehouse-overview-integrate.md).
Pour plus de conseils de développement, consultez [Choix de conception et techniques de codage pour les entrepôts de données](sql-data-warehouse-overview-develop.md).

