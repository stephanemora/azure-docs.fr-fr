---
title: Comprendre comment utiliser Azure Digital Twins Swagger | Microsoft Docs
description: Guide pratique pour utiliser Azure Digital Twins Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: adgera
ms.openlocfilehash: dbadc90a206937d4c9f1d7b75a872d93b1a8a587
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323871"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Guide pratique pour utiliser Azure Digital Twins Swagger

Chaque instance Azure Digital Twins provisionnée inclut sa propre documentation de référence sur Swagger générée automatiquement.

[Swagger](https://swagger.io/) (ou [OpenAPI](https://www.openapis.org/)) regroupe des informations sur les API complexes dans une ressource de référence interactive et indépendante du langage. Plus précisément, Swagger fournit des documents de référence essentiels sur les charges utiles JSON, les méthodes HTTP et les points de terminaison spécifiques à utiliser pour effectuer des opérations sur une API.

## <a name="swagger-summary"></a>Résumé de Swagger

Swagger fournit un résumé interactif de votre API, notamment :

* Informations sur l’API et le modèle objet.
* Points de terminaison d’API REST spécifiant les méthodes HTTP, les en-têtes, les paramètres, les chemins du contexte et les charges utiles de demande nécessaires.
* Test des fonctionnalités de l’API.
* Exemple d’informations de réponse pour la validation et la confirmation des réponses HTTP.
* Informations sur les codes d’erreur.

Swagger est donc un outil pratique pour faciliter le développement et le test des appels passés à l’API de gestion.

> [!TIP]
> Pour référence, vous pouvez obtenir un premier aperçu de Swagger qui illustre le jeu de fonctionnalités de l’API.
> Pour ce faire, rendez-vous à l’adresse [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Vous pouvez accéder à votre propre documentation Swagger pour l’API de Gestion générée à l’adresse suivante :

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| `yourInstanceName` | Nom de votre instance Azure Digital Twins |
| `yourLocation` | Région de serveur dans laquelle votre instance est hébergée |

## <a name="reference-material"></a>Documents de référence

Les documents de référence générés automatiquement expliquent les principaux concepts et modèles objet.

Un bref résumé décrit l’API :

![Début de Swagger][1]

Les principaux modèles objet de l’API sont également listés :

![Modèles Swagger][2]

Vous pouvez cliquer sur chaque modèle objet listé pour obtenir un résumé plus détaillé des principaux attributs :

![Modèle Swagger][3]

Les modèles objet Swagger générés sont pratiques pour voir la totalité des [objets et API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins disponibles. Il s’agit d’une excellente ressource pour les développeurs souhaitant créer des solutions sur Azure Digital Twins.

## <a name="endpoint-summary"></a>Résumé des points de terminaison

Swagger fournit également une vue d’ensemble détaillée de tous les points de terminaison qui composent l’API.

Chaque point de terminaison listé inclut également les informations de demande nécessaires telles que les suivantes :

* Paramètres obligatoires.
* Types de données de paramètre obligatoires.
* Méthode HTTP pour accéder à la ressource.

![Points de terminaison Swagger][4]

Vous pouvez cliquer sur chaque ressource pour voir une présentation plus détaillée.

## <a name="using-swagger-to-test-endpoints"></a>Utilisation de Swagger pour tester les points de terminaison

Une des fonctionnalités puissantes que fournit Swagger est la possibilité d’**essayer** ou de tester un point de terminaison de l’API directement par le biais de l’interface utilisateur de la documentation.

Quand vous cliquez sur un point de terminaison spécifique, un bouton **Try it out** (Essayez-le) apparaît :

![Essayer Swagger][5]

Quand vous développez cette section, des champs d’entrée pour chaque paramètre requis et facultatif apparaissent. Entrez les valeurs appropriées et cliquez sur **Execute** :

![Swagger essayé][6]

Après avoir exécuté le test, vous pouvez valider les données de la réponse.

## <a name="swagger-response-data"></a>Données de la réponse de Swagger

Chaque point de terminaison listé inclut également des données de corps de réponse pour la validation de votre développement et de vos tests. Ces exemples incluent les codes d’état et JSON souhaités pour les requêtes HTTP réussies.

![Réponse de Swagger][7]

Les exemples incluent également des codes d’erreur pour faciliter le débogage ou l’amélioration des tests ayant échoué.

## <a name="swagger-oauth-20-authorization"></a>Autorisation OAuth 2.0 avec Swagger

Pour tester de façon interactive les demandes sur des ressources de l’API protégées par OAuth 2.0, consultez la [documentation officielle](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les modèles objet Azure Digital Twins et le graphe d’intelligence spatiale, consultez [cet article](./concepts-objectmodel-spatialgraph.md).

Pour savoir comment vous authentifier auprès de votre API de gestion, consultez [Authentification auprès des API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
