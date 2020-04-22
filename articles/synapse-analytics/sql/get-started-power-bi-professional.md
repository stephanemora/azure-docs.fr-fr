---
title: Se connecter à Power BI Professional
description: Dans ce tutoriel, nous allons voir la procédure qui permet de connecter Power BI Desktop à SQL à la demande (préversion).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425328"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Se connecter à Synapse SQL avec Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Dans ce tutoriel, nous allons voir la procédure qui permet de connecter Power BI Desktop à SQL à la demande (préversion).

## <a name="prerequisites"></a>Prérequis

Outil d’émission de requêtes :

- Client SQL de votre choix :

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop est installé

Paramètres :

| Paramètre                                 | Description                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresse de point de terminaison de service SQL à la demande    | Utilisée comme nom de serveur                                   |
| Région de point de terminaison de service SQL à la demande     | Utilisée pour déterminer le stockage que nous allons utiliser dans les exemples |
| Nom d’utilisateur et mot de passe pour l’accès au point de terminaison | Utilisés pour accéder au point de terminaison                               |
| Base de données à utiliser pour créer des vues     | Cette base de données est utilisée comme point de départ dans les exemples       |

## <a name="first-time-setup"></a>Première configuration

Deux étapes précèdent l’utilisation des exemples :

1. Créer une base de données pour vos vues
2. Créer les informations d’identification que devra utiliser SQL à la demande pour accéder aux fichiers dans le stockage

### <a name="create-database"></a>Créer une base de données

Étant donné que vous allez utiliser l’environnement de démonstration, vous devez créer votre propre base de données à des fins de démonstration. La base de données est nécessaire pour y créer des vues. Vous allez utiliser cette base de données dans certains des exemples de requêtes de cette documentation.

> [!NOTE]
> Notez que les bases de données sont utilisées uniquement pour les métadonnées de vue, et non pour les données réelles.
>
> Notez le nom de la base de données que vous utilisez, vous en aurez besoin plus tard.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Créer des informations d’identification

Nous devons créer des informations d’identification afin que vous puissiez exécuter des requêtes. Ces informations d’identification seront utilisées par le service SQL à la demande pour accéder aux fichiers dans le stockage.

> [!NOTE]
> Notez que vous devez créer des informations d’identification pour accéder au compte de stockage. Bien que SQL à la demande puisse accéder aux stockages de différentes régions, le fait d’avoir le stockage et l’espace de travail Azure Synapse dans la même région offre de meilleures performances.

**Extrait de code sur la façon de créer des informations d’identification pour les conteneurs de données de recensement**. Exécutez ce qui suit :

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Création de rapports Power BI Desktop

Ouvrez l’application Power BI Desktop et sélectionnez l’option « Obtenir les données ».
![Ouvrez l’application Power BI Desktop et sélectionnez « Obtenir les données ».](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Étape 1 : Sélectionner la source de données

Sélectionnez « Azure » dans le menu, puis « Azure SQL Database ».
![Sélectionnez la source de données.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Étape 2 : Sélectionner la base de données

Entrez l’URL de la base de données et le nom de la base de données où la vue réside.
![Sélectionnez la base de données sur le point de terminaison.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Étapes suivantes

Passez à [Interroger des fichiers de stockage](get-started-azure-data-studio.md) pour savoir comment vous connecter à SQL à la demande à l’aide d’Azure Data Studio.
 