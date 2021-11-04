---
title: Importer une API GraphQL en utilisant le portail Azure | Microsoft Docs
titleSuffix: ''
description: Découvrez comment la Gestion des API prend en charge GraphQL, comment ajouter une API GraphQL et quelles sont les limitations de GraphQL.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1541e9de6d2f9346826e256fcb854b0ca7cdac3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096938"
---
# <a name="import-a-graphql-api-preview"></a>Importer une API GraphQL (préversion)

GraphQL est un langage de requête standard open source pour les API. Contrairement aux API basées sur un point de terminaison (ou de type REST) qui sont conçues pour des actions sur les ressources, les API GraphQL prennent en charge un ensemble plus large de cas d’utilisation et se concentrent sur les types de données, les schémas et les requêtes.

La Gestion des API s’attaque aux défis de sécurité, d’authentification et d’autorisation qui accompagnent la publication des API GraphQL. En utilisant la Gestion des API pour exposer vos API GraphQL, vous pouvez :
* Ajouter un service GraphQL sous forme d’API dans le portail Azure.  
* Sécuriser les API GraphQL en appliquant à la fois des stratégies de contrôle d’accès existantes et une [nouvelle stratégie](graphql-validation-policies.md) pour la sécurisation et la protection contre les attaques ciblées sur GraphQL. 
* Explorer le schéma et exécuter des requêtes de test sur les API GraphQL dans le portail Azure et les portails des développeurs. 

[!INCLUDE [preview-callout-graphql.md](./includes/preview/preview-callout-graphql.md)]

Dans cet article, vous allez :
> [!div class="checklist"]
> * Découvrir les avantages des API GraphQL.
> * Ajouter une API GraphQL dans votre instance Gestion des API.
> * Tester votre API GraphQL.
> * Découvrir les limitations de votre API GraphQL dans la Gestion des API.

## <a name="prerequisites"></a>Prérequis

- Disposer d’une instance d’API Management. Si vous ne l’avez pas déjà fait, [créez-en un](get-started-create-service-instance.md).
- Une API GraphQL. 

## <a name="add-a-graphql-api"></a>Ajouter une API GraphQL

1. Accédez à votre instance API Management.
1. Dans le menu de navigation latéral, sous la section **API**, sélectionnez **API**.
1. Sous **Définir une nouvelle API**, sélectionnez l’icône **GraphQL**.

    :::image type="content" source="media/graphql-api/import-graphql-api.png" alt-text="Sélection de l’icône GraphQL dans la liste des API":::

1. Dans la boîte de dialogue, sélectionnez **Complète** et renseignez les champs de formulaire requis.

    :::image type="content" source="media/graphql-api/create-from-graphql-schema.png" alt-text="Décrire les champs pour la création de GraphQL":::

    | Champ | Description |
    |----------------|-------|
    | Nom complet | Nom sous lequel votre API GraphQL est affichée. |
    | Nom | Nom brut de l’API GraphQL. Se remplit automatiquement à mesure que vous tapez le nom complet. |
    | Point de terminaison de l’API GraphQL | URL de base avec le nom de point de terminaison de l’API GraphQL. <br /> Par exemple : *https://example.com/your-GraphQL-name* . Vous pouvez également utiliser le [point de terminaison GraphQL « Star Wars »](https://swapi-graphql.netlify.app/.netlify/functions/index) courant en démo. |
    | Charger un fichier de schéma | Sélectionnez cette option pour rechercher et charger votre fichier de schéma. |
    | Description | Ajoutez une description de votre API. |
    | Modèle d’URL | Sélectionnez HTTP, HTTPS ou Les deux. Sélection par défaut : *Les deux*. |
    | Suffixe de l’URL de l’API| Ajoutez un suffixe d’URL pour identifier cette API spécifique dans cette instance de gestion des API. Il doit être unique dans cette instance Gestion des API. |
    | URL de base | Champ non modifiable affichant l’URL de base de votre API |
    | Étiquettes | Associez votre API GraphQL à des étiquettes nouvelles ou existantes. |
    | Produits | Associez votre API GraphQL à un produit pour la publier. |
    | Passerelles | Associez votre API GraphQL à des passerelles existantes. Sélection de passerelle par défaut : *Managée*. |
    | Créer une version pour cette API ? | Sélectionnez une version pour gérer les versions de votre API GraphQL. |
 
1. Cliquez sur **Créer**.

## <a name="test-your-graphql-api"></a>Tester votre API GraphQL

1. Accédez à votre instance API Management.
1. Dans le menu de navigation latéral, sous la section **API**, sélectionnez **API**.
1. Sous **Toutes les API**, sélectionnez votre API GraphQL.
1. Sélectionnez l'onglet **Test** pour accéder à la console de test. 
1. Sous **En-têtes** :
    1. Sélectionnez l’en-tête dans le menu déroulant **Nom**.
    1. Entrez la valeur dans le champ **Valeur**.
    1. Ajoutez d’autres en-têtes en sélectionnant **+ Ajouter un en-tête**.
    1. Supprimez les en-têtes à l’aide de l’**icône de corbeille**.
1. Si vous avez ajouté un produit à votre API GraphQL, appliquez l’étendue du produit sous **Appliquer l’étendue du produit**.
1. Sous **Éditeur de requête**, vous pouvez :
    1. Sélectionner au moins un champ ou sous-champ dans la liste du menu latéral. Les champs et les sous-champs que vous sélectionnez s’affichent dans l’éditeur de requête.
    1. Commencer à taper dans l’éditeur de requête pour composer une requête.
    
        :::image type="content" source="media/graphql-api/test-graphql-query.png" alt-text="Description de l’ajout de champs dans l’éditeur de requête":::

1. Sous **Variables de requête**, ajoutez des variables pour réutiliser la même requête ou mutation et passer des valeurs différentes.
1. Cliquez sur **Envoyer**.
1. Consultez la **Réponse**.

    :::image type="content" source="media/graphql-api/graphql-query-response.png" alt-text="Voir la réponse de la requête de test":::

1. Répétez les étapes précédentes pour tester différentes charges utiles.
1. Une fois le test effectué, quittez la console de test.

## <a name="limitations"></a>Limitations

* Seul le transfert GraphQL est pris en charge. 
* Chaque API GraphQL dans la Gestion des API correspond à un seul point de terminaison de back-end GraphQL.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
