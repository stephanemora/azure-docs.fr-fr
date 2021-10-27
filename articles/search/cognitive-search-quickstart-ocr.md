---
title: 'Démarrage rapide : OCR et analyse d’image'
titleSuffix: Azure Cognitive Search
description: Utilisez l’Assistant Importation de données et les compétences cognitives de l’IA pour appliquer l’OCR et l’analyse d’image afin d’identifier et d’extraire du texte pouvant faire l’objet de recherches à partir de fichiers image.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/07/2021
ms.openlocfilehash: 68fc76ab80320a41cd9c9f0884dec8dc7771bccc
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065271"
---
# <a name="quickstart-apply-ocr-and-image-analysis-using-the-import-data-wizard"></a>Démarrage rapide : Appliquer l’OCR et l’analyse d’image à l’aide de l’Assistant Importation de données

Découvrez comment l’enrichissement par IA dans Recherche cognitive Azure permet d’ajouter l’OCR (reconnaissance optique de caractères) et l’analyse d’image pour créer du contenu pouvant être recherché à partir de fichiers image. 

Dans ce guide de démarrage rapide, vous allez exécuter l’Assistant **Importation de données** pour analyser du contenu visuel dans des fichiers JPG. Le contenu est constitué de photos de panneaux. La sortie est un index pouvant faire l’objet de recherches. Celui-ci contient des légendes, des étiquettes et du texte identifiés par l’OCR. Tous ces éléments peuvent être interrogés dans le portail à l’aide de l’[Explorateur de recherche](search-explorer.md). 

Pour commencer, vous allez créer quelques ressources et charger des exemples de fichiers avant d’exécuter l’Assistant.

Vous préférez vous lancer dans le code ? Essayez plutôt le [tutoriel .NET](cognitive-search-tutorial-blob-dotnet.md), le [tutoriel Python](cognitive-search-tutorial-blob-python.md) ou le [tutoriel REST](cognitive-search-tutorial-blob-dotnet.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez disposer des prérequis suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

+ Service Recherche cognitive Azure. [Créez un service](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ Compte Stockage Azure avec Stockage Blob. [Créez un compte de stockage](../storage/common/storage-account-create.md?tabs=azure-portal) ou [recherchez un compte existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

  + Choisissez le même abonnement si vous souhaitez que l’Assistant trouve votre compte de stockage et configure la connexion.
  + Choisissez la même région que celle de la Recherche cognitive Azure pour éviter des frais de bande passante.
  + Choisissez le StorageV2 (usage général V2).

> [!NOTE]
> Ce guide de démarrage rapide utilise également [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pour l’intelligence artificielle. Parce que la charge de travail est vraiment petite, Cognitive Services est utilisé en arrière-plan pour traiter gratuitement jusqu’à 20 transactions. Cela signifie que vous pouvez effectuer cet exercice sans avoir à créer une ressource Cognitive Services supplémentaire.

## <a name="set-up-your-data"></a>Configurer vos données

Dans les étapes suivantes, configurez un conteneur d’objets blob dans Stockage Azure pour stocker des fichiers de contenu hétérogènes.

1. [Téléchargez des exemples de données](https://github.com/Azure-Samples/azure-search-sample-data) à partir de GitHub. Il existe plusieurs jeux de données. Utilisez les fichiers du dossier **unsplash-images\jpg-signs** pour ce guide de démarrage rapide.

1. Chargez les exemples de données dans un conteneur d’objets blob.

   1. Connectez-vous au [portail Azure](https://portal.azure.com/) et recherchez votre compte de stockage.
   1. Dans le volet de navigation de gauche, sélectionnez **Conteneurs**.
   1. [Créez un conteneur](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) nommé « signs ». Utilisez le niveau d’accès public par défaut.
   1. Dans le conteneur « signs », sélectionnez **Charger** pour charger les fichiers à partir de votre dossier **unsplash-images\jpg-signs** local.

Vous devez avoir 10 fichiers contenant des photos de panneaux.

Il existe un deuxième sous-dossier qui comprend des bâtiments emblématiques. Si vous voulez [attacher une clé Cognitive Services](cognitive-search-attach-cognitive-services.md), vous pouvez inclure ces fichiers également pour voir comment l’analyse des images fonctionne sur des fichiers image qui n’incluent pas de texte incorporé. La clé est nécessaire pour les travaux qui dépassent l’allocation gratuite.

Vous êtes maintenant prêt à passer à l’Assistant Importation de données.

## <a name="run-the-import-data-wizard"></a>Exécuter l’Assistant Importation de données

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte Azure.

1. [Recherchez votre service de recherche](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Ensuite, dans la page Vue d’ensemble, cliquez sur **Importer des données** sur la barre de commandes pour configurer l’enrichissement cognitif en quatre étapes.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Capture d’écran de la commande Importer des données" border="true":::

### <a name="step-1---create-a-data-source"></a>Étape 1 : Créer une source de données

1. Dans **Connexion à vos données**, choisissez **Stockage Blob Azure**. Choisissez une connexion existante au compte de stockage et au conteneur que vous avez créés. Donnez un nom à la source de données et utilisez les valeurs par défaut pour le reste. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/connect-to-signs.png" alt-text="Configuration d’objets blob Azure" border="true":::

### <a name="step-2---add-cognitive-skills"></a>Étape 2 : Ajouter des compétences cognitives

Configurez ensuite l’enrichissement par IA pour appeler l’OCR et l’analyse d’image. 

1. Pour ce guide de démarrage rapide, nous utilisons la ressource Cognitive Services au niveau **Gratuit**. Les exemples de données se composent de 19 fichiers. Ainsi, l’allocation quotidienne de 20 transactions gratuites par indexeur sur Cognitive Services est suffisante pour ce guide de démarrage rapide. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/free-enrichments.png" alt-text="Attacher le traitement gratuit par Cognitive Services" border="true":::

1. Dans la même page, développez **Ajouter des enrichissements**, puis effectuez trois sélections :

   Activez l’OCR et fusionnez tout le texte dans le champ merged_content.

   Choisissez « Générer les balises des images » et « Générer les légendes des images ».

   :::image type="content" source="media/cognitive-search-quickstart-blob/select-ocr-image-enrichments.png" alt-text="Attacher Cognitive Services - sélectionner les services pour l’ensemble de compétences" border="true":::

   Pour l’analyse d’image, les images sont séparées du texte durant le craquage de document. Le champ « merged_content » réassocie le texte et les images dans le pipeline d’enrichissement par IA.

### <a name="step-3---configure-the-index"></a>Étape 3 : Configurer l’index

Un index contient le contenu pouvant faire l’objet de recherches. L’Assistant **Importation de données** peut généralement déduire le schéma à votre place en échantillonnant les données. Au cours de cette étape, examinez le schéma généré, puis modifiez éventuellement les paramètres. Vous trouverez ci-dessous le schéma par défaut créé pour le jeu de données de démonstration.

Pour ce guide de démarrage rapide, l’Assistant effectue un travail de qualité en termes de définition de valeurs par défaut raisonnables :  

+ Les champs par défaut sont basés sur les propriétés des objets blob existants ainsi que sur les nouveaux champs destinés à contenir une sortie d’enrichissement (par exemple, `text`, `layoutText`, `imageCaption`). Les types de données sont déduits à partir des métadonnées et des échantillonnages de données.

+ La clé de document par défaut est *metadata_storage_path* (ce champ est sélectionné, car il contient des valeurs uniques).

+ Les attributs par défaut sont **Récupérable** et **PossibilitéRecherche**. **PossibilitéRecherche** permet la recherche en texte intégral dans un champ. **Récupérable** signifie que les valeurs des champs peuvent être retournées dans les résultats. L’Assistant suppose que vous souhaitez ces champs récupérables et interrogeables, car vous les avez créés par l’intermédiaire de compétences.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields-ocr-images.png" alt-text="Champs d’index" border="true":::

Marquer un champ comme étant **Récupérable** ne signifie pas que le champ *doit* être présent dans les résultats de recherche. Vous pouvez contrôler avec précision la composition des résultats de recherche à l’aide du paramètre de requête **$select** pour spécifier les champs à inclure. Pour les champs comportant beaucoup de texte, tels que `content`, le paramètre **$select** constitue votre solution pour former pour les utilisateurs humains de votre application des résultats de recherche faciles à gérer, tout en garantissant au code client l’accès à toutes les informations dont il a besoin via l’attribut **Récupérable**.

### <a name="step-4---configure-the-indexer"></a>Étape 4 : Configurer l’indexeur

L’indexeur est une ressource de niveau supérieur qui gère le processus d’indexation. Il spécifie le nom de la source de données, un index cible et la fréquence d’exécution. L’Assistant **Importation de données** crée plusieurs objets, dont un indexeur qui est toujours présent et que vous pouvez exécuter à plusieurs reprises.

1. Dans la page **Indexeur**, vous pouvez accepter le nom par défaut et cliquer sur l’option de planification **Une fois** pour l’exécuter immédiatement. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-signs.png" alt-text="Définition de l’indexeur" border="true":::

1. Cliquez sur **Envoyer** pour créer et exécuter simultanément l’indexeur.

## <a name="monitor-status"></a>Superviser l’état

L’indexation des compétences cognitives prend plus de temps que l’indexation textuelle classique. Pour superviser la progression, accédez à la page Vue d’ensemble, puis sélectionnez l’onglet **Indexeurs** au milieu de la page.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-status-signs.png" alt-text="État de l’indexeur" border="true":::

Pour vérifier les détails de l’état d’exécution, sélectionnez un indexeur dans la liste.

## <a name="query-in-search-explorer"></a>Requête dans l’Explorateur de recherche

Après la création d’un index, vous pouvez exécuter des requêtes pour retourner des résultats. Dans le portail, utilisez l’**Explorateur de recherche** pour cette tâche. 

1. Sur la page de tableau de bord du service de recherche, cliquez sur **Explorateur de recherche** sur la barre de commandes.

1. Cliquez sur **Modifier l’index** en haut pour sélectionner l’index créé.

1. Dans Chaîne de requête, entrez une chaîne de recherche pour interroger l’index, par exemple `search=sign&searchFields=imageTags&$select=text,imageCaption,imageTags&$count=true`, puis sélectionnez **Rechercher**.

   :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-query-string-signs.png" alt-text="Chaîne de requête dans l’Explorateur de recherche" border="true":::

Les résultats sont retournés au format JSON, qui peut être long et difficile à lire, en particulier dans des documents volumineux provenant d’objets blob Azure. Voici quelques conseils pour effectuer des recherches dans cet outil :

+ Ajoutez `$select` pour spécifier les champs à inclure dans les résultats.
+ Ajoutez `searchField` pour délimiter l’étendue de la recherche en texte intégral à des champs spécifiques.
+ Utilisez CTRL-F pour rechercher des termes ou des propriétés spécifiques dans les résultats au format JSON.

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-results-signs.png" alt-text="Exemple de l’explorateur de recherche" border="true":::

Les chaînes de requête respectent la casse. Ainsi, si vous recevez un message « champ inconnu », consultez l’onglet **Champs** ou **Définition d’index (JSON)** pour vérifier le nom et la casse. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

Le service Recherche cognitive comporte d’autres compétences intégrées qui peuvent être utilisées dans l’Assistant Importation de données. Le prochain guide de démarrage rapide utilise la reconnaissance d’entités, la détection de langue et la traduction de texte. 

> [!div class="nextstepaction"]
> [Démarrage rapide : Traduire du texte et reconnaître des entités à l’aide de l’Assistant Importation de données](cognitive-search-quickstart-blob.md)