---
title: Utiliser l’ingestion en un clic pour ingérer des données dans Azure Data Explorer
description: Découvrez comment ingérer (charger) des données dans Azure Data Explorer simplement en utilisant l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69519828"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données dans Azure Data Explorer

Cet article explique comment utiliser l’ingestion en un clic pour ingérer rapidement une nouvelle table au format JSON ou CSV du stockage dans Azure Data Explorer. Une fois les données ingérées, vous pouvez modifier la table et exécuter des requêtes à l’aide de l’interface utilisateur web.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Connectez-vous à [l’application](https://dataexplorer.azure.com/).
* Créer [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)
* Source de données dans le stockage Azure.

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Cliquez avec le bouton droit sur le *nom de la base de données* et sélectionnez **Ingérer de nouvelles données (préversion)**

    ![sélectionner l’ingestion en un clic dans l’interface utilisateur web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Dans la fenêtre **Ingestion de données (préversion)** , sous l’onglet **Source**, renseignez les **Détails du projet** :

    * Entrez un nouveau **Nom de table**. 
    * Sélectionnez le **Type d’ingestion** > **À partir du stockage**.
    * Entrez le **Lien vers le stockage** avec l’URL du stockage. Utilisez l’URL SAS d’objet blob pour les comptes de stockage privés. 
    * Sélectionnez **Modifier le schéma**
 
    ![détails de la source d’ingestion en un clic](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. Sous l'onglet **Schéma**, sélectionnez **Format de données** dans la liste déroulante > **JSON** ou **CSV**. 
   
   Si vous sélectionnez **CSV** :
    * Sélectionnez la case à cocher **Ignorer la ligne d’en-tête** pour ignorer la ligne d’en-tête du fichier CSV.    
    * Le **Nom de mappage** est défini automatiquement, mais il peut être modifié.

    ![schéma de format csv d’ingestion en un clic.png](media/ingest-data-one-click/one-click-csv-format.png)

   Si vous sélectionnez **JSON** :
    * Sélectionnez les **niveaux JSON** : 1-10 dans la liste déroulante. Les niveaux dans le fichier JSON sont affichés dans le tableau en bas à droite. 
    * Le **Nom de mappage** est défini automatiquement, mais il peut être modifié.

    ![schéma de format JSON d’ingestion en un clic](media/ingest-data-one-click/one-click-json-format.png)  

1. Dans **l'éditeur**, sélectionnez **V** à droite pour ouvrir l’éditeur. Dans l’éditeur, vous pouvez afficher et copier les requêtes automatiques générées à partir de vos entrées. 

1.  Dans le tableau en bas à droite : 
    * Sélectionner **V** à droite de la colonne pour **Renommer la colonne**, **Supprimer la colonne**, **Trier par ordre croissant** ou **Trier par ordre décroissant**
    * Double-cliquez sur le nom de la colonne à modifier.
    * Sélectionnez l’icône à gauche du nom de la colonne pour modifier le type de données. 

1. Sélectionnez **Démarrer l’ingestion** pour créer une table, créer un mappage et commencer l’ingestion de données.
 
## <a name="query-data"></a>Données de requête

1. Dans la fenêtre **Ingestion de données terminée**, les trois étapes sont signalées par des coches vertes si l’ingestion des données s’est déroulée correctement. 
 
    ![ingestion de données en un clic terminée](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Sélectionnez **V** pour ouvrir la requête. Copiez la requête dans l’interface utilisateur web pour la modifier.

1. Le menu à droite contient des **Requêtes rapides** et des **Outils rapides**. 

    * Les **requêtes rapides** incluent des liens vers l’interface utilisateur web avec des exemples de requêtes.
    * Les **Outils** incluent un lien vers l’interface utilisateur web avec des **commandes Drop** qui vous permettent de résoudre les problèmes en exécutant la commande `.drop` appropriée.

    > [!TIP]
    > Des données peuvent être perdues avec les commandes `.drop`. Utilisez-les avec précaution.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)
