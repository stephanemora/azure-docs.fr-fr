---
title: Guide pratique pour utiliser les clés de création et de runtime avec LUIS
titleSuffix: Azure Cognitive Services
description: "LUIS utilise deux clés : la clé de création pour créer votre modèle et la clé d'exécution pour interroger le point de terminaison de prédiction avec des énoncés utilisateur."
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 954e7a22ae6b242c6221119c688259e4ce629a2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82101057"
---
# <a name="authoring-and-runtime-keys"></a>Clés de création et d'exécution

Language Understanding (LUIS) possède deux services et ensembles d’API :

* Création (anciennement _programmatique_)
* Runtime de prédiction

Il existe plusieurs types de clés, selon le service que vous souhaitez utiliser et la façon dont vous souhaitez utiliser ce service.

## <a name="non-azure-resources-for-luis"></a>Ressources non Azure pour LUIS

### <a name="starter-key"></a>Clé de démarrage

Lorsque vous commencez à utiliser LUIS, une **clé de démarrage** est créée pour vous. Cette ressource fournit les éléments suivants :

* Demandes de service de création gratuites via le portail LUIS ou les API (y compris les kits de développement logiciel)
* 1 000 requêtes gratuites de points de terminaison de prédiction par mois via un navigateur, une API ou des SDK

## <a name="azure-resources-for-luis"></a>Ressources Azure pour LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS autorise trois types de ressources Azure :

|Clé|Objectif|Service cognitif `kind`|Service cognitif `type`|
|--|--|--|--|
|[Clé de création](#programmatic-key)|Accédez aux données d’application et gérez-les avec des créations, des formations, des publications et des tests. Créez une clé de création LUIS si vous envisagez de créer des applications LUIS par programmation.<br><br>L’objectif de la clé de `LUIS.Authoring` est de vous permettre d’effectuer les opérations suivantes :<br>* gérer par programme les applications et les modèles Language Understanding, y compris la formation et la publication ;<br> * contrôler les autorisations d'accès à la ressource de création en attribuant le [rôle de contributeur](#contributions-from-other-authors) à des utilisateurs.|`LUIS.Authoring`|`Cognitive Services`|
|[Clé de prédiction](#prediction-endpoint-runtime-key)| Interrogez les requêtes de point de terminaison de prédiction. Créez une clé de prédiction LUIS avant que votre application cliente demande des prédictions au-delà des 1 000 requêtes fournies par la ressource Starter. |`LUIS`|`Cognitive Services`|
|[Clé de ressource multi-service Cognitive Services](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Requêtes de point de terminaison de prédiction de requête partagées avec LUIS et d’autres services Cognitive Services pris en charge.|`CognitiveServices`|`Cognitive Services`|

À la fin du processus de création de ressource, [attribuez la clé](luis-how-to-azure-subscription.md) à l'application dans le portail LUIS.

Il est important de créer des applications LUIS dans les [régions](luis-reference-regions.md#publishing-regions) où vous souhaitez publier et interroger.

> [!CAUTION]
> Pour des raisons pratiques, de nombreux exemples utilisent la [clé de démarrage](#starter-key) car son [quota](luis-limits.md#key-limits) inclut quelques appels gratuits au point de terminaison de prédiction.


### <a name="query-prediction-resources"></a>Interroger les ressources de prédiction

* La clé d'exécution peut être utilisée pour toutes vos applications LUIS, ou pour des applications LUIS spécifiques.
* N'utilisez pas la clé d'exécution pour la création d'applications LUIS.

Le point de terminaison d'exécution LUIS accepte deux styles de requêtes, les deux utilisant la clé d'exécution de point de terminaison de prédiction, mais à des emplacements différents.

Le point de terminaison utilisé pour accéder à l'environnement d'exécution utilise un sous-domaine propre à la région de votre ressource, indiquée par le terme `{region}` dans le tableau suivant.

## <a name="assignment-of-the-key"></a>Attribution de la clé

Vous pouvez [attribuer](luis-how-to-azure-subscription.md) la clé d'exécution à partir du [portail LUIS](https://www.luis.ai) ou via les API correspondantes.

## <a name="key-limits"></a>Limites de clés

Vous pouvez générer jusqu'à 10 clés de création par région et par abonnement.

Consultez [Limites de clés](luis-limits.md#key-limits) et [Régions Azure](luis-reference-regions.md).

Les régions de publication sont différentes des régions de création. Veillez à créer une application dans la région de création qui correspond à la région de publication dans laquelle vous souhaitez que votre application cliente soit située.

## <a name="key-limit-errors"></a>Erreurs de limites de clés
Si vous dépassez votre quota de transactions par seconde (TPS), vous recevez une erreur HTTP 429. Si vous dépassez votre quota de transactions par mois (TPM), vous recevez une erreur HTTP 403.

## <a name="contributions-from-other-authors"></a>Contributions d'autres auteurs

**Pour la [création d'applications migrées](luis-migration-authoring.md) de ressource** : les _contributeurs_ sont gérés dans le Portail Azure pour la ressource de création, à l’aide de la page de **contrôle d’accès (IAM)** . Apprenez à [ajouter un utilisateur](luis-how-to-collaborate.md) à l'aide de l'adresse e-mail du collaborateur et du rôle de _contributeur_.

**Pour les applications qui n'ont pas encore été migrées** : tous les _collaborateurs_ sont gérés sur le portail Luis à partir de la page **Gérer -> Collaborateurs**.

## <a name="move-transfer-or-change-ownership"></a>Déplacer, transférer ou modifier la propriété

Une application est définie par ses ressources Azure, qui sont elles-mêmes déterminées par l'abonnement du propriétaire.

Vous pouvez déplacer votre application LUIS. Utilisez les ressources documentaires suivantes à partir du portail Azure ou de l'interface Azure CLI :

* [Déplacer l'application entre des ressources de création LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Déplacer des ressources au sein d'un même abonnement ou entre des abonnements différents](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Pour transférer la [propriété](../../cost-management-billing/manage/billing-subscription-transfer.md) de votre abonnement :

**Pour les utilisateurs ayant migré - [création d’applications de ressources migrées](luis-migration-authoring.md)**  : En tant que propriétaire de la ressource, vous pouvez ajouter un `contributor`.

**Pour les utilisateurs qui n’ont pas encore été migrés** : Exportez votre application sous forme de fichier JSON. Un autre utilisateur LUIS peut importer l'application, devenant ainsi le propriétaire de celle-ci. La nouvelle application aura un ID d'application différent.

## <a name="access-for-private-and-public-apps"></a>Accès pour les applications privées et publiques

Pour une application **privée**, l'environnement d'exécution est accessible aux propriétaires et aux contributeurs. Pour une application **publique**, l'environnement d'exécution est accessible à tous les utilisateurs qui disposent de leur propre ressource d'exécution Azure [Cognitive Service](../cognitive-services-apis-create-account.md) ou [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) et de l'ID de l'application publique.

Aucun catalogue d'applications publiques n'est actuellement disponible.

### <a name="authoring-access"></a>Accès création
L'accès à l'application à partir du portail [LUIS](luis-reference-regions.md#luis-website) ou des [API de création](https://go.microsoft.com/fwlink/?linkid=2092087) est contrôlé par la ressource de création Azure.

Le propriétaire et tous les contributeurs disposent de l'accès requis pour créer l'application.

|L’accès à la création inclut|Notes|
|--|--|
|Ajouter ou supprimer des clés de point de terminaison||
|Exporter la version||
|Exporter les journaux d’activité du point de terminaison||
|Importer la version||
|Rendre une application publique|Lorsqu’une application est publique, toute personne disposant d’une clé de création ou de point de terminaison peut interroger l’application.|
|Modifier le modèle|
|Publish|
|Passez en revue les énoncés de point de terminaison pour un [apprentissage actif](luis-how-to-review-endpoint-utterances.md)|
|Former|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Accès à l'environnement d'exécution du point de terminaison de prédiction

L'accès permettant d'interroger le point de terminaison de prédiction est contrôlé par un paramètre de la page **Informations sur l'application** dans la section **Gérer**.

|[Point de terminaison privé](#runtime-security-for-private-apps)|[Point de terminaison public](#runtime-security-for-public-apps)|
|:--|:--|
|Accessible au propriétaire et aux contributeurs|Accessible au propriétaire, aux contributeurs et à toute personne qui connaît l'ID de l'application|

Vous pouvez contrôler qui voit votre clé d'exécution LUIS en l'appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Une fois l'API côté serveur appelée et authentifiée, et l'autorisation vérifiée, passez l'appel à LUIS. Bien que cette stratégie n'empêche pas les attaques de l'intercepteur, elle masque votre clé et l'URL du point de terminaison à vos utilisateurs, vous permet d'effectuer un suivi de l'accès et vous permet d'ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Sécurité d'exécution pour les applications privées

L'environnement d'exécution d'une application privée est uniquement disponible pour :

|Clé et utilisateur|Explication|
|--|--|
|Clé de création du propriétaire| Jusqu’à 1 000 accès au point de terminaison|
|Clés de création collaborateur/contributeur| Jusqu’à 1 000 accès au point de terminaison|
|N'importe quelle clé attribuée à LUIS par un auteur ou un collaborateur/contributeur|Dépend du niveau d’utilisation de la clé|

#### <a name="runtime-security-for-public-apps"></a>Sécurité d'exécution pour les applications publiques

Une fois qu’une application est configurée comme étant publique, _toute_ clé de création LUIS valide ou clé de point de terminaison LUIS peut interroger votre application, tant que la clé n’a pas épuisé le quota de point de terminaison.

Un utilisateur qui n'est pas propriétaire ou contributeur ne peut accéder à l'environnement d'exécution d'une application publique que s'il dispose de l'ID de l'application. LUIS ne dispose pas d’un _marché_ public, ni d’un autre moyen de rechercher une application publique.

Une application publique est publiée dans toutes les régions. Ainsi, un utilisateur ayant une clé de ressource LUIS basée sur une région peut accéder à l’application dans toute région associée à la clé de ressource.

## <a name="transfer-of-ownership"></a>Transfert de propriété

LUIS n’a pas le concept de transfert de la propriété d’une ressource.

## <a name="securing-the-endpoint"></a>Sécurisation du point de terminaison

Vous pouvez déterminer qui peut voir votre clé d'exécution de point de terminaison de prédiction LUIS en l'appelant dans un environnement serveur à serveur. Si vous utilisez LUIS à partir d’un bot, la connexion entre le bot et LUIS est déjà sécurisée. Si vous appelez le point de terminaison LUIS directement, vous devez créer une API côté serveur (comme une [fonction](https://azure.microsoft.com/services/functions/) Azure) avec un accès contrôlé (comme [AAD](https://azure.microsoft.com/services/active-directory/)). Lorsque l’API côté serveur est appelée et que l’authentification et l’autorisation sont vérifiées, passez l’appel à LUIS. Bien que cette stratégie n’empêche pas les attaques de type interception (man-in-the-middle), elle masque votre point de terminaison à vos utilisateurs, vous permet d’effectuer le suivi de l’accès et vous permet d’ajouter la journalisation de la réponse du point de terminaison (comme [Application Insights](https://azure.microsoft.com/services/application-insights/)).

## <a name="next-steps"></a>Étapes suivantes

* Comprendre les concepts du [contrôle de version](luis-concept-version.md).
* Apprenez à [créer des clés](luis-how-to-azure-subscription.md).
