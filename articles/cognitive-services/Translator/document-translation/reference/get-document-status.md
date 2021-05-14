---
title: Méthode get document status
titleSuffix: Azure Cognitive Services
description: La méthode get document status retourne l’état d’un document spécifique.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 4c6e82af46a012ad53dfa1cc1db1252ef2c0443e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864934"
---
# <a name="get-document-status"></a>Obtenir l’état d’un document

La méthode Get Document Status retourne l’état d’un document spécifique. La méthode retourne l’état de traduction d’un document spécifique en fonction de l’ID de la requête et de l’ID du document.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

Découvrez comment déterminer votre [nom de domaine personnalisé](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous ne pouvez pas utiliser le point de terminaison qui se trouve dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP au service Traduction de documentation.

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

|Paramètre de requête.|Obligatoire|Description|
|--- |--- |--- |
|documentId|Vrai|ID du document.|
|id|Vrai|L'ID de traitement.|
## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de requête sont les suivants :

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|En-tête de requête obligatoire|

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

|Code d’état|Description|
|--- |--- |
|200|OK. Demande réussie et acceptée par le service. Les détails de l’opération sont retournés. HeadersRetry-After: integerETag: string|
|401|Non autorisé. Vérifiez vos informations d’identification.|
|404|Introuvable. Ressource introuvable.|
|500|Erreur interne du serveur.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|

## <a name="get-document-status-response"></a>Réponse de get document status

### <a name="successful-get-document-status-response"></a>Réponse positive de get document status

|Nom|Type|Description|
|--- |--- |--- |
|path|string|Emplacement du document ou du dossier.|
|createdDateTimeUtc|string|Date et heure de création de l’opération.|
|lastActionDateTimeUtc|string|Date et heure de mise à jour de l’état de l’opération.|
|status|String|Liste des états possibles pour un travail ou un document. <ul><li>Opération annulée</li><li>Cancelling</li><li>Failed</li><li>NotStarted</li><li>En cours d’exécution</li><li>Succès</li><li>ValidationFailed</li></ul>|
|to|string|Code de langue à deux lettres de la langue cible. Voir la liste des langues.|
|progress|nombre|Progression de la traduction, si elle est disponible.|
|id|string|ID du document.|
|characterCharged|entier|Caractères facturés par l’API.|

### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|innerError|InnerErrorV2|Nouveau format d’erreur interne, conforme aux instructions de l’API Cognitive Services. Il contient les propriétés requises ErrorCode, message, et propriétés facultatives cibles, Details (paire clé-valeur), erreur interne (peut être imbriquée).|
|innerError.code|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive
L’objet JSON suivant est un exemple de réponse positive.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Exemple de réponse d’erreur

L’objet JSON suivant est un exemple de réponse d’erreur. Le schéma des autres codes d’erreur est le même.

Code d’état : 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Suivez notre guide de démarrage rapide pour en savoir plus sur l’utilisation du service Traduction de document et de la bibliothèque de client.

> [!div class="nextstepaction"]
> [Bien démarrer avec la traduction de documentation](../get-started-with-document-translation.md)
