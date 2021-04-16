---
title: Traduction de documentation - Méthode get document storage source
titleSuffix: Azure Cognitive Services
description: La méthode get document storage source retourne une liste des sources de stockage prises en charge.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: d6d8b1d08bbef1c37cbf0583022428037808580d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613765"
---
# <a name="document-translation-get-document-storage-source"></a>Traduction de documentation : get document storage source

La méthode Get Document Storage Source retourne une liste des options/sources de stockage prises en charge par le service Traduction de documentation.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Découvrez comment déterminer votre [nom de domaine personnalisé](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous ne pouvez pas utiliser le point de terminaison qui se trouve dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP au service Traduction de documentation.

## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de requête sont les suivants :

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|En-tête de requête obligatoire|

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

|Code d’état|Description|
|--- |--- |
|200|OK. Demande réussie, et retourne la liste des sources de stockage.|
|500|Erreur interne du serveur.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|

## <a name="get-document-storage-source-response"></a>Réponse de get document storage source

### <a name="successful-get-document-storage-source-response"></a>Réponse positive de get document storage source
Type de base pour le retour de liste dans l’API Get Document Storage Source.

|Nom|Type|Description|
|--- |--- |--- |
|value|string []|Liste d’objets.|


### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|innerError|InnerErrorV2|Nouveau format d’erreur interne, conforme aux instructions de l’API Cognitive Services. Il contient les propriétés obligatoires ErrorCode, message et les propriétés facultatives target, details(paire clé-valeur), et l’erreur interne (qui peut être imbriquée).|
|innerError.code|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive

Voici un exemple de réponse positive :

```JSON
{
  "value": [
    "AzureBlob"
  ]
}
```

### <a name="example-error-response"></a>Exemple de réponse d’erreur
Voici un exemple de réponse d’erreur : Le schéma des autres codes d’erreur est le même.

Code d’état : 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Suivez notre guide de démarrage rapide pour en savoir plus sur l’utilisation du service Traduction de documentation et de la bibliothèque de client.

> [!div class="nextstepaction"]
> [Bien démarrer avec la traduction de documents](../get-started-with-document-translation.md)