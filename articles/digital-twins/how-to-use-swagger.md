---
title: Comprendre comment utiliser Azure Digital Twins Swagger | Microsoft Docs
description: Utiliser Azure Digital Twins Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 69b8f64574427a6b3d3d2cf0312eac88d86e5907
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960194"
---
# <a name="use-azure-digital-twins-swagger"></a>Utiliser Azure Digital Twins Swagger

Chaque instance Azure Digital Twins provisionnée inclut sa propre documentation de référence sur Swagger générée automatiquement.

[Swagger](https://swagger.io/), ou [OpenAPI](https://www.openapis.org/), regroupe des informations complexes sur les API au sein d’une ressource de référence interactive et indépendante du langage. Swagger fournit des documents de référence essentiels sur les charges utiles JSON, les méthodes HTTP et les points de terminaison à utiliser pour effectuer des opérations sur une API.

## <a name="swagger-summary"></a>Résumé de Swagger

Swagger propose un résumé interactif de votre API, qui comporte les éléments suivants :

* Informations sur l’API et le modèle objet.
* Points de terminaison d’API REST spécifiant les méthodes HTTP, les chemins contextuels, les paramètres, les en-têtes et les charges utiles de demande nécessaires.
* Test des fonctionnalités de l’API.
* Exemple d’informations de réponse utilisées pour valider et confirmer les réponses HTTP.
* Informations sur les codes d’erreur.

Swagger est un outil pratique qui facilite le développement et le test des appels passés à l’API Gestion.

> [!TIP]
> Vous pouvez obtenir un premier aperçu de Swagger qui illustre le jeu de fonctionnalités de l’API.
> Pour ce faire, rendez-vous à l’adresse [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Vous pouvez accéder à votre propre documentation Swagger générée pour l’API Gestion à l’adresse suivante :

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nom de l’attribut personnalisé | Remplacer par |
| --- | --- |
| *yourInstanceName* | Nom de votre instance Azure Digital Twins |
| *yourLocation* | Région de serveur dans laquelle votre instance est hébergée |

## <a name="reference-material"></a>Documents de référence

Les documents de référence générés automatiquement expliquent les principaux concepts et modèles objet.

Un bref résumé décrit l’API.

![Début de Swagger][1]

Les principaux modèles objet de l’API sont également listés.

![Modèles Swagger][2]

Vous pouvez sélectionner chacun des modèles objet listés pour obtenir un résumé plus détaillé des principaux attributs.

![Modèle Swagger][3]

Les modèles objet Swagger générés sont pratiques pour voir la totalité des [objets et API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins disponibles. Les développeurs peuvent se servir de cette ressource lorsqu’ils créent des solutions sur Azure Digital Twins.

## <a name="endpoint-summary"></a>Résumé des points de terminaison

Swagger fournit également une vue d’ensemble détaillée de tous les points de terminaison qui composent l’API.

Chaque point de terminaison listé inclut également les informations obligatoires de la demande, et notamment les suivantes :

* Paramètres obligatoires.
* Types de données de paramètre obligatoires.
* Méthode HTTP permettant d’accéder à la ressource.

![Points de terminaison Swagger][4]

Sélectionnez une ressource pour en voir une présentation plus détaillée.

## <a name="use-swagger-to-test-endpoints"></a>Utiliser Swagger pour tester des points de terminaison

L’une des fonctionnalités les plus puissantes de Swagger est la possibilité de tester directement un point de terminaison d’API sur l’interface utilisateur de la documentation.

Lorsqu’un point de terminaison spécifique est sélectionné, le bouton **Essayer** apparaît.

![Essayer Swagger][5]

Développez cette section pour faire apparaître les champs d’entrée de chaque paramètre obligatoire et de chaque paramètre facultatif. Entrez les valeurs correspondantes, puis sélectionnez **Exécuter**.

![Swagger essayé][6]

Après avoir exécuté le test, vous pouvez valider les données de réponse.

## <a name="swagger-response-data"></a>Données de la réponse de Swagger

Chaque point de terminaison listé inclut également des données de corps de réponse pour la validation de votre développement et de vos tests. Ces exemples comportent les codes d’état et le code JSON correspondant à des requêtes HTTP réussies.

![Réponse de Swagger][7]

Les exemples incluent également des codes d’erreur pour faciliter le débogage ou l’amélioration des tests ayant échoué.

## <a name="swagger-oauth-20-authorization"></a>Autorisation OAuth 2.0 avec Swagger

Pour tester de façon interactive les demandes sur des ressources de l’API protégées par OAuth 2.0, consultez la [documentation officielle](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les modèles objet Azure Digital Twins et le graphe d’intelligence spatiale, voir [Comprendre les modèles objet Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

Pour savoir comment s’authentifier auprès d’une API Gestion, voir [S’authentifier auprès des API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
