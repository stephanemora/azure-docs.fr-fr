---
title: Utilisation d’Azure Stream Analytics
description: Conseils d’utilisation d’Azure Stream Analytics avec votre entrepôt de données dans Azure Synapse pour développer des solutions en temps réel.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350437"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Utiliser Azure Stream Analytics avec Azure Synapse Analytics

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Pour découvrir les principes de base de ce service, voir l’article [Présentation d’Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md). Vous pouvez ensuite apprendre à créer une solution de bout en bout avec Stream Analytics en suivant le didacticiel [Prise en main d’Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

Dans cet article, vous allez apprendre à utiliser votre entrepôt de données comme récepteur de sortie pour vos travaux Azure Stream Analytics.

## <a name="prerequisites"></a>Prérequis

* Travail Azure Stream Analytics : pour créer un travail Azure Stream Analytics, suivez les étapes décrites dans le tutoriel [Bien démarrer avec Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) pour les opérations suivantes :  

    1. Création d’une entrée de hub d’événements
    2. Configuration et démarrage de l’application de génération d’événements
    3. Configuration d’un travail Stream Analytics
    4. Spécification d’une entrée de travail et d’une requête
* Entrepôt de données du pool SQL Azure Synapse : pour créer un entrepôt de données, suivez les étapes décrites dans le guide de [Démarrage rapide pour créer un entrepôt de données](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Spécifier la sortie de streaming pour pointer vers votre entrepôt de données

### <a name="step-1"></a>Étape 1

Dans le portail Azure, accédez à votre travail Stream Analytics, puis cliquez sur **Sorties** dans le menu **Topologie de la tâche**.

### <a name="step-2"></a>Étape 2

Cliquez sur le bouton **Ajouter**, puis choisissez **Base de données SQL** dans le menu déroulant.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Étape 3 :

Saisissez les valeurs suivantes :

* *Alias de sortie* : entrez un nom convivial pour cette sortie de travail.
* *Abonnement*:
  * Si votre entrepôt de données se trouve dans le même abonnement que la tâche Stream Analytics, cliquez sur ***Sélectionner une base de données SQL dans vos abonnements***.
  * Si votre base de données se trouve dans un autre abonnement, cliquez sur Fournir les paramètres de base de données SQL manuellement.
* *Base de données* : Sélectionnez la base de données de destination dans la liste déroulante.
* *Nom d’utilisateur* : entrez le nom d’utilisateur d’un compte disposant d’autorisations en écriture sur la base de données.
* *Mot de passe* : indiquez le mot de passe du compte d’utilisateur spécifié.
* *Table*: spécifiez le nom de la table cible dans la base de données.
* Cliquez sur le bouton **Enregistrer**.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Étape 4

Avant de pouvoir exécuter un test, vous devez créer la table dans votre entrepôt de données.  Exécutez le script de création de table suivant avec SQL Server Management Studio (SSMS) ou l’outil de requête de votre choix.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Étape 5

Dans le portail Azure de la tâche Stream Analytics, cliquez sur le nom de votre travail.  Cliquez sur le bouton ***Test*** dans le volet ***Détails de la sortie***.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) Quand la connexion à la base de données est établie, une notification s’affiche dans le portail.

### <a name="step-6"></a>Étape 6

Cliquez sur le menu ***Requête*** sous ***Topologie de la tâche***, puis changez la requête pour insérer des données dans la sortie du flux que vous avez créée.  Cliquez sur le bouton ***Tester la requête sélectionnée*** pour tester votre requête.  Cliquez sur le bouton ***Enregistrer la requête*** quand le test de votre requête réussit.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Étape 7

Démarrez le travail Azure Stream Analytics.  Cliquez sur le bouton ***Démarrer*** dans le menu ***Vue d’ensemble***.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Cliquez sur le bouton ***Démarrer*** dans le volet de démarrage du travail.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble de l’intégration, consultez [Intégrer d’autres services](sql-data-warehouse-overview-integrate.md).
Pour plus de conseils de développement, consultez [Choix de conception et techniques de codage pour les entrepôts de données](sql-data-warehouse-overview-develop.md).
