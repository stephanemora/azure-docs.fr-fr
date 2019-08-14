---
title: Se connecter à la base de connaissances avec Power BI - Recherche Azure
description: Créez une base de connaissances à l’aide de l’Assistant Importation de données dans le portail Azure, puis connectez-vous avec Power BI pour l’analyse et l’exploration.
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 518a96ae8ace5c9630d594fe70487635b6ec1d2c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840839"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Créer une base de connaissances Recherche Azure et se connecter à l’aide de Power BI

> [!Note]
> La base de connaissances est en préversion et ne doit pas être utilisée en production. L’[API REST Recherche Azure version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de .NET SDK pour l’instant.
>

La base de connaissances est une fonctionnalité disponible dans Recherche Azure. Elle conserve la sortie d’un pipeline enrichi par l’intelligence artificielle pour une analyse ultérieure ou un autre traitement en aval. Un pipeline enrichi grâce à l’intelligence artificielle accepte les fichiers image ou les fichiers texte non structurés d’une source de données prise en charge, les envoie à l’indexation Recherche Azure, applique des enrichissements par intelligence artificielle à partir de Cognitive Services (tels que l’analyse d’images et le traitement en langage naturel), puis enregistre les résultats dans une base de connaissances dans Stockage Azure. À partir de la base de connaissances, vous pouvez attacher des outils tels que Power BI ou l’Explorateur Stockage pour explorer les résultats.

Dans cet article, créez une base de connaissances dans le portail, puis connectez-vous et explorez à l’aide de Power Query dans Power BI Desktop. 

Cette procédure pas à pas utilise un jeu de données composé de révisions et d’évaluations de clients, d’un scoring de sentiment de Cognitive Services, puis Power Query pour interroger vos documents. Les données proviennent des données d’évaluation des hôtels sur Kaggle.com. 

## <a name="prerequisites"></a>Prérequis

+ [Téléchargez le fichier CSV d’évaluation des hôtels (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Ce jeu de données contient des enregistrements de commentaires des clients sur les hôtels.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Recherche Azure](search-create-service-portal.md). Vous pouvez utiliser un service gratuit pour cette procédure pas à pas. 

+ [Stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Vérifiez que le compte est à « usage général », soit *StorageV2 (usage général v2)* qui est la valeur par défaut, soit *Stockage (usage général v1)* . Choisissez la même région que Recherche Azure.
 
## <a name="prepare-data"></a>Préparer les données 

Chargez le fichier. csv dans Stockage Blob Azure afin qu’il soit accessible par un indexeur Recherche Azure.

1. [Connectez-vous au portail Azure](https://portal.azure.com), accédez à votre compte de stockage Azure, cliquez sur **Objets blob**, puis sur **+ Conteneur**.

1. [Créez un conteneur d’objets blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) qui doit contenir des exemples de données : 

   1. Nommez le conteneur `hotel-reviews`. 
   
   1. Définissez le niveau d’accès public sur l’une de ses valeurs valides. Nous avons utilisé la valeur par défaut.

1. Une fois le conteneur créé, ouvrez-le et sélectionnez **Charger** dans la barre de commandes.

1. Accédez au dossier contenant **HotelReviews-Free.csv**, sélectionnez le fichier, puis cliquez sur **Charger**.

   ![Charger le fichier. csv](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Charger le fichier. csv")

1. Tant que vous vous trouvez dans le stockage Azure, obtenez le nom de conteneur et la chaîne de connexion.  Vous aurez besoin de ces deux chaînes lors de la création d’un objet source de données :

   1. Dans la page Vue d’ensemble, cliquez sur **Clés d’accès** et copiez une *chaîne de connexion*. Elle commence par `DefaultEndpointsProtocol=https;` et se termine par `EndpointSuffix=core.windows.net`. Votre nom de compte et votre clé sont entre les deux. 

   1. Le nom du conteneur doit être `hotel-reviews` ou tout autre nom que vous avez choisi. 

## <a name="create-and-run-ai-enrichments"></a>Créer et exécuter des enrichissements par intelligence artificielle

Utilisez l’Assistant Importation de données pour créer la base de connaissances. Vous allez importer le jeu de données, choisir des enrichissements, configurer une base de connaissances et un index, puis les exécuter.

1. [Recherchez votre service de recherche](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans le portail Azure.

1. Cliquez sur **Importer des données** dans la barre de commandes.

1. Créez l’objet source de données dans la première page de l’Assistant.

   - Sélectionner **Stockage Blob Azure**.

   - Attribuez un nom à la source de données, par exemple *hotel-reviews-ds*.

   - Comme les données sont au format .csv, sélectionnez **Texte délimité** pour le mode d’analyse, sélectionnez **La première ligne contient l’en-tête** et assurez-vous que le caractère délimiteur est une virgule.

   - La chaîne de connexion à votre compte de stockage Azure peut être obtenue dans le portail, sous **Clés d’accès**.

   - Le nom du conteneur peut également être obtenu à partir du portail dans votre liste Azure Storage Blob.

      ![Créer un objet source de données](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Créer un objet source de données")

1. Ajoutez des enrichissements et configurez une base de connaissances dans la deuxième page de l’Assistant.

   - Dans **Attacher Cognitive Services**, vous pouvez utiliser la ressource Gratuite qui autorise jusqu’à 20 transactions par jour. Le nombre d’enregistrements dans HotelReviews-Free.csv est inférieur à 20.

   - Dans **Ajouter des enrichissements**, nommez l’ensemble de compétences *hotel-reviews-ss*, choisissez le champ *reviews_text*, choisissez un niveau de granularité de *Pages (5 000 blocs de caractères)* , puis sélectionnez ces trois compétences cognitives : *Extraire les phrases clés*, *Détecter la langue*, *Détecter le sentiment*.

      ![Créer un ensemble de compétences](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Créer un ensemble de compétences")

   - Dans **Enregistrer les enrichissements dans une banque de connaissances**, fournissez la chaîne de connexion à votre compte de stockage Azure à usage général. Une base de connaissances est créée dans le stockage Table Azure lorsque vous sélectionnez les options de *projets de table Azure* dans cette section.

      ![Configurer la base de connaissances](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Configurer la base de connaissances")
   
   Cliquez sur **Suivant : Personnaliser l’index cible** pour passer à l’étape suivante.

1. Configurez un index pour les requêtes de recherche en texte intégral facultatives dans Recherche Azure. Bien que l’objectif de cette procédure pas à pas soit Power BI connecté au Stockage Table Azure , l’Assistant **Importer des données** peut également créer un index utilisé pour la recherche en texte intégral dans Recherche Azure. 

   L’Assistant échantillonne votre source de données pour déduire les champs et les types de données. Il vous suffit de sélectionner les attributs nécessaires pour atteindre les comportements souhaités. Par exemple, *Récupérable* signifie que le contenu du champ peut être récupéré à partir du service, tandis que *Possibilité de recherche* permet d’effectuer une recherche en texte intégral sur les champs sélectionnés.

   - Attribuez un nom à l’index, par exemple *hotel-reviews-idx*.
   - Définissez tous les champs sur **Récupérables**.
   - Définissez *city*, *name*, *reviews_text*, *language*, *keyphrases* sur **Possibilité de recherche**.
   - Définissez *sentiment*, *language*, *keyphrases* sur **Filtrable** et **À choix multiples**. 
   
    Votre index doit ressembler à l’image suivante.

     ![Configurer un index](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Configurer un index")

   Cliquez sur **Suivant : Créer un indexeur** pour passer à l’étape suivante.

1. Configurez un indexeur en lui attribuant un nom et une cadence d’exécution. Pour cette procédure pas à pas, utilisez *Hotel-Reviews-idxr* comme nom d’indexeur et utilisez la planification par défaut **Une fois** pour exécuter l’indexeur immédiatement.

   L’exécution de l’indexeur place toutes les configurations précédentes en mouvement. L’extraction, le traitement et la réception se produisent ici à cette étape.

1. Surveillez l’indexation dans la file d’attente de notifications dans le portail. L’exécution prend plusieurs minutes.

## <a name="connect-with-power-bi"></a>Se connecter avec Power BI

1. Lancez Power BI Desktop et sélectionnez **Obtenir les données**.

1. Dans Obtenir les données, sélectionnez **Azure**, puis **Stockage Table Azure**. Cliquez sur **Connecter**.

1. Dans Nom du compte ou URL, collez le nom de votre compte de stockage Azure (l’URL complète est résolue pour vous).

1. Entrez la clé du compte.

1. Sélectionnez Document, Phrases clés et Pages. Il s’agit des tables créées par l’Assistant Importation de données lorsque vous sélectionnez les mêmes éléments portant le même nom dans la configuration de la base de connaissances. Cliquez sur **Charger**.

1. Ouvrez Power Query en cliquant sur la commande **Modifier les requêtes**.

   ![Ouvrir Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Ouvrir Power Query")

1. Pour les documents :

   - Supprimez les colonnes PartitionKey, RowKey et Timestamp créées par le Stockage Table Azure. La base de connaissances fournit des relations utilisées dans cette analyse.

   - Développez la colonne de contenu.

     ![Modifier les tables](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Modifier les tables")

1. Sélectionnez tous les champs, puis désélectionnez ceux qui commencent par « metadata ».

1. Corrigez le type de données pour les colonnes suivantes à l’aide de l’icône ABC-123 sur chaque colonne :

   - Les colonnes de date doivent être définies sur **DateTime**
   - La *latitude* doit être définie sur un **Nombre décimal**
   - La *longitude* doit être définie sur un **Nombre décimal**

1. Répétez les étapes précédentes pour les phrases clés, en supprimant PartitionKey et les autres colonnes, en développant les colonnes de contenu, en affectant à *SentimentScore* la valeur **Nombre décimal**.

1. Répétez la procédure pour les pages, en supprimant PartitionKey et les autres colonnes, en développant les colonnes de contenu. Il n’y a aucune modification de type de données pour cette table.

1. Cliquez sur **Fermer et appliquer** à l’extrême gauche de la barre de commandes Power Query.

1. Vérifiez que Power BI reconnaît les relations créées par la base de connaissances au sein de vos données. Cliquez sur la vignette Relations dans le volet de navigation gauche. Les trois tables doivent être liées. Modifiez les relations et assurez-vous que la « direction du filtrage croisé » est définie sur les deux ; tous les visuels sont ainsi actualisés lorsqu’un filtre est appliqué.

   ![Valider les relations](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Valider les relations")

## <a name="try-with-larger-data-sets"></a>Essayer des jeux de données plus grands

Nous avons volontairement gardé le jeu de données petit pour éviter les frais liés à une procédure de démonstration. Pour une expérience plus réaliste, vous pouvez créer et attacher une ressource Cognitive Services facturable pour activer un plus grand nombre de transactions par rapport aux compétences d’analyseur de sentiment, d’extraction d’expressions clés et de détecteur de langage.

Créez des conteneurs dans le Stockage Blob Azure et chargez chaque fichier CSV dans son propre conteneur. Spécifiez l’un de ces conteneurs à l’étape de création de la source de données dans l’Assistant Importation de données.

| Description | Lien |
|-------------|------|
| Niveau Gratuit   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Petit (500 enregistrements) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Moyen (6 000 enregistrements)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Grand (jeu de données complet de 35 000 enregistrements) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Sachez que le traitement des jeux de données très volumineux est onéreux. Celui-ci coûte environ 1 000 dollars USD.|

Dans l’étape d’enrichissement de l’Assistant, attachez une ressource [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) facturable, créée au niveau *S0*, dans la même région que Recherche Azure pour utiliser des jeux de données plus grands. 

  ![Créer une ressource Cognitive Services](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Créer une ressource Cognitive Services")

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez répéter cet exercice ou effectuer une autre procédure pas à pas d’enrichissement via l’intelligence artificielle, supprimez l’indexeur *hotel-reviews-idx* que vous venez de créer. La suppression de l’indexeur réinitialise le compteur de transactions quotidiennes gratuites à zéro. 

Pour plus d’informations sur les procédures pas à pas illustrant la base de connaissances, passez à l’article suivant qui explique comment créer une base de connaissances à l’aide des API REST et de Postman.

> [!div class="nextstepaction"]
> [Prise en main de la base de connaissances](knowledge-store-howto.md)
