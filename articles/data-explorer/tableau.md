---
title: Connexion Open Database Connectivity (ODBC) à l’Explorateur de données Azure permet de visualiser les données de Tableau
description: Dans cet article, vous allez apprendre à utiliser une connexion de la connectivité ODBC (Open Database) pour la connexion de l’Explorateur de données Azure pour visualiser les données avec un Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499083"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualiser les données à partir de l’Explorateur de données Azure dans le Tableau

 [Tableau](https://www.tableau.com/) est une plateforme analytique visual pour business intelligence. Pour vous connecter à l’Explorateur de données Azure à partir du Tableau et importez des données d’un cluster de l’exemple, utilisez le pilote SQL Server ODBC Open Database Connectivity (). 

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin de ce qui suit pour terminer cet article :

* [Se connecter à l’Explorateur de données Azure avec ODBC](connect-odbc.md) à l’aide du pilote ODBC SQL Server, pour se connecter à l’Explorateur de données Azure à partir du Tableau. 

* Tableau Desktop, complète, ou [d’évaluation](https://www.tableau.com/products/desktop/download) version.

* Un cluster qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [créer un cluster de l’Explorateur de données Azure et de la base de données](create-cluster-database-portal.md) et [ingérer les exemples de données dans l’Explorateur de données Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualiser les données dans un Tableau 

Une fois que vous avez terminé la configuration d’ODBC, vous pouvez importer des exemples de données dans le Tableau.

1. Dans le Tableau Desktop, dans le menu de gauche, sélectionnez **autres bases de données (ODBC)** .

    ![Se connecter à ODBC](media/tableau/connect-odbc.png)

1. Pour **DSN**, sélectionnez la source de données que vous avez créé pour ODBC, puis sélectionnez **Sign In**.

    ![Connexion dans ODBC](media/tableau/odbc-sign-in.png)

1. Pour **base de données**, sélectionnez la base de données sur votre cluster de l’exemple, tel que *TestDatabase*. Pour **schéma**, sélectionnez *dbo*et pour **Table**, sélectionnez le *StormEvents* exemple de table.

    ![Sélectionnez la base de données et de table](media/tableau/select-database-table.png)

1. Tableau affiche maintenant le schéma pour les exemples de données. Sélectionnez **mise à jour maintenant** pour importer les données dans le Tableau.

    ![Mettre à jour des données](media/tableau/update-data.png)

    Lorsque les données sont importées, Tableau affiche des lignes de données similaires à l’image suivante.

    ![Jeu de résultats](media/tableau/result-set.png)

1. Vous pouvez désormais créer des visualisations dans le Tableau en fonction des données importées à partir de l’Explorateur de données Azure. Pour plus d’informations, consultez [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)