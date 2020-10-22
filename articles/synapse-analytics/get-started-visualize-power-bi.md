---
title: 'Tutoriel : Prise en main d’Azure Synapse Analytics - Visualiser des données d’espace de travail avec Power BI'
description: Dans ce tutoriel, découvrez comment créer un espace de travail Power BI, lier votre espace de travail Azure Synapse et créer un jeu de données Power BI qui utilise les données de l’espace de travail Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: c5c41b98d52a4b8182fb162c3bfa9a08c0df5e15
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171855"
---
# <a name="visualize-data-with-power-bi"></a>Visualiser des données avec Power BI

Dans ce tutoriel, découvrez comment créer un espace de travail Power BI, lier votre espace de travail Azure Synapse et créer un jeu de données Power BI qui utilise les données de l’espace de travail Azure Synapse. 

## <a name="overview"></a>Vue d’ensemble

À partir des données NYC Taxi, nous avons créé des jeux de données agrégés dans deux tables :
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Vous pouvez lier un espace de travail Power BI à votre espace de travail Azure Synapse. Cette fonctionnalité vous permet d’accéder facilement aux données dans votre espace de travail Power BI. Vous pouvez modifier vos rapports Power BI directement dans votre espace de travail Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Création d’un espace de travail Power BI

1. Connectez-vous à [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Créez un espace de travail Power BI nommé **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Lier votre espace de travail Azure Synapse à votre nouvel espace de travail Power BI

1. Dans Synapse Studio, accédez à **Gérer** > **Services liés**.
1. Sélectionnez **Nouveau** > **Se connecter à Power BI**, puis définissez les champs suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|
    |**Nom**|**NYCTaxiWorkspace1**|
    |**Nom de l’espace de travail**|**NYCTaxiWorkspace1**|

1. Sélectionnez **Create** (Créer).

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Créer un jeu de données Power BI qui utilise des données dans votre espace de travail Azure Synapse

1. Dans Synapse Studio, accédez au hub **Développer** > **Power BI**.
1. Accédez à **NYCTaxiWorkspace1** > **Jeux de données Power BI** et sélectionnez **Nouveau jeu de données Power BI**.
1. Pointez sur la base de données **SQLDB1**, puis sélectionnez **Télécharger le fichier .pbids**.
1. Ouvrez le fichier **.pbids** téléchargé. Power BI Desktop s’ouvre et se connecte automatiquement à **SQLDB1** dans votre espace de travail Azure Synapse.
1. Si vous voyez s’afficher une boîte de dialogue appelée **Base de données SQL Server** :
    1. Sélectionnez **Compte Microsoft**.
    1. Sélectionnez **Se connecter** et connectez-vous à votre compte.
    1. Sélectionnez **Connecter**.
1. Une fois la boîte de dialogue **Navigateur** ouverte, recherchez la table **PassengerCountStats** et sélectionnez **Charger**.
1. Dans la boîte de dialogue **Paramètres de connexion** qui s’affiche, sélectionnez **DirectQuery** > **OK**.
1. Sélectionnez le bouton **Rapport** sur le côté gauche.
1. Ajoutez **Graphique en, courbes** à votre rapport.
    1. Faites glisser la colonne **PassengerCount** vers **Visualisations** > **Axe**.
    1. Faites glisser les colonnes **SumTripDistance** et **AvgTripDistance** vers **Visualisations** > **Valeurs**.
1. Sous l’onglet **Accueil**, sélectionnez **Publier**.
1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.
1. Choisissez le nom de fichier **PassengerAnalysis.pbix**, puis sélectionnez **Enregistrer**.
1. Dans **Sélectionner une destination**, choisissez **NYCTaxiWorkspace1**, puis cliquez sur **Sélectionner**.
1. Attendez la fin de la publication.

### <a name="configure-authentication-for-your-dataset"></a>Configurer l’authentification pour votre jeu de données

1. Ouvrez [powerbi.microsoft.com](https://powerbi.microsoft.com/) et sélectionnez **Se connecter**.
1. Sur le côté gauche, sous **Espaces de travail**, sélectionnez l’espace de travail **NYCTaxiWorkspace1**.
1. Dans cet espace de travail, recherchez un jeu de données appelé **Passenger Analysis** et un rapport appelé **Passenger Analysis**.
1. Pointez sur le jeu de données **PassengerAnalysis**, sélectionnez les points de suspension (...), puis sélectionnez **Paramètres**.
1. Dans **Informations d’identification de la source de données**, définissez la **méthode d’authentification** sur **OAuth2**, puis sélectionnez **Se connecter**.

### <a name="edit-a-report-in-synapse-studio"></a>Modifier un rapport dans Synapse Studio

1. Retournez dans Synapse Studio et sélectionnez **Fermer et actualiser**.
1. Accédez au hub **Développer**.
1. Pointez sur **Power BI**, puis sélectionnez l’icône pour actualiser le nœud **Rapports Power BI**.
1. Sous **Power BI** vous devez normalement voir :
    * Sous **NYCTaxiWorkspace1** > **Jeux de données Power BI**, un nouveau jeu de données appelé **PassengerAnalysis**.
    * Sous **NYCTaxiWorkspace1** > **Rapports Power BI**, un nouveau rapport appelé **PassengerAnalysis**.
1. Sélectionnez le rapport **PassengerAnalysis**. Le rapport s’ouvre. Vous pouvez le modifier directement dans Synapse Studio.

## <a name="monitor-activities"></a>Activités d'analyse

1. Dans Synapse Studio, accédez au hub **Superviser**.
1. À cet emplacement, vous pouvez voir un historique de toutes les activités qui se produisent dans l’espace de travail et celles qui sont actuellement actives.
1. Explorez les activités **Exécutions du pipeline**, **Applications Apache Spark** et **Requêtes SQL** pour voir tout ce que vous avez déjà fait dans l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Surveiller](get-started-monitor.md)
                                 

