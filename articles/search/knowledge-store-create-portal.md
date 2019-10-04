---
title: Créer une base de connaissances dans le portail Azure - Recherche Azure
description: Créez une base de connaissances Recherche Azure pour les enrichissements persistants du pipeline de recherche cognitive, à l’aide de l’Assistant Importation de données du portail Azure.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265194"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Créer une base de connaissances Recherche Azure dans le portail Azure

> [!Note]
> La base de connaissances est en préversion et ne doit pas être utilisée en production. L’[API REST Recherche Azure version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de .NET SDK pour l’instant.
>

La base de connaissances est une fonctionnalité du service Recherche Azure. Elle permet de conserver la sortie d’un pipeline enrichi par IA (intelligence artificielle) pour une analyse future ou tout autre traitement en aval. Un pipeline enrichi par IA accepte des fichiers image ou des fichiers texte non structurés, les indexe à l’aide du service Recherche Azure, applique des enrichissements par IA à partir de Cognitive Services (par exemple l’analyse d’images et le traitement du langage naturel), puis enregistre les résultats au sein d’une base de connaissances dans Stockage Azure. Vous pouvez ensuite utiliser des outils tels que Power BI ou l’Explorateur Stockage pour explorer la base de connaissances.

Dans cet article, vous allez utiliser l’Assistant Importation de données du portail Azure pour ingérer, indexer et appliquer des enrichissements par IA à un ensemble d’avis sur des hôtels. Les avis sur les hôtels sont importés dans Stockage Blob Azure, et les résultats sont enregistrés en tant que base de connaissances dans Stockage Table Azure.

Une fois que vous avez créé la base de connaissances, vous pouvez apprendre à y accéder à l’aide de l’Explorateur Stockage ou de Power BI.

## <a name="prerequisites"></a>Prérequis

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

+ [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pour stocker les exemples de données et la base de connaissances. Votre compte de stockage doit utiliser le même emplacement (par exemple US-WE) que votre service Recherche Azure. De plus, le *Type de compte* doit être *StorageV2 (v2 à usage général)* (par défaut) ou *Stockage (v1 à usage général)* .

## <a name="load-the-data"></a>Chargement des données

Chargez le fichier CSV d’avis sur les hôtels dans Stockage Blob Azure afin de le rendre accessible à un indexeur Recherche Azure et pour qu’il soit alimenté via le pipeline d’enrichissement par IA.

### <a name="create-an-azure-blob-container-with-the-data"></a>Créer un conteneur d’objets blob Azure avec les données

1. [Téléchargez les données d’avis sur les hôtels enregistrées dans un fichier CSV (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Ces données proviennent de Kaggle.com et contiennent des commentaires de clients sur les hôtels.
1. [Connectez-vous au portail Azure](https://portal.azure.com), puis accédez à votre compte de stockage Azure.
1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Pour ce faire, dans la barre de navigation de gauche correspondant à votre compte de stockage, cliquez sur **Objets blob**, puis sur **+ Conteneur** dans la barre de commandes.
1. Pour le **Nom** du nouveau conteneur, entrez `hotel-reviews`.
1. Sélectionnez un **Niveau d’accès public**. Nous avons utilisé la valeur par défaut.
1. Cliquez sur **OK** pour créer le conteneur d’objets blob Azure.
1. Ouvrez le nouveau conteneur `hotels-review`, cliquez sur **Charger**, puis sélectionnez le fichier **HotelReviews-Free.csv** que vous avez téléchargé à la première étape.

    ![Charger les données](media/knowledge-store-create-portal/upload-command-bar.png "Charger les avis sur les hôtels")

1. Cliquez sur **Charger** pour importer le fichier CSV dans Stockage Blob Azure. Le nouveau conteneur s’affiche.

    ![Créer le conteneur d’objets blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Créer le conteneur d’objets blob Azure")

### <a name="get-the-azure-storage-account-connection-string"></a>Obtenir la chaîne de connexion du compte Stockage Azure

1. Dans le portail, accédez à votre compte Stockage Azure.
1. Dans la colonne de navigation de gauche du service, cliquez sur **Clés d’accès**.
1. Sous **clé 1**, copiez et enregistrez la *Chaîne de connexion*. La chaîne commence par `DefaultEndpointsProtocol=https`. Votre nom et votre clé de compte de stockage sont incorporés dans la chaîne. Gardez cette chaîne à portée de main. Vous en aurez besoin au cours des prochaines étapes.

## <a name="create-and-run-ai-enrichments"></a>Créer et exécuter des enrichissements par intelligence artificielle

Utilisez l’Assistant Importation de données pour créer la base de connaissances. Vous allez créer une source de données, choisir des enrichissements, configurer une base de connaissances et un index, puis les exécuter.

### <a name="start-the-import-data-wizard"></a>Démarrer l’Assistant Importer des données

1. Dans le portail Azure, [Recherchez votre service de recherche](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Dans la barre de commandes, cliquez sur **Importer des données** pour démarrer l’Assistant Importation.

### <a name="connect-to-your-data-import-data-wizard"></a>Se connecter aux données (Assistant Importation de données)

Dans cette étape de l’Assistant, vous allez créer une source de données à partir du blob Azure et des données relatives aux hôtels.

1. Dans la liste **Source de données**, sélectionnez **Stockage Blob Azure**.
1. Pour **Nom**, entrez `hotel-reviews-ds`.
1. Pour **Mode d’analyse**, sélectionnez **Texte délimité**, puis cochez la case **La première ligne contient l’en-tête**. Vérifiez que le **Caractère délimiteur** est une virgule (,).
1. Entrez la **Chaîne de connexion** du service de stockage, que vous avez enregistrée à une étape précédente.
1. Pour **Nom du conteneur**, entrez `hotel-reviews`.
1. Cliquez sur **Suivant : Ajouter la recherche cognitive (facultatif)** .

      ![Créer un objet source de données](media/knowledge-store-create-portal/hotel-reviews-ds.png "Créer un objet source de données")

## <a name="add-cognitive-search-import-data-wizard"></a>Ajouter une recherche cognitive (Assistant Importation de données)

Dans cette étape de l’Assistant, vous allez créer un ensemble de compétences par enrichissement des compétences cognitives. Les compétences que nous utilisons dans cet exemple vont extraire des phrases clés et détecter le langage ainsi que les sentiments. Ces enrichissements vont être « projetés » dans une base de connaissances en tant que tables Azure.

1. Développez **Attacher Cognitive Services**. **Gratuit (enrichissements limités)** est sélectionné par défaut. Vous pouvez utiliser cette ressource, car le nombre d’enregistrements dans HotelReviews-Free.csv est de 19, et cette ressource gratuite autorise jusqu’à 20 transactions par jour.
1. Développez **Ajouter des enrichissements**.
1. Pour **Nom de l’ensemble de compétences**, entrez `hotel-reviews-ss`.
1. Pour **Champ de données source**, sélectionnez **reviews_text*.
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

1. Cliquez sur **Suivant : Personnaliser l’index cible**.

### <a name="import-data-import-data-wizard"></a>Importer des données (Assistant Importation de données)

Dans cette étape de l’Assistant, vous allez configurer un index pour d’éventuelles requêtes de recherche en texte intégral. L’Assistant va échantillonner votre source de données pour en déduire des champs et des types de données. Il vous suffit de sélectionner les attributs correspondant au comportement souhaité. Par exemple, l’attribut **Récupérable** permet au service de recherche de retourner une valeur de champ, alors que l’attribut **Possibilité de recherche** active la recherche en texte intégral sur le champ.

1. Pour **Nom de l’index**, entrez `hotel-reviews-idx`.
1. Pour les attributs, effectuez les sélections suivantes :
    + Sélectionnez **Récupérable** pour tous les champs.
    + Sélectionnez **Filtrable** et **À choix multiples** pour les champs suivants : *Sentiment*, *Language*, *Keyphrases*
    + Sélectionnez **Possibilité de recherche** pour les champs suivants : *city*, *name*, *reviews_text*, *language*, *Keyphrases*

    Votre index doit ressembler à l’image suivante. Dans la mesure où la liste est longue, tous les champs ne sont pas visibles dans l’image.

    ![Configurer un index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurer un index")

1. Cliquez sur **Suivant : Créer un indexeur**.

### <a name="create-an-indexer"></a>Créer un indexeur

Dans cette étape de l’Assistant, vous allez configurer un indexeur qui doit rassembler la source de données, l’ensemble de compétences et l’index que vous avez définis dans les étapes précédentes de l’Assistant.

1. Pour **Nom**, entrez `hotel-reviews-idxr`.
1. Pour **Planification**, conservez la valeur par défaut **Une fois**.
1. Cliquez sur **Envoyer** pour exécuter l’indexeur. Les opérations d’extraction de données, d’indexation et d’application des compétences cognitives se produisent toutes à cette étape.

### <a name="monitor-the-notifications-queue-for-status"></a>Superviser l’état de la file d’attente des notifications

1. Dans le portail Azure, supervisez le journal d’activité Notifications, et recherchez un lien d’état **Notification Recherche Azure** cliquable. L’exécution peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez enrichi vos données à l’aide de Cognitive Services et que vous avez projeté les résultats dans une base de connaissances, vous pouvez utiliser l’Explorateur Stockage ou Power BI pour explorer votre jeu de données enrichi.

Pour apprendre à explorer cette base de connaissances à l’aide de l’Explorateur Stockage, consultez la procédure pas à pas suivante.

> [!div class="nextstepaction"]
> [Voir avec l’Explorateur Stockage](knowledge-store-view-storage-explorer.md)

Pour apprendre à connecter cette base de connaissances à Power BI, consultez la procédure pas à pas suivante.

> [!div class="nextstepaction"]
> [Connexion avec Power BI](knowledge-store-connect-power-bi.md)

Si vous souhaitez répéter cet exercice ou essayer une autre procédure pas à pas d’enrichissement par IA, supprimez l’indexeur *hotel-reviews-idxr*. La suppression de l’indexeur réinitialise le compteur de transactions quotidiennes gratuites à zéro.
