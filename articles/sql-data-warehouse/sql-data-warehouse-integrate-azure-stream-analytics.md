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
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685717"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utiliser Azure Stream Analytics avec SQL Data Warehouse
Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Pour découvrir les principes de base de ce service, voir l’article [Présentation d’Azure Stream Analytics][Introduction to Azure Stream Analytics]. Vous pouvez ensuite apprendre à créer une solution de bout en bout avec Stream Analytics en suivant le didacticiel [Prise en main d’Azure Stream Analytics][Get started using Azure Stream Analytics] .

Dans cet article, vous allez apprendre à utiliser votre base de données Azure SQL Data Warehouse à la façon d’un récepteur de sortie pour vos travaux Stream Analytics.

## <a name="prerequisites"></a>Prérequis
Tout d’abord, exécutez les étapes suivantes dans le didacticiel [Prise en main d’Azure Stream Analytics][Get started using Azure Stream Analytics] .  

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

![][server-name]

* *Nom d’utilisateur* : entrez le nom d’utilisateur d’un compte disposant d’autorisations en écriture sur la base de données.
* *Mot de passe* : indiquez le mot de passe du compte d’utilisateur spécifié.
* *Table*: spécifiez le nom de la table cible dans la base de données.

![][add-database]

### <a name="step-4"></a>Étape 4
Cliquez sur la coche pour ajouter cette sortie du travail et pour vérifier que Stream Analytics peut se connecter à la base de données.

Dès que la connexion à la base de données est établie, une notification s’affiche dans le portail. Vous pouvez cliquez sur Tester pour tester la connexion à la base de données.

## <a name="next-steps"></a>Étapes suivantes
Pour consulter une vue d’ensemble de l’intégration, accédez à la rubrique [Vue d’ensemble sur l’intégration de SQL Data Warehouse][SQL Data Warehouse integration overview].

Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
