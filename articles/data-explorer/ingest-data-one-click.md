---
title: Utiliser l’ingestion en un clic pour ingérer des données dans Azure Data Explorer
description: Découvrez comment ingérer (charger) des données dans Azure Data Explorer simplement en utilisant l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c4ee4ed81cd4cc443a8f412462a5a7f204c91898
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688186"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données dans Azure Data Explorer

Cet article explique comment utiliser l’ingestion en un clic pour ingérer rapidement une nouvelle table au format json ou csv à partir d’un fichier de stockage ou d’un fichier local dans une table existante ou une nouvelle table dans Azure Data Explorer. Utilisez l’Assistant intuitif et, au bout de quelques minutes, vos données sont ingérées, vous pouvez modifier la table et exécuter des requêtes à l’aide de l’interface utilisateur web.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Connectez-vous à [l’application](https://dataexplorer.azure.com/).
* Créer [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)
* Connectez-vous à [l’interface utilisateur web](https://dataexplorer.azure.com/) et [ajoutez une connexion à votre cluster](/azure/data-explorer/web-query-data#add-clusters)
* Source de données dans le stockage Azure.

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Cliquez avec le bouton droit sur la ligne *base de données* ou *table* dans le menu de gauche de l’interface utilisateur web, puis sélectionnez **Ingérer de nouvelles données (préversion)**

    ![sélectionner l’ingestion en un clic dans l’interface utilisateur web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Dans la fenêtre **Ingestion de nouvelles données (préversion)** , sous l’onglet **Source**, renseignez les **Détails du projet** :

    * **Table**: Sélectionnez le nom de la table existante dans la liste déroulante ou sélectionnez **Créer** pour créer une nouvelle table.
    * Sélectionnez le **Type d’ingestion** > **À partir du stockage** ou **À partir d’un fichier**.
        * Si vous avez sélectionné **à partir du stockage**, entrez le **Lien vers le stockage** pour ajouter l’URL au stockage. Utilisez [l’URL SAS d’objet blob](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) pour les comptes de stockage privés. 
        * Si vous avez sélectionné **à partir d’un fichier**, sélectionnez **Parcourir**, puis faites glisser le fichier dans la zone.
    * Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table.
 
    ![détails de la source d’ingestion en un clic](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Si vous sélectionnez **Ingérer de nouvelles données (préversion)** sur une ligne de *table*, le nom de la table sélectionnée s’affiche dans les **Détails du projet**.

1. Si vous avez sélectionné une table existante, la fenêtre **Mapper les colonnes** s’ouvre pour mapper les colonnes des données source aux colonnes de la table cible. 
    * Utilisez **Omettre la colonne** pour supprimer une colonne cible de la table. 
    * Utilisez **Nouvelle colonne** pour ajouter une nouvelle colonne à votre table. 

    ![Fenêtre Mapper les colonnes](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Dans l’onglet **Schéma** :

    * Sélectionnez **Type de compression** dans la liste déroulante > **Non compressé** ou **GZip**.
    * Sélectionnez **Format des données** dans la liste déroulante > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE** ou **PSV**. 
        * Lorsque vous sélectionnez le format **JSON**, sélectionnez **Niveaux JSON** : 1-10. Les niveaux affectent la représentation des données de colonne de la table. 
        * Si vous sélectionnez un autre format que JSON : cochez la case **Inclure les noms des colonnes** pour ignorer la ligne d’en-tête du fichier.    
    * Le **Nom de mappage** est défini automatiquement, mais il peut être modifié.
    * Si vous avez sélectionné une table existante, vous pouvez sélectionner le bouton **Mapper les colonnes** pour ouvrir la fenêtre **Mapper les colonnes**.

    ![schéma de format csv d’ingestion en un clic.png](media/ingest-data-one-click/one-click-csv-format.png)

1. Dans **l'éditeur**, sélectionnez **V** à droite pour ouvrir l’éditeur. Dans l’éditeur, vous pouvez afficher et copier les requêtes automatiques générées à partir de vos entrées. 

1.  Dans la table : 
    * Cliquez avec le bouton droit sur les nouveaux en-têtes de colonne pour **Modifier le type de données**, **Renommer la colonne**, **Supprimer la colonne**, **Trier par ordre croissant** ou **Trier par ordre décroissant**. Sur les colonnes existantes, seul le tri des données est disponible. 
    * Double-cliquez sur le nom de la nouvelle colonne à modifier.

1. Sélectionnez **Démarrer l’ingestion** pour créer une table, créer un mappage et commencer l’ingestion de données.

    ![schéma de format JSON d’ingestion en un clic](media/ingest-data-one-click/one-click-json-format.png) 
 
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
