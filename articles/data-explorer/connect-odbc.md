---
title: Se connecter à l’Explorateur de données Azure avec ODBC
description: Dans cet article, vous allez apprendre à configurer une connexion de la connectivité ODBC (Open Database) à l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494845"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Se connecter à l’Explorateur de données Azure avec ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) est une interface de programmation d’application largement acceptées (API) pour l’accès de base de données. Utiliser ODBC pour se connecter à l’Explorateur de données Azure à partir d’applications qui n’ont pas un connecteur dédié.

Dans les coulisses, applications appellent des fonctions dans l’interface ODBC, qui sont implémentées dans des modules spécifiques à la base de données appelées *pilotes*. Explorateur de données Azure prend en charge un sous-ensemble du protocole de communication de SQL Server ([MS-TDS](/azure/kusto/api/tds/)), donc il peut utiliser le pilote ODBC pour SQL Server.

Dans cet article, vous allez apprendre à utiliser le pilote ODBC de SQL Server, pour vous connecter à l’Explorateur de données Azure à partir de n’importe quelle application qui prend en charge ODBC.

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin des éléments suivants :

* [Microsoft ODBC Driver for SQL Server version 17.2.0.1 ou version ultérieure](/sql/connect/odbc/download-odbc-driver-for-sql-server) pour votre système d’exploitation.

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

    ![Intégrée à Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Sélectionnez la base de données avec les exemples de données puis **suivant**.

    ![Modifier la base de données par défaut](media/connect-odbc/change-default-database.png)

1. Dans l’écran suivant, laissez toutes les options comme valeur par défaut est puis sélectionnez **Terminer**.

1. Sélectionnez **Source de données de Test**.

    ![Source de données de test](media/connect-odbc/test-data-source.png)

1. Vérifiez que le test a réussi puis sélectionnez **OK**. Si le test a échoué, vérifiez les valeurs que vous avez spécifié dans les étapes précédentes et que vous disposez des autorisations suffisantes pour se connecter au cluster.

    ![Test a réussi](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à l’Explorateur de données Azure à partir du Tableau](tableau.md)