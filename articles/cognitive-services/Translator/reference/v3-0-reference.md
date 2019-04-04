---
title: Référence de l’API de traduction de texte Translator Text V3.0
titlesuffix: Azure Cognitive Services
description: Documentation de référence pour l’API de traduction de texte Translator Text V3.0.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 4a5bed67252c3b87233c8d2e677e3c620adb8a17
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918805"
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

Microsoft Translator est desservi par des centres de données situés dans plusieurs emplacements. Ils sont actuellement présents dans 6 [régions Azure](https://azure.microsoft.com/global-infrastructure/regions) :

* **Amérique :** USA Ouest 2 et USA Centre-Ouest 
* **Asie-Pacifique :** Asie Sud-Est et Corée Sud
* **Europe :** Europe Nord et Europe Ouest

Les requêtes adressées à l’API de traduction de texte Translator Text de Microsoft sont dans la plupart des cas gérées par le centre de données le plus proche de l’emplacement d’origine de la requête. En cas de défaillance d’un centre de données, la requête peut être routée à l’extérieur de la région.

Pour forcer la gestion de la requête par un centre de données spécifique, remplacez le point de terminaison Global dans la requête d’API par le point de terminaison régional souhaité :

|Description|Région|URL de base|
|:--|:--|:--|
|Azure|Globale|  api.cognitive.microsofttranslator.com|
|Azure|Amérique du Nord|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europe|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asie-Pacifique|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

S’abonner à l’API Translator Text ou [multi-service Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) dans Microsoft Cognitive Services et utiliser votre abonnement de clé (disponible dans le portail Azure) pour s’authentifier. 

Trois en-têtes sont à votre disposition pour authentifier votre abonnement. Ce tableau décrit la façon dont chaque en-tête est utilisé :

|headers|Description|
|:----|:----|
|Ocp-Apim-Subscription-Key|*À utiliser avec un abonnement à Cognitive Services si vous transmettez votre clé secrète*.<br/>La valeur est la clé secrète Azure pour votre abonnement à l’API de traduction de texte Translator Text.|
|Authorization|*Utiliser avec abonnement Cognitive Services si vous passez un jeton d’authentification.*<br/>La valeur est le jeton du porteur : `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Si vous transmettez une clé secrète multiservice, utiliser avec abonnement multiservice Cognitive Services.*<br/>La valeur est la région de l’abonnement multiservice. Cette valeur est facultative lorsque vous N'utilisez pas un abonnement multiservice.|

###  <a name="secret-key"></a>Clé secrète
La première option consiste à procéder à l’authentification à l’aide de l’en-tête `Ocp-Apim-Subscription-Key`. Ajoutez simplement l’en-tête `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` à votre requête.

### <a name="authorization-token"></a>Jeton d’autorisation
Vous pouvez également échanger votre clé secrète contre un jeton d’accès. Ce jeton est inclus avec chaque requête sous la forme de l’en-tête `Authorization`. Pour obtenir un jeton d’autorisation, exécutez une demande `POST` pour l’URL suivante :

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

### <a name="multi-service-subscription"></a>Abonnement multiservice

La dernière option d’authentification consiste à utiliser un abonnement d’un Service COGNITIF multiservice. Vous pouvez ainsi utiliser une clé secrète unique pour authentifier les requêtes de plusieurs services. 

Lorsque vous utilisez une clé secrète multiservice, vous devez inclure deux en-têtes d’authentification avec votre demande. Le premier passe la clé secrète, et le second spécifie la région associée à votre abonnement. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Région est obligatoire pour l’abonnement de texte API multiservice. La région que vous sélectionnez est la seule région que vous pouvez utiliser pour la traduction de texte lors de l’utilisation de la clé d’abonnement multiservice, et doit être la même région que vous avez sélectionné lorsque vous avez souscrit votre abonnement multiservice via le portail Azure.

Les régions disponibles sont les suivantes : `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` et `westus2`.

Si vous passez la clé secrète dans la chaîne de requête avec le paramètre `Subscription-Key`, vous devez spécifier la région avec le paramètre de requête `Subscription-Region`.

Si vous utilisez un jeton du porteur, vous devez obtenir le jeton du point de terminaison de la région : `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Errors

Une réponse d’erreur standard est un objet JSON avec une paire nom/valeur nommée `error`. La valeur est également un objet JSON avec les propriétés :

  * `code`: code d’erreur défini par le serveur.

  * `message`: chaîne fournissant une représentation lisible de l’erreur.

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

