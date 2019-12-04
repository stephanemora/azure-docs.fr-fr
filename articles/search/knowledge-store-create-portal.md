---
title: Créer une base de connaissances (préversion) dans le portail Azure
titleSuffix: Azure Cognitive Search
description: Utilisez l’Assistant Importation de données pour créer une base de connaissances où conserver le contenu enrichi persistant. Connectez-vous à une base de connaissances pour l’analyse à partir d’autres applications, ou envoyez du contenu enrichi aux processus en aval. Cette fonctionnalité est actuellement disponible en préversion publique.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: a8cc368b2949d9a65034ee4f989b8603dfa01027
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533957"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Démarrage rapide : Créer une base de connaissances Recherche cognitive Azure dans le portail Azure

> [!IMPORTANT] 
> La base de connaissances est actuellement en préversion publique. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

La base de connaissances est une fonctionnalité de la Recherche cognitive Azure qui conserve la sortie d’un pipeline de compétences cognitives en vue d’analyses ultérieures ou d’un traitement en aval. 

Un pipeline accepte les images et le texte non structuré comme contenu brut, applique l’intelligence artificielle à Cognitive Services (par exemple, pour le traitement des images et du langage naturel) et crée du contenu enrichi (nouvelles structures et informations) en sortie. L’un des artefacts physiques créés par un pipeline est une [base de connaissances](knowledge-store-concept-intro.md), à laquelle vous pouvez accéder par le biais d’outils pour analyser et explorer le contenu.

Dans ce guide de démarrage rapide, vous combinez des services et des données dans le cloud Azure pour créer une base de connaissances. Une fois que tout est prêt, vous exécutez l’Assistant **Importation de données** dans le portail pour tout récupérer. Vous affichez ensuite le contenu d’origine et le contenu généré par l’intelligence artificielle dans le portail (avec l’[Explorateur Stockage](knowledge-store-view-storage-explorer.md)).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-services-and-load-data"></a>Créer des services et charger des données

Ce guide de démarrage rapide utilise la Recherche cognitive Azure, le Stockage Blob Azure et [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pour l’IA. 

En raison de la taille réduite de la charge de travail, Cognitive Services est utilisé en arrière-plan pour traiter gratuitement jusqu’à 20 transactions par jour, quand il est appelé à partir de la Recherche cognitive Azure. Tant que vous utilisez les exemples de données que nous fournissons, vous pouvez ignorer les étapes de création ou d’attachement d’une ressource Cognitive Services.

1. [Téléchargez le fichier HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Ce fichier CSV contient des données d’avis d’hôtel (issues de Kaggle.com). Il rassemble 19 commentaires de clients relatifs à un seul hôtel. 

1. [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [recherchez un compte existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) dans votre abonnement actuel. Vous utilisez le stockage Azure pour le contenu brut à importer, mais aussi pour la base de connaissances qui est le résultat final.

   Votre compte doit remplir ces deux exigences :

   + Il doit être dans la même région que la Recherche cognitive Azure. 
   
   + Il doit être de type StorageV2 (V2 universel). 

1. Ouvrez les pages des services Blob et créez un conteneur.  

1. Cliquez sur **Télécharger**.

    ![Charger les données](media/knowledge-store-create-portal/upload-command-bar.png "Charger les avis sur les hôtels")

1. Sélectionnez le fichier **HotelReviews-Free.csv** que vous avez téléchargé à la première étape.

    ![Créer le conteneur d’objets Blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Créer le conteneur d’objets Blob Azure")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide.

Vous êtes maintenant prêt à passer à l’Assistant Importation de données.

## <a name="run-the-import-data-wizard"></a>Exécuter l’Assistant Importation de données

Dans la page Vue d’ensemble du service de recherche, cliquez sur **Importer des données** dans la barre de commandes pour créer une base de connaissances en quatre étapes.

  ![Commande Importer des données](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

1. Dans **Se connecter à vos données**, choisissez **Stockage Blob Azure**, puis sélectionnez le compte et le conteneur que vous avez créés. 
1. Pour **Nom**, entrez `hotel-reviews-ds`.
1. Pour **Mode d’analyse**, sélectionnez **Texte délimité**, puis cochez la case **La première ligne contient l’en-tête**. Vérifiez que le **Caractère délimiteur** est une virgule (,).
1. Entrez la **Chaîne de connexion** du service de stockage, que vous avez enregistrée à une étape précédente.
1. Pour **Nom du conteneur**, entrez `hotel-reviews`.
1. Cliquez sur **Suivant : Ajouter l’enrichissement par IA (facultatif)** .

      ![Créer un objet de source de données](media/knowledge-store-create-portal/hotel-reviews-ds.png "Créer un objet de source de données")

1. Passez à la page suivante.

### <a name="step-2-add-cognitive-skills"></a>Étape 2 : Ajouter des compétences cognitives

Dans cette étape de l’Assistant, vous allez créer un ensemble de compétences par enrichissement des compétences cognitives. Les compétences que nous utilisons dans cet exemple vont extraire des phrases clés et détecter le langage ainsi que les sentiments. Dans une étape ultérieure, ces enrichissements seront « projetés » dans une base de connaissances en tant que tables Azure.

1. Développez **Attacher Cognitive Services**. **Gratuit (enrichissements limités)** est sélectionné par défaut. Vous pouvez utiliser cette ressource, car le nombre d’enregistrements dans HotelReviews-Free.csv est de 19, et cette ressource gratuite autorise jusqu’à 20 transactions par jour.
1. Développez **Ajouter des compétences cognitives**.
1. Pour **Nom de l’ensemble de compétences**, entrez `hotel-reviews-ss`.
1. Pour **Champ de données source**, sélectionnez **reviews_text**.
1. Pour **Niveau de précision d’enrichissement**, sélectionnez **Pages (segments de 5 000 caractères)**
1. Sélectionnez les compétences cognitives suivantes :
    + **Extraire des expressions clés**
    + **Détecter la langue**
    + **Détecter le sentiment**

      ![Créer un ensemble de compétences](media/knowledge-store-create-portal/hotel-reviews-ss.png "Créer un ensemble de compétences")

1. Développez **Enregistrer les enrichissements dans une base de connaissances**.
1. Entrez la **Chaîne de connexion du compte de stockage**, que vous avez enregistrée à une étape précédente.
1. Sélectionnez les **projections de table Azure** suivantes :
    + **Documents**
    + **Pages**
    + **Phrases clés**

    ![Configurer la base de connaissances](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurer la base de connaissances")

1. Passez à la page suivante.

### <a name="step-3-configure-the-index"></a>Étape 3 : Configurer l’index

Dans cette étape de l’Assistant, vous allez configurer un index pour d’éventuelles requêtes de recherche en texte intégral. L’Assistant va échantillonner votre source de données pour en déduire des champs et des types de données. Il vous suffit de sélectionner les attributs correspondant au comportement souhaité. Par exemple, l’attribut **Récupérable** permet au service de recherche de retourner une valeur de champ, alors que l’attribut **Possibilité de recherche** active la recherche en texte intégral sur le champ.

1. Pour **Nom de l’index**, entrez `hotel-reviews-idx`.
1. Pour les attributs, effectuez les sélections suivantes :
    + Sélectionnez **Récupérable** pour tous les champs.
    + Sélectionnez **Filtrable** et **À choix multiples** pour les champs suivants : *Sentiment*, *Language*, *Keyphrases*
    + Sélectionnez **Possibilité de recherche** pour les champs suivants : *city*, *name*, *reviews_text*, *language*, *Keyphrases*

    Votre index doit ressembler à l’image suivante. Dans la mesure où la liste est longue, tous les champs ne sont pas visibles dans l’image.

    ![Configurer un index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurer un index")

1. Passez à la page suivante.

### <a name="step-4-configure-the-indexer"></a>Étape 4 : Configurer l’indexeur

Dans cette étape de l’Assistant, vous allez configurer un indexeur qui doit rassembler la source de données, l’ensemble de compétences et l’index que vous avez définis dans les étapes précédentes de l’Assistant.

1. Pour **Nom**, entrez `hotel-reviews-idxr`.
1. Pour **Planification**, conservez la valeur par défaut **Une fois**.
1. Cliquez sur **Envoyer** pour exécuter l’indexeur. Les opérations d’extraction de données, d’indexation et d’application des compétences cognitives se produisent toutes à cette étape.

## <a name="monitor-status"></a>Superviser l’état

L’indexation des compétences cognitives prend plus de temps que l’indexation standard basée sur du texte. L’Assistant doit ouvrir la liste de l’indexeur sur la page Vue d’ensemble afin que vous puissiez suivre la progression. Pour une navigation automatique, accédez à la page Vue d’ensemble et cliquez sur **Indexeurs**.

Dans le portail Azure, vous pouvez aussi superviser le journal d’activité Notifications, et rechercher un lien d’état **Notification Recherche cognitive Azure** cliquable. L’exécution peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez enrichi vos données à l’aide de Cognitive Services et que vous avez projeté les résultats dans une base de connaissances, vous pouvez utiliser l’Explorateur Stockage ou Power BI pour explorer votre jeu de données enrichi.

Vous pouvez afficher le contenu dans l’Explorateur Stockage, ou aller un peu plus loin avec Power BI pour obtenir des insights par le biais de la visualisation.

> [!div class="nextstepaction"]
> [Afficher avec l’Explorateur Stockage](knowledge-store-view-storage-explorer.md)
> [Se connecter à Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Si vous souhaitez répéter cet exercice ou essayer une autre procédure pas à pas d’enrichissement par IA, supprimez l’indexeur *hotel-reviews-idxr*. La suppression de l’indexeur réinitialise le compteur des transactions quotidiennes gratuites à zéro pour le traitement par Cognitive Services.
