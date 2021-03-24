---
title: Créer une application de démonstration dans le portail Azure
titleSuffix: Azure Cognitive Search
description: Exécutez l’Assistant Créer une application de démonstration (préversion) afin de générer des pages HTML et un script pour une application web opérationnelle. La page comprend une barre de recherche, une zone de résultats, une barre latérale et prend en charge la saisie semi-automatique.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745748"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Démarrage rapide : Créer une application de démonstration dans le portail (Recherche cognitive Azure)

Utilisez l’Assistant **Créer une application de démonstration**  du portail Azure pour générer une application web de type « localhost » téléchargeable qui s’exécute dans un navigateur. En fonction de sa configuration, l’application générée est opérationnelle dès la première utilisation, avec une connexion en lecture seule active à un index distant. Une application par défaut peut inclure une barre de recherche, une zone de résultats, des filtres dans une barre latérale et prendre en charge la saisie semi-automatique.

L’application de démonstration peut vous aider à visualiser la manière dont un index fonctionne dans une application cliente, mais elle n’est pas destinée aux scénarios de production. Les applications clientes doivent inclure la logique de sécurité, de gestion des erreurs et d’hébergement que la page HTML générée ne fournit pas. Quand vous êtes prêt à créer une application cliente, consultez [Créer votre première application de recherche à l’aide du SDK .NET](tutorial-csharp-create-first-app.md) pour connaître les étapes suivantes.

## <a name="prerequisites"></a>Prérequis

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

+ Service Recherche cognitive Azure. [Créez un service](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ [Microsoft Edge (dernière version)](https://www.microsoft.com/edge) ou Google Chrome.

+ Un [index de recherche](search-what-is-an-index.md) à utiliser comme base de votre application générée. 

  Ce guide de démarrage rapide utilise l’exemple prédéfini de données et d’index Real Estate, car il contient des images miniatures (l’Assistant prend en charge l’ajout d’images à la page de résultats). Pour créer l’index utilisé dans cet exercice, exécutez l’Assistant **Importer des données**, en choisissant la source de données *realestate-us-sample*.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="Page source de données de l’exemple de données" border="false":::

Quand l’index est prêt à être utilisé, passez à l’étape suivante.

## <a name="start-the-wizard"></a>Démarrer l’Assistant

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte Azure.

1. [Recherchez votre service de recherche](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Ensuite, dans la page Vue d’ensemble, à partir des liens situés au milieu de la page, sélectionnez **Index**. 

1. Choisissez *realestate-us-sample-index* dans la liste des index existants.

1. Dans la page de l’index, en haut, sélectionnez **Créer une application de démonstration (préversion)** pour démarrer l’Assistant.

1. Dans la première page de l’Assistant, sélectionnez **Activer le partage des ressources inter-origines (CORS)** pour ajouter la prise en charge du mécanisme CORS à votre définition d’index. Cette étape est facultative, mais votre application web locale ne se connecte pas à l’index distant sans elle.

## <a name="configure-search-results"></a>Configurer les résultats de la recherche

L’Assistant fournit une disposition de base pour l’affichage des résultats de la recherche qui inclut un espace pour une image miniature, un titre et une description. En complément de tous ces éléments se trouve un champ dans votre index qui fournit les données. 

1. Dans Miniature, choisissez le champ *miniature* dans l’index *realestate-us-sample*. Cet exemple inclut des miniatures d’image sous la forme d’images contenant des adresses URL stockées dans un champ appelé *miniature*. Si votre index n’a pas d’images, laissez ce champ vide.

1. Dans Titre, choisissez un champ qui reflète l’unicité de chaque document. Dans cet exemple, l’ID de liste est une sélection acceptable.

1. Dans Description, choisissez un champ qui fournit des détails pouvant faciliter la décision de cliquer ou non pour accéder à ce document particulier.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="configurer les résultats pour les exemples de données" border="false":::

## <a name="add-a-sidebar"></a>Ajouter une barre latérale

Le service de recherche prend en charge la navigation par facettes, souvent rendue sous forme de barre latérale. Les facettes reposent sur des champs filtrables et à choix multiples, comme exprimé dans le schéma d’index.

Dans Recherche cognitive Azure, la navigation par facettes est une expérience de filtrage cumulative. Au sein d’une catégorie, la sélection de plusieurs filtres développe les résultats (par exemple, en sélectionnant Seattle et Bellevue dans Ville). Parmi les catégories, la sélection de plusieurs filtres affine les résultats.

> [!TIP]
> Vous pouvez voir le schéma d’index complet dans le portail. Recherchez le lien **Définition d’index (JSON)** dans la page de vue d’ensemble de chaque index. Les champs éligibles à la navigation par facettes ont des attributs « filtrable : true » et « à choix multiples : true ».

1. Dans l’Assistant, sélectionnez l’onglet **Barre latérale** en haut de la page. Vous verrez une liste de tous les champs qui sont attribués comme pouvant être filtrés et à choix multiples dans l’index.

1. Acceptez la sélection actuelle des champs à choix multiples et passez à la page suivante.

## <a name="add-typeahead"></a>Ajouter la saisie semi-automatique

La fonctionnalité de saisie semi-automatique est disponible sous la forme de suggestions d’autocomplétion et de requête. L’Assistant prend en charge les suggestions de requête. En fonction des séquences de touches entrées par l’utilisateur, le service de recherche retourne la liste des chaînes de requête « complétées » pouvant être sélectionnées comme entrée.

Les suggestions sont activées sur des définitions de champs spécifiques. L’Assistant vous laisse la possibilité de configurer la quantité d’informations incluses dans une suggestion. 

La capture d’écran suivante montre les options de l’Assistant, en regard d’une page rendue dans l’application. Vous pouvez voir comment les sélections de champs sont utilisées et comment l’option « Afficher le nom du champ » est utilisée pour inclure ou exclure un étiquetage au sein de la suggestion.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Configuration des suggestions de requêtes":::

## <a name="add-suggestions"></a>Ajouter des suggestions

Les suggestions font référence aux invites de requête automatisées qui sont attachées à la zone de recherche. Recherche cognitive en prend deux en charge : la *saisie semi-automatique* d’un terme de recherche partiellement entré, et les *suggestions* pour une liste déroulante des documents potentiels correspondants.

L’Assistant prend en charge les suggestions, et les champs qui peuvent fournir des résultats suggérés sont dérivés d’une construction [`Suggesters`](index-add-suggesters.md) dans l’index :

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. Dans l’Assistant, sélectionnez l’onglet **Suggestions** en haut de la page. Vous verrez une liste de tous les champs qui sont désignés dans le schéma d’index en tant que fournisseurs de suggestions.

1. Acceptez la sélection actuelle et passez à la page suivante.

## <a name="create-download-and-execute"></a>Créer, télécharger et exécuter

1. Sélectionnez **Créer une application de démonstration** en bas de la page pour générer le fichier HTML.

1. À l’invite, sélectionnez **Télécharger votre application** pour télécharger le fichier.

1. Ouvrez le fichier et cliquez sur le bouton Rechercher. Cette action exécute une requête, qui peut être une requête vide (`*`) retournant un jeu de résultats arbitraire. La page doit ressembler à la capture d’écran suivante. Entrez un terme et utilisez des filtres pour affiner les résultats. 

L’index sous-jacent se compose de données générées et fictives qui ont été dupliquées dans tous les documents, et les descriptions ne correspondent parfois pas à l’image. Vous pouvez vous attendre à une expérience plus cohérente quand vous créez une application basée sur vos propres index.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="Exécuter l’application":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

L’application de démonstration est utile pour le prototypage, car vous pouvez simuler une expérience utilisateur final sans avoir à écrire de code JavaScript ou front-end. Pour plus d’informations sur les fonctionnalités front-end, commencez par la navigation par facettes :

> [!div class="nextstepaction"]
> [Guide pratique pour générer un filtre de facette](search-filters-facets.md)