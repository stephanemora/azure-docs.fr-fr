---
title: Se connecter à l’Explorateur de données Azure avec ODBC
description: Dans cette procédure, vous allez apprendre à configurer une connexion ODBC à l’Explorateur de données Azure, puis utiliser cette connexion pour visualiser les données avec un Tableau.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: ad00ad247b047d4acf97eb5d0e96229949181ecf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740421"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Se connecter à l’Explorateur de données Azure avec ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) est une interface de programmation d’application largement acceptées (API) pour l’accès de base de données. Utiliser ODBC pour se connecter à l’Explorateur de données Azure à partir d’applications qui n’ont pas un connecteur dédié.

Dans les coulisses, applications appellent des fonctions dans l’interface ODBC, qui sont implémentées dans des modules spécifiques à la base de données appelées *pilotes*. Explorateur de données Azure prend en charge un sous-ensemble du protocole de communication de SQL Server ([MS-TDS](/azure/kusto/api/tds/)) ; par conséquent, il peut utiliser le pilote ODBC pour SQL Server.

Dans cet article, vous allez apprendre à utiliser le pilote ODBC de SQL Server, pour vous connecter à l’Explorateur de données Azure à partir de n’importe quelle application qui prend en charge ODBC. Vous pouvez ensuite si vous le souhaitez vous connecter à l’Explorateur de données Azure à partir du Tableau et importer des données à partir d’un cluster de l’exemple.

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce guide pratique, vous avez besoin des éléments suivants :

* [Microsoft ODBC Driver for SQL Server version 17.2.0.1 ou version ultérieure](/sql/connect/odbc/download-odbc-driver-for-sql-server) pour votre système d’exploitation.

* Si vous souhaitez suivre notre exemple de Tableau, vous devez également :

  * Tableau Desktop, complète ou [d’évaluation](https://www.tableau.com/products/desktop/download) version.

  * Un cluster qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Configurer la source de données ODBC

Suivez ces étapes pour configurer une source de données ODBC à l’aide du pilote ODBC pour SQL Server.

1. Dans Windows, recherchez *Sources de données ODBC*, puis ouvrez l’application de bureau de Sources de données ODBC.

1. Sélectionnez **Ajouter**.

    ![Ajouter une source de données](media/connect-odbc/add-data-source.png)

1. Sélectionnez **ODBC Driver 17 for SQL Server** puis **Terminer**.

    ![Sélection du pilote](media/connect-odbc/select-driver.png)

1. Entrez un nom et une description pour la connexion et le cluster que vous souhaitez vous connecter, puis sélectionnez **suivant**. L’URL doit être sous la forme de cluster  *\<ClusterName\>.\< Région\>. kusto.windows.net*.

    ![Sélectionner un serveur](media/connect-odbc/select-server.png)

1. Sélectionnez **intégrées à Active Directory** puis **suivant**.

    ![Active Directory intégré](media/connect-odbc/active-directory-integrated.png)

1. Sélectionnez la base de données avec les exemples de données puis **suivant**.

    ![Modifier la base de données par défaut](media/connect-odbc/change-default-database.png)

1. Dans l’écran suivant, laissez toutes les options comme valeur par défaut est puis sélectionnez **Terminer**.

1. Sélectionnez **Source de données de Test**.

    ![Source de données de test](media/connect-odbc/test-data-source.png)

1. Vérifiez que le test a réussi puis sélectionnez **OK**. Si le test a échoué, vérifiez les valeurs que vous avez spécifié dans les étapes précédentes et que vous disposez des autorisations suffisantes pour se connecter au cluster.

    ![Test a réussi](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Visualiser les données dans le Tableau (facultatif)

Maintenant vous avez terminé la configuration d’ODBC, vous pouvez importer des exemples de données dans le Tableau.

1. Dans le Tableau Desktop, dans le menu de gauche, sélectionnez **autres bases de données (ODBC)**.

    ![Se connecter à ODBC](media/connect-odbc/connect-odbc.png)

1. Pour **DSN**, sélectionnez la source de données que vous avez créé pour ODBC, puis sélectionnez **Sign In**.

    ![Connexion dans ODBC](media/connect-odbc/odbc-sign-in.png)

1. Pour **base de données**, sélectionnez la base de données sur votre cluster de l’exemple, tel que *TestDatabase*. Pour **schéma**, sélectionnez *dbo*et pour **Table**, sélectionnez le *StormEvents* exemple de table.

    ![Sélectionnez la base de données et de table](media/connect-odbc/select-database-table.png)

1. Tableau affiche maintenant le schéma pour les exemples de données. Sélectionnez **mise à jour maintenant** pour importer les données dans le Tableau.

    ![Mettre à jour des données](media/connect-odbc/update-data.png)

    Lorsque les données sont importées, Tableau affiche des lignes de données similaires à l’image suivante.

    ![Jeu de résultats](media/connect-odbc/result-set.png)

1. Vous pouvez désormais créer des visualisations dans le Tableau en fonction des données importées à partir de l’Explorateur de données Azure. Pour plus d’informations, consultez [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)

[Tutoriel : Visualiser des données Azure Data Explorer dans Power BI](visualize-power-bi.md)