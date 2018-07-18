---
title: Documentation de référence pour l’API de traduction de texte Microsoft Translator Text v3.0 | Microsoft Docs
description: Référencez la documentation relative à l’API de traduction de texte Microsoft Translator Text v3.0.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368721"
---
#<a name="translator-text-api-v30"></a>API de traduction de texte Translator Text v3.0

## <a name="whats-new"></a>Nouveautés

La version 3 de l’API de traduction de texte Microsoft Translator Text fournit une API web moderne basée sur JSON. Elle simplifie l’utilisation et améliorer les performances en regroupant les fonctionnalités existantes en moins d’opérations, et elle fournit de nouvelles fonctionnalités.

 * Translittération pour convertir du texte dans une langue d’un script vers un autre script.
 * Traduction en plusieurs langues en une seule demande.
 * Détection de la langue, traduction et translittération en une seule demande.
 * Dictionnaire permettant de rechercher d’autres traductions d’un terme et de rechercher des traductions inverses et des exemples montrant les termes utilisés en contexte.
 * Résultats de détection de langue plus informatifs.

## <a name="base-urls"></a>URL de base

L’API Texte v3.0 est disponible dans le cloud suivant :

| Description | Région | URL de base                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globale | api.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentification

Abonnez-vous à l’API de traduction de texte Translator Text dans Microsoft Cognitive Services et utilisez votre clé d’abonnement (disponible sur le portail Azure) pour vous authentifier. 

La façon la plus simple consiste à transmettre votre clé secrète Azure au service de traduction en utilisant l’en-tête de demande `Ocp-Apim-Subscription-Key`.

Une alternative consiste à utiliser votre clé secrète pour obtenir un jeton d’autorisation auprès du service de jetons. Ensuite, vous transmettez ce jeton d’autorisation au service de traduction en utilisant l’en-tête de demande `Authorization`. Pour obtenir un jeton d’autorisation, exécutez une demande `POST` pour l’URL suivante :

| Environnement     | URL du service d’authentification                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Voici des exemples de demandes pour obtenir un jeton avec une clé secrète :

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Une demande réussie retourne le jeton d’accès codé sous forme de texte brut dans le corps de la réponse. Le jeton valide est transmis au service de traduction sous forme de jeton du porteur dans l’autorisation.

```
Authorization: Bearer <Base64-access_token>
```

Un jeton d’authentification est valide pour une durée de 10 minutes. Le jeton doit être réutilisé lorsque vous effectuez plusieurs appels aux API Translator. Toutefois, si votre programme effectue des demandes à l’API Translator sur une période prolongée, votre programme doit demander un nouveau jeton d’accès à intervalles réguliers (par exemple, toutes les 8 minutes).

Pour résumer, une demande client à l’API Translator inclut un en-tête d’autorisation extrait du tableau suivant :

<table width="100%">
  <th width="30%">headers</th>
  <th>Description</th>
  <tr>
    <td>Ocp-Apim-Subscription-Key</td>
    <td>*À utiliser avec un abonnement à Cognitive Services si vous transmettez votre clé secrète*.<br/>La valeur est la clé secrète Azure pour votre abonnement à l’API de traduction de texte Translator Text.</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>*À utiliser avec un abonnement à Cognitive Services si vous transmettez un jeton d'authentification.*<br/>La valeur est le jeton du porteur : `Bearer <token>`.</td>
  </tr>
</table> 

## <a name="errors"></a>Errors

Une réponse d’erreur standard est un objet JSON avec une paire nom/valeur nommée `error`. La valeur est également un objet JSON avec les propriétés :

  * `code` : Code d’erreur défini par le serveur.

  * `message` : Chaîne fournissant une représentation lisible de l’erreur.

Par exemple, un client avec un abonnement d’essai gratuit recevrait l’erreur suivante une fois le quota gratuit épuisé :

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
