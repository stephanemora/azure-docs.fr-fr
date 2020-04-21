---
title: Scripts SQL dans Azure Synapse Studio (préversion)
description: Introduction aux scripts SQL Azure Synapse Studio (préversion)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427397"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Utilisation d’un script SQL dans Azure Synapse Studio (préversion)

Azure Synapse Studio (préversion) fournit une interface web de script SQL qui vous permet de créer des requêtes SQL. Vous pouvez vous connecter au pool SQL (préversion) ou à SQL à la demande (préversion). 

## <a name="begin-authoring-in-sql-script"></a>Commencer à créer dans un script SQL 

Il existe plusieurs façons de démarrer l’expérience de création dans un script SQL. Vous pouvez créer un script SQL à l’aide de l’une des méthodes suivantes.

1. Sélectionnez l’icône « + », puis choisissez un script SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Dans le menu Actions, sous Développer des scripts SQL, choisissez « Nouveau script SQL ». 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

or 

3. Dans le menu Actions, sous Développer des scripts SQL, choisissez « Importer », puis sélectionnez un script SQL existant dans votre stockage local.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Créer votre script SQL

1. Choisissez un nom pour votre script SQL en sélectionnant le bouton « Propriété », puis en remplaçant le nom par défaut attribué au script.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Choisissez le pool SQL spécifique ou SQL à la demande dans le menu déroulant « Se connecter à ». Ou bien, si nécessaire, choisissez la base de données dans « Utiliser la base de données ».

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Commencez à créer votre script SQL à l’aide de la fonctionnalité intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Exécuter votre script SQL

Sélectionnez le bouton « Exécuter » pour exécuter votre script SQL. Par défaut, les résultats sont affichés dans une table.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exporter vos résultats

Vous pouvez exporter les résultats vers votre stockage local dans différents formats (CSV, Excel, JSON, XML) en sélectionnant « Exporter les résultats », puis en choisissant l’extension.

Vous pouvez également visualiser les résultats du script SQL dans un graphique en sélectionnant le bouton « Graphique ». Sélectionnez le « Type de graphique » et la « Colonne Catégorie ». Vous pouvez exporter le graphique dans une image en sélectionnant « Enregistrer en tant qu’image ». 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explorez les données d’un fichier Parquet.

Vous pouvez explorer des fichiers Parquet dans un compte de stockage à l’aide du script SQL pour obtenir un aperçu de leur contenu. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tables SQL, tables externes, affichages

En sélectionnant le menu « Actions » sous les données, vous pouvez choisir plusieurs actions telles que : « New SQL script », « Select TOP 1000 rows », « CREATE », « DROP and CREATE ». Explorez les mouvements disponibles en cliquant avec le bouton droit sur les nœuds du pool SQL et de SQL à la demande.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’un script SQL, consultez [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).