---
title: Référence de l’API de traduction de texte Translator Text V3.0
titlesuffix: Azure Cognitive Services
description: Documentation de référence pour l’API de traduction de texte Translator Text V3.0.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 6f679536d69f700fd6678eb3bbbb869e42439cde
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853351"
---
# <a name="translator-text-api-v30"></a>API de traduction de texte Translator Text v3.0

## <a name="whats-new"></a>Nouveautés

La version 3 de l’API de traduction de texte Translator Text fournit une API web moderne basée sur JSON. Elle simplifie l’utilisation et améliorer les performances en regroupant les fonctionnalités existantes en moins d’opérations, et elle fournit de nouvelles fonctionnalités.

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
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Le code d’erreur est un nombre à 6 chiffres qui combine le code d’état HTTP à 3 chiffres et un nombre à 3 chiffres qui sert à catégoriser plus précisément l’erreur. Voici les codes d’erreur courants :

| Code | Description |
|:----|:-----|
| 400000| Une des requêtes d’entrées n’est pas valide.|
| 400001| Le paramètre « scope » n’est pas valide.|
| 400002| Le paramètre « category » n’est pas valide.|
| 400003| Un spécificateur de langage est manquant ou non valide.|
| 400004| Un spécificateur de script cible (« To script ») est manquant ou non valide.|
| 400005| Un texte d’entrée est manquant ou non valide.|
| 400006| La combinaison de langue et de script n’est pas valide.|
| 400018| Un spécificateur de script source (« From script ») est manquant ou non valide.|
| 400019| L’une des langues spécifiées n’est pas prise en charge.|
| 400020| L’un des éléments du tableau de texte d’entrée n’est pas valide.|
| 400021| Le paramètre de version d’API est manquant ou non valide.|
| 400023| L’une des paires de langues spécifiées n’est pas valide.|
| 400035| La langue source (champ « From ») n’est pas valide.|
| 400036| La langue cible (champ « To ») est manquante ou non valide.|
| 400042| L’une des options spécifiées (champ « Options ») n’est pas valide.|
| 400043| L’ID de trace client (champ de ClientTraceId ou en-tête de X-ClientTranceId) est manquant ou non valide.|
| 400050| Le texte d’entrée est trop long.|
| 400064| Le paramètre « translation » est manquant ou non valide.|
| 400070| Le nombre de scripts de cible (paramètre ToScript) ne correspond pas au nombre de langages cible (paramètre To).|
| 400071| La valeur n’est pas valide pour TextType.|
| 400072| Le tableau de texte d’entrée compte trop d’éléments.|
| 400073| Le paramètre de script n’est pas valide.|
| 400074| Le corps de la requête n’est pas un élément JSON valide.|
| 400075| La combinaison de paire de langue et de catégorie n’est pas valide.|
| 400077| La taille de requête maximale a été dépassée.|
| 400079| Le système personnalisé demandé pour la traduction entre le langage source et le langage cible n’existe pas.|
| 401000| La requête n’est pas autorisée car les informations d’identification sont manquantes ou non valides.|
| 401015| « Les informations d’identification fournies sont valables pour l’API Speech. Cette requête nécessite des informations d’identification pour l’API Texte. Veuillez utiliser un abonnement à l’API de traduction de texte Translator Text. »|
| 403000| L’opération n’est pas autorisée.|
| 403001| L’opération n’est pas autorisée, car l’abonnement a dépassé son quota gratuit.|
| 405000| La méthode de requête n’est pas prise en charge pour la ressource demandée.|
| 408001| Le système de traduction personnalisée demandé n’est pas encore disponible. Réessayez dans quelques minutes.|
| 415000| L’en-tête Content-Type est manquante ou invalide.|
| 429000, 429001, 429002| Le serveur a rejeté la requête, car le client envoie trop de requête. Réduisez la fréquence des requêtes pour pallier la limitation.|
| 500000| Une erreur inattendue s’est produite. Si l’erreur persiste, signalez-la en fournissant les informations suivantes : date et heure de l’erreur, identificateur de la demande dans l’en-tête de réponse X-RequestId, et identificateur du client dans l’en-tête de demande X-ClientTraceId.|
| 503000| Le service est temporairement indisponible. Veuillez réessayer. Si l’erreur persiste, signalez-la en fournissant les informations suivantes : date et heure de l’erreur, identificateur de la demande dans l’en-tête de réponse X-RequestId, et identificateur du client dans l’en-tête de demande X-ClientTraceId.|

