---
title: Importer une application web Azure dans Gestion des API Azure | Microsoft Docs
description: Cet article explique comment utiliser Gestion des API Azure pour importer une API web hébergée dans Azure App Service.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/27/2021
ms.author: apimpm
ms.openlocfilehash: de51a32f737e71501723847ec473387ac419f8a6
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108230765"
---
# <a name="import-an-azure-web-app-as-an-api"></a>Importer une application web Azure en tant qu’API

Cet article explique comment importer une application web Azure dans Gestion des API Azure et tester l’API importée en utilisant le portail Azure.

> [!NOTE]
> Vous pouvez importer et gérer vos API à l’aide de l’extension Gestion des API pour Visual Studio Code. Suivez le [tutoriel sur l’extension Gestion des API](visual-studio-code-tutorial.md) pour effectuer l’installation et commencer.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une application web hébergée dans App Service
> * Tester l’API dans le portail Azure

## <a name="expose-web-app-with-api-management"></a>Exposer une application web avec Gestion des API

[Azure App Service](../app-service/overview.md) est un service HTTP pour l’hébergement d’applications web, d’API REST et de back-ends mobiles. Les développeurs d’API peuvent utiliser leurs piles technologiques et pipelines préférés pour développer des API et publier leurs back-ends d’API en tant qu’applications web dans un environnement sécurisé et évolutif. Ensuite, utilisez Gestion des API pour exposer les applications web, gérer et protéger les API tout au long de leur cycle de vie et les publier auprès des consommateurs.

Gestion des API est l’environnement recommandé pour exposer une API hébergée par une application web, pour plusieurs raisons :

* Découpler la gestion et la sécurisation du serveur frontal exposé aux consommateurs d’API à partir de la gestion et de la surveillance de l’application web principale
* Gérer les API web hébergées en tant qu’applications web dans le même environnement que vos autres API
* Appliquer des [stratégies](api-management-policies.md) pour modifier le comportement de l’API, comme la limitation du taux d’appels
* Diriger les consommateurs d’API vers le [portail des développeurs](api-management-howto-developer-portal.md) personnalisable de Gestion des API pour découvrir et apprendre à connaître vos API, demander l’accès et les essayer

Pour plus d’informations, consultez [En savoir plus sur Gestion des API](api-management-key-concepts.md).

## <a name="openapi-specification-versus-wildcard-operations"></a>Spécification OpenAPI et opérations génériques

Gestion des API prend en charge l’importation d’applications web hébergées dans App Service qui incluent une spécification OpenAPI (définition Swagger). Toutefois, une spécification OpenAPI n’est pas obligatoire.

* Si l’application web a une spécification OpenAPI configurée dans une définition d’API, Gestion des API crée des opérations d’API qui correspondent directement à la définition, y compris les chemins d’accès, les paramètres et les types de réponse requis. 

  Il est recommandé d’avoir une spécification OpenAPI, car l’API est importée dans Gestion des API avec une grande fidélité, ce qui vous donne la possibilité de valider, gérer, sécuriser et mettre à jour les configurations pour chaque opération séparément.

* Si aucune spécification OpenAPI n’est fournie, Gestion des API génère des [opérations génériques](add-api-manually.md#add-and-test-a-wildcard-operation) pour les verbes HTTP courants (GET, PUT, etc.). Ajoutez un chemin d’accès ou des paramètres requis à une opération générique pour transmettre une demande d’API à l’API back-end.

  Avec les opérations génériques, vous pouvez toujours tirer parti des mêmes fonctionnalités de Gestion des API, mais les opérations ne sont pas définies au même niveau de détail par défaut. Dans les deux cas, vous pouvez [modifier](edit-api.md) ou [ajouter](add-api-manually.md) des opérations à l’API importée.
 
### <a name="example"></a>Exemple
Votre application web back-end peut prendre en charge deux opérations GET : 
*  `https://myappservice.azurewebsites.net/customer/{id}`
*  `https://myappservice.azurewebsites.net/customers`

Vous importez l’application web dans votre service Gestion des API dans un chemin d’accès tel que `https://contosoapi.azureapi.net/store`. Le tableau suivant répertorie les opérations qui sont importées dans Gestion des API, avec ou sans spécification OpenAPI : 

| Type |Opérations importées  |Exemples de demandes |
|---------|---------|---------|
|Spécification OpenAPI    | `GET  /customer/{id}`<br/><br/> `GET  /customers`         |  `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`       |
|Caractère générique     | `GET  /*`         | `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`  |

L’opération générique autorise les mêmes requêtes au service back-end que les opérations dans la spécification OpenAPI. Toutefois, les opérations spécifiées par OpenAPI peuvent être gérées séparément dans Gestion des API. 

## <a name="prerequisites"></a>Prérequis

+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Assurez-vous que votre abonnement contient une instance App Service. Pour plus d’informations, consultez [Documentation d’App Service](../app-service/index.yml).

  Pour connaître les étapes permettant de créer un exemple d’API web et de le publier en tant qu’application web Azure, consultez :

    * [Tutoriel : Création d’une API web avec ASP.NET Core](/aspnet/core/tutorials/first-web-api)
    * [Publier une application ASP.NET Core sur Azure avec Visual Studio Code](/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importer et publier une API back-end

> [!TIP]
> Les étapes suivantes permettent de démarrer l’importation en utilisant Gestion des API Azure dans le portail Azure. Vous pouvez également vous connecter à Gestion des API directement depuis votre application web en sélectionnant **Gestion des API** dans le menu **API** de l’application.  

1. À partir du portail Azure, accédez à votre service Gestion des API, puis sélectionnez **API** dans le menu.
1. Sélectionnez **App Service** dans la liste.

    :::image type="content" source="media/import-app-service-as-api/app-service.png" alt-text="Créer à partir d’App Service":::
1. Sélectionnez **Parcourir** pour afficher la liste des instances App Service dans votre abonnement.
1. Sélectionnez un instance App Service. Si une définition OpenAPI est associée à l’application web sélectionnée, Gestion des API l’extrait et l’importe. 

    Si aucune définition OpenAPI n’est trouvée, Gestion des API expose l’API en générant des opérations génériques pour les verbes HTTP courants. 
1. Ajoutez un suffixe d’URL d’API. Le suffixe est un nom qui identifie cette API spécifique dans cette instance Gestion des API. Il doit être unique dans cette instance APIM.
1. Publiez l’API en l’associant à un produit. Dans ce cas, le produit « *Illimité* » est utilisé. Si vous souhaitez que l’API soit publiée et mise à la disposition des développeurs, ajoutez-la à un produit. Vous pouvez effectuer cette opération durant la création de l’API ou ultérieurement.

    > [!NOTE]
    > Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure de nombreuses API et les proposer aux développeurs dans le portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.
    >
    > Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :
    > * **Starter**
    > * **Illimité**   
1. Entrez d’autres paramètres d’API. Vous pouvez définir les valeurs lors de la création, ou les configurer ultérieurement en accédant à l’onglet **Paramètres**. Les paramètres sont expliqués dans le tutoriel [Importer et publier votre première API](import-and-publish.md#import-and-publish-a-backend-api).
1. Sélectionnez **Create** (Créer).
    :::image type="content" source="media/import-app-service-as-api/import-app-service.png" alt-text="Créer une API à partir d’App Service":::

## <a name="test-the-new-api-in-the-azure-portal"></a>Tester la nouvelle API dans le Portail Azure

Les opérations peuvent être directement appelées depuis le portail Azure, qui permet d’afficher et de tester les opérations d’une API. Vous pouvez également tester l’API dans le [portail des développeurs](api-management-howto-developer-portal.md) ou en utilisant vos propres outils clients REST.

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
1. Sélectionnez l’onglet **Test**.
1. Sélectionnez une opération.

    La page affiche des champs pour les paramètres de requête et des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance Gestion des API, la clé est renseignée automatiquement, car vous êtes déjà administrateur. 
1. Appuyez sur **Envoyer**.

    Lorsque le test est réussi, le back-end répond avec **200 OK** et des données.

### <a name="test-wildcard-operation-in-the-portal"></a>Tester une opération générique dans le portail

Lorsque des opérations génériques sont générées, elles peuvent ne pas correspondre directement à l’API back-end. Par exemple, une opération GET générique importée dans Gestion des API utilise le chemin d’accès `/` par défaut. Toutefois, votre API back-end peut prendre en charge une opération GET à l’emplacement suivant :

`/api/TodoItems`

Vous pouvez tester le chemin d’accès `/api/TodoItems` comme suit. 

1. Sélectionnez l’API que vous avez créée, puis sélectionnez l’opération.
1. Sélectionnez l’onglet **Test**.
1. Dans **Paramètres du modèle**, mettez à jour la valeur à côté du nom générique (*). Par exemple, entrez `api/TodoItems`. Cette valeur est ajoutée au chemin d’accès `/` pour l’opération générique.

    :::image type="content" source="media/import-app-service-as-api/test-wildcard-operation.png" alt-text="Tester une opération générique":::
1. Sélectionnez **Envoyer**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
