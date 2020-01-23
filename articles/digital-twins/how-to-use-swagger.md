---
title: Guide pratique pour utiliser la documentation de référence sur Swagger - Azure Digital Twins | Microsoft Docs
description: Comprendre comment utiliser la documentation de référence relative à Swagger sur Azure Digital Twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: a14f3f6c17423dd9a86cbafe477fb8148932d43c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863527"
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

[![Résumé de Swagger et présentation des API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Les modèles objet des API de gestion sont également répertoriés.

[![Modèles Swagger répertoriés en bas de l’interface utilisateur Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Vous pouvez sélectionner chacun des modèles objet listés pour obtenir un résumé plus détaillé des principaux attributs.

[![Modèles Swagger développés pour lire le contenu des modèles](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Les modèles objet Swagger générés sont pratiques pour lire la totalité des [objets et API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins disponibles. Les développeurs peuvent utiliser cette ressource quand ils créent des solutions sur Azure Digital Twins.

## <a name="endpoint-summary"></a>Résumé des points de terminaison

Swagger fournit également une vue d'ensemble détaillée de tous les points de terminaison qui composent les API de gestion.

Chaque point de terminaison listé inclut également les informations obligatoires de la demande, et notamment les suivantes :

* Paramètres obligatoires.
* Types de données de paramètre obligatoires.
* Méthode HTTP permettant d’accéder à la ressource.

[![Points de terminaison Swagger affichés dans l’interface utilisateur Swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Sélectionnez chaque ressource pour afficher son contenu supplémentaire afin d’obtenir une vue d’ensemble plus détaillée.

## <a name="use-swagger-to-test-endpoints"></a>Utiliser Swagger pour tester des points de terminaison

L’une des fonctionnalités les plus puissantes de Swagger est la possibilité de tester directement un point de terminaison d’API sur l’interface utilisateur de la documentation.

Une fois que vous avez sélectionné un point de terminaison spécifique, le bouton **Essayer** s’affiche.

[![Bouton Essayer Swagger](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Développez cette section pour faire apparaître les champs d’entrée de chaque paramètre obligatoire et de chaque paramètre facultatif. Entrez les valeurs correctes, puis sélectionnez **Exécuter**.

[![Exemple de résultat Essayer Swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Après avoir exécuté le test, vous pouvez valider les données de réponse.

## <a name="swagger-response-data"></a>Données de la réponse de Swagger

Chaque point de terminaison listé inclut également des données de corps de réponse pour la validation de votre développement et de vos tests. Ces exemples incluent les codes d’état et JSON des requêtes HTTP réussies.

[![Exemple de réponse JSON Swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Les exemples incluent également des codes d’erreur pour faciliter le débogage ou l’amélioration des tests ayant échoué.

## <a name="swagger-oauth-20-authorization"></a>Autorisation OAuth 2.0 avec Swagger

> [!NOTE]
> * L’utilisateur principal qui a créé la ressource Azure Digital Twins se voit attribuer le rôle d’administrateur de l’espace, et est en mesure de créer des attributions de rôles supplémentaires pour d’autres utilisateurs. Ces utilisateurs et leurs rôles peuvent être autorisés à appeler les API.

1. Suivez les étapes de [ce démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) pour créer et configurer une application Azure AD. Vous pouvez aussi réutiliser une inscription d’application existante.

1. Ajoutez l’**URL de redirection** suivante à votre inscription d’application Azure AD :

    [![Inscrire l’URL de redirection Swagger dans AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | Remplacer par | Exemple |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Votre URL de documentation API REST de gestion disponible sur le portail  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Copiez l’**ID client** de votre application Azure AD.

À l’issue de l’inscription de l’application Azure Active Directory, procédez comme suit :

1. Cliquez sur le bouton **Autoriser** sur la page de votre instance Swagger.

    [![Cliquez sur le bouton Autoriser de Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Collez l’ID d’application dans le champ **client_id**.

    [![Champ client_id de Swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Vous serez ensuite redirigé vers le modal de réussite suivant.

    [![Modal de redirection de Swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Pour en savoir plus sur les demandes de test interactif protégées par OAuth 2.0, lisez la [documentation officielle](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles objet Azure Digital Twins et le graphe d’intelligence spatiale, voir [Comprendre les modèles objet Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

- Pour savoir comment s’authentifier auprès d’une API Gestion, voir [S’authentifier auprès des API](./security-authenticating-apis.md).