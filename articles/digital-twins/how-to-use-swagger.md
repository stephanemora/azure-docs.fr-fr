---
title: Comprendre comment utiliser les références relatives à Swagger sur Azure Digital Twins | Microsoft Docs
description: Comprendre comment utiliser la documentation de référence relative à Swagger sur Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 842c35a1575c7bcf6f547fb04d5680178b3bee78
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730395"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentation de référence relative à Swagger sur Azure Digital Twins

Chaque instance Azure Digital Twins provisionnée inclut sa propre documentation de référence sur Swagger générée automatiquement.

[Swagger](https://swagger.io/), ou [OpenAPI](https://www.openapis.org/), regroupe des informations complexes sur les API au sein d’une ressource de référence interactive et indépendante du langage. Swagger fournit des documents de référence essentiels sur les charges utiles JSON, les méthodes HTTP et les points de terminaison à utiliser pour effectuer des opérations sur une API.

## <a name="swagger-summary"></a>Résumé de Swagger

Swagger propose un résumé interactif de votre API, qui comporte les éléments suivants :

* Informations sur l’API et le modèle objet.
* Points de terminaison d’API REST spécifiant les méthodes HTTP, les chemins contextuels, les paramètres, les en-têtes et les charges utiles de demande nécessaires.
* Test des fonctionnalités de l’API.
* Exemple d’informations de réponse utilisées pour valider et confirmer les réponses HTTP.
* Informations sur les codes d’erreur.

Swagger est un outil très pratique qui facilite le développement et le test des appels passés aux API de gestion Azure Digital Twins.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Documents de référence

La documentation de référence sur Swagger, générée automatiquement, fournit un rapide aperçu des concepts importants, des points de terminaison des API de gestion disponibles, ainsi qu'une description de chaque modèle objet pour faciliter le développement et les tests.

Un bref résumé décrit l’API.

[![Haut de swagger](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Les modèles objet des API de gestion sont également répertoriés.

[![Modèles de swagger](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

Vous pouvez sélectionner chacun des modèles objet listés pour obtenir un résumé plus détaillé des principaux attributs.

[![Modèle de swagger](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

Les modèles objet Swagger générés sont pratiques pour voir la totalité des [objets et API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins disponibles. Les développeurs peuvent utiliser cette ressource quand ils créent des solutions sur Azure Digital Twins.

## <a name="endpoint-summary"></a>Résumé des points de terminaison

Swagger fournit également une vue d'ensemble détaillée de tous les points de terminaison qui composent les API de gestion.

Chaque point de terminaison listé inclut également les informations obligatoires de la demande, et notamment les suivantes :

* Paramètres obligatoires.
* Types de données de paramètre obligatoires.
* Méthode HTTP permettant d’accéder à la ressource.

[![Les points de terminaison swagger](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

Sélectionnez une ressource pour en voir une présentation plus détaillée.

## <a name="use-swagger-to-test-endpoints"></a>Utiliser Swagger pour tester des points de terminaison

L’une des fonctionnalités les plus puissantes de Swagger est la possibilité de tester directement un point de terminaison d’API sur l’interface utilisateur de la documentation.

Lorsqu’un point de terminaison spécifique est sélectionné, le bouton **Essayer** apparaît.

[![Essayez de swagger](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

Développez cette section pour faire apparaître les champs d’entrée de chaque paramètre obligatoire et de chaque paramètre facultatif. Entrez les valeurs correctes, puis sélectionnez **Exécuter**.

[![A tenté de swagger](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

Après avoir exécuté le test, vous pouvez valider les données de réponse.

## <a name="swagger-response-data"></a>Données de la réponse de Swagger

Chaque point de terminaison listé inclut également des données de corps de réponse pour la validation de votre développement et de vos tests. Ces exemples comportent les codes d’état et le code JSON correspondant à des requêtes HTTP réussies.

[![Réponse de swagger](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

Les exemples incluent également des codes d’erreur pour faciliter le débogage ou l’amélioration des tests ayant échoué.

## <a name="swagger-oauth-20-authorization"></a>Autorisation OAuth 2.0 avec Swagger

Pour en savoir plus sur les demandes de test protégées par OAuth 2.0, consultez la [documentation officielle](https://swagger.io/docs/specification/authentication/oauth2/).

> [!NOTE]
> L’utilisateur principal qui a créé la ressource de représentations numériques Azure aura une attribution de rôle d’administrateur de l’espace et sera en mesure de créer des attributions de rôles supplémentaires pour d’autres utilisateurs.

1. Suivez les étapes de [ce démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) pour créer une application Azure AD de type ***application Web / API***. Ou vous pouvez réutiliser une inscription d’application existant.

2. Ajoutez l’url de réponse suivants à l’inscription d’application :

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nom  | Remplacer par | Exemples |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Votre URL de documentation API REST de gestion disponibles dans le portail  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Accorder des autorisations pour votre application accéder aux représentations numériques d’Azure. Sous **Autorisations requises**, entrez `Azure Digital Twins` et sélectionnez **Autorisations déléguées**. Sélectionnez ensuite **Accorder des autorisations**.

    ![Inscriptions d’applications Azure AD – Ajout d’API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Configurer le manifeste d’application pour autoriser un flux implicite OAuth 2.0. Cliquez sur **Manifeste** pour ouvrir le manifeste d’application pour votre application. Définissez *oauth2AllowImplicitFlow* sur `true`.

    ![Flux implicite Azure AD](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Copiez l’ID de votre application Azure AD.

À l’issue de l’inscription Azure Active Directory :

6. Cliquez sur le **Authorize** bouton sur votre page de swagger.

    [![Cliquez sur le fichier Swagger bouton Autoriser](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

7. Collez l’ID d’application dans le **client_id** champ.

    [![Champ client_id de swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

8. Vous serez ensuite redirigé vers la réussite suivante modale.

    [![Modal de redirection de swagger](media/how-to-use-swagger/swagger_auth_redirect.PNG)](media/how-to-use-swagger/swagger_auth_redirect.PNG#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles objet Azure Digital Twins et le graphe d’intelligence spatiale, voir [Comprendre les modèles objet Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

- Pour savoir comment s’authentifier auprès d’une API Gestion, voir [S’authentifier auprès des API](./security-authenticating-apis.md).