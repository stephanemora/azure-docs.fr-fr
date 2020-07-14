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
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921393"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Utilisation d’un script SQL dans Azure Synapse Studio (préversion)

Azure Synapse Studio (préversion) fournit une interface web de script SQL qui vous permet de créer des requêtes SQL. Vous pouvez vous connecter au pool SQL (préversion) ou à SQL à la demande (préversion). 

## <a name="begin-authoring-in-sql-script"></a>Commencer à créer dans un script SQL 

Il existe plusieurs façons de démarrer l’expérience de création dans un script SQL. Vous pouvez créer un script SQL à l’aide de l’une des méthodes suivantes.

1. Dans le menu Développer, sélectionnez l’icône de **« + »** , puis choisissez **Script SQL**.

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. Dans le menu **Actions**, choisissez **Nouveau script SQL**.
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

Sinon, vous pouvez : 

3. Dans le menu **Actions**, sous Développer des scripts SQL, choisissez **Importer**, puis sélectionnez un script SQL existant dans votre stockage local.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Créer votre script SQL

1. Choisissez un nom pour votre script SQL en sélectionnant le bouton **Propriété**, puis en remplaçant le nom par défaut attribué au script.
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. Choisissez le pool SQL spécifique ou SQL à la demande dans le menu déroulant **Se connecter à**. Ou bien, si nécessaire, choisissez la base de données dans **Utiliser la base de données**.
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. Commencez à créer votre script SQL à l’aide de la fonctionnalité intellisense.

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Exécuter votre script SQL

Sélectionnez le bouton **Exécuter** pour exécuter votre script SQL. Par défaut, les résultats sont affichés dans une table.

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exporter vos résultats

Vous pouvez exporter les résultats vers votre stockage local dans différents formats (CSV, Excel, JSON, XML) en sélectionnant « Exporter les résultats », puis en choisissant l’extension.

Vous pouvez également visualiser les résultats du script SQL dans un graphique en sélectionnant le bouton **Graphique**. Sélectionnez le « Type de graphique » et la **Colonne Catégorie**. Vous pouvez exporter le graphique dans une image en sélectionnant **Enregistrer en tant qu’image**. 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explorer les données d’un fichier Parquet

Vous pouvez explorer des fichiers Parquet dans un compte de stockage à l’aide du script SQL pour obtenir un aperçu de leur contenu.

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tables SQL, tables externes, affichages

En sélectionnant le menu **Actions** sous les données, vous pouvez choisir plusieurs actions telles que :

- Nouveau script SQL
- Sélectionner les 1000 lignes du haut
- CREATE
- DROP et CREATE 
 
Explorez les mouvements disponibles en cliquant avec le bouton droit sur les nœuds du pool SQL et de SQL à la demande.
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’un script SQL, consultez [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
