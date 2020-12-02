---
title: Utilisation d’Azure Stream Analytics
description: Conseils d’utilisation d’Azure Stream Analytics avec votre entrepôt de données dans Azure Synapse pour développer des solutions en temps réel.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 3ead3393218255808eb67983251fcf9f2561c82c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020178"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Utiliser Azure Stream Analytics avec Azure Synapse Analytics

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Pour découvrir les principes de base de ce service, voir l’article [Présentation d’Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Vous pouvez ensuite apprendre à créer une solution de bout en bout avec Stream Analytics en suivant le didacticiel [Prise en main d’Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

Dans cet article, vous allez apprendre à utiliser votre entrepôt de données en tant que récepteur de sortie pour l'ingestion de données à haut débit avec les travaux Azure Stream Analytics.

## <a name="prerequisites"></a>Prérequis

* Travail Azure Stream Analytics : pour créer un travail Azure Stream Analytics, suivez les étapes décrites dans le tutoriel [Bien démarrer avec Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pour les opérations suivantes :  

    1. Création d’une entrée de hub d’événements
    2. Configuration et démarrage de l’application de génération d’événements
    3. Configuration d’un travail Stream Analytics
    4. Spécification d’une entrée de travail et d’une requête
* Pool SQL Synapse dédié pour votre entrepôt de données : pour créer un entrepôt de données, suivez les étapes décrites dans le guide de [Démarrage rapide pour créer un entrepôt de données](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Spécifier la sortie de streaming pour pointer vers votre entrepôt de données

### <a name="step-1"></a>Étape 1

Dans le portail Azure, accédez à votre travail Stream Analytics, puis cliquez sur **Sorties** dans le menu **Topologie de la tâche**.

### <a name="step-2"></a>Étape 2

Cliquez sur le bouton **Ajouter**, puis choisissez **Azure Synapse Analytics** dans le menu déroulant.

![Choisir Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Étape 3 :

Saisissez les valeurs suivantes :

* *Alias de sortie* : entrez un nom convivial pour cette sortie de travail.
* *Abonnement*:
  * Si votre entrepôt de données est associé au même abonnement que la tâche Stream Analytics, cliquez sur ***Sélectionner Azure Synapse Analytics dans vos abonnements** _.
  _Si votre entrepôt de données est associé à un autre abonnement, cliquez sur Fournir les paramètres Azure Synapse Analytics manuellement.
* *Base de données* : Sélectionnez la base de données de destination dans la liste déroulante.
* *Nom d’utilisateur* : entrez le nom d’utilisateur d’un compte disposant d’autorisations en écriture sur la base de données.
* *Mot de passe* : indiquez le mot de passe du compte d’utilisateur spécifié.
* *Table*: spécifiez le nom de la table cible dans la base de données.
* Cliquez sur le bouton **Enregistrer**.

![Formulaire Azure Synapse Analytics complété](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

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

Dans le portail Azure de la tâche Stream Analytics, cliquez sur le nom de votre travail.  Cliquez sur le bouton **_Test_* _ dans le volet _*_Détails de la sortie_*_.

![Bouton Test du volet Détails de la sortie](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) Lorsque la connexion à la base de données est établie, une notification s'affiche sur le portail.

### <a name="step-6"></a>Étape 6

Cliquez sur le menu _*_Requête_*_ sous _*_Topologie de la tâche_*_, puis changez la requête pour insérer des données dans la sortie du flux que vous avez créée.  Cliquez sur le bouton _*_Tester la requête sélectionnée_*_ pour tester votre requête.  Cliquez sur le bouton _*_Enregistrer la requête_*_ quand le test de votre requête réussit.

![Enregistrer la requête](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Étape 7

Démarrez le travail Azure Stream Analytics.  Cliquez sur le bouton _*_Démarrer_*_ dans le menu _*_Vue d’ensemble_*_.

![Démarrer une tâche Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Cliquez sur le bouton _ *_Démarrer_** dans le volet de démarrage du travail.

![Cliquez sur Démarrer.](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble de l’intégration, consultez [Intégrer d’autres services](sql-data-warehouse-overview-integrate.md).
Pour plus de conseils de développement, consultez [Choix de conception et techniques de codage pour les entrepôts de données](sql-data-warehouse-overview-develop.md).
