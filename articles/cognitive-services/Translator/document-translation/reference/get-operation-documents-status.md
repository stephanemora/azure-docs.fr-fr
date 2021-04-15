---
title: Traduction de documentation - get operation documents status
titleSuffix: Azure Cognitive Services
description: La méthode get operation documents status retourne l’état de tous les documents d’une demande de traduction de document par lot.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c6cf33f32abb473badbb11bf58bd52ffba53d75c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613759"
---
# <a name="document-translation-get-operation-documents-status"></a>Traduction de documentation : get operation documents status

La méthode get operation documents status retourne l’état de tous les documents d’une demande de traduction de document par lot.

Les documents inclus dans la réponse sont triés par ID de document et par ordre décroissant. Si le nombre de documents dans la réponse dépasse notre limite de pagination, la pagination côté serveur est utilisée. Les réponses paginées indiquent un résultat partiel et incluent un jeton de continuation dans la réponse. L’absence de jeton de continuation signifie qu’aucune page supplémentaire n’est disponible.

Les paramètres de requête $top et $skip peuvent être utilisés pour spécifier un nombre de résultats à retourner et un décalage pour la collection. Le serveur honore les valeurs spécifiées par le client. Toutefois, les clients doivent être prêts à gérer les réponses qui contiennent une taille de page différente ou un jeton de continuation.

Lorsque $top et $skip sont tous deux inclus, le serveur doit d’abord appliquer $skip, puis $top sur la collection.

> [!NOTE]
> Si le serveur ne peut pas honorer $top et/ou $skip, il doit retourner une erreur au client afin de l’en informer au lieu d’ignorer simplement les options de requête. Cela réduit le risque que le client émette des hypothèses quant aux données retournées.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|id|Vrai|ID de l’opération.|
|$skip|False|Ignorer les entrées $skip dans la collection. Lorsque $top et $skip sont tous deux fournis, $skip est appliqué en premier.|
|$top|False|Prendre les entrées $top dans la collection. Lorsque $top et $skip sont tous deux fournis, $skip est appliqué en premier.|

## <a name="request-headers"></a>En-têtes de requête

Les en-têtes de requête sont les suivants :

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|En-tête de requête obligatoire|

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

|Code d’état|Description|
|--- |--- |
|200|OK. Demande réussie, et retourne l’état des documents. HeadersRetry-After: integerETag: string|
|400|Demande non valide. Vérifiez les paramètres d’entrée.|
|401|Non autorisé. Vérifiez vos informations d’identification.|
|404|Ressource introuvable.|
|500|Erreur interne du serveur.|
|Autres codes d’état|<ul><li>Trop de demandes</li><li>Serveur temporairement indisponible</li></ul>|


## <a name="get-operation-documents-status-response"></a>Réponse de get operation documents status

### <a name="successful-get-operation-documents-status-response"></a>Réponse positive de get operation documents status

Les informations suivantes sont retournées dans une réponse positive.

|Nom|Type|Description|
|--- |--- |--- |
|@nextLink|string|URL de la page suivante. Null s’il n’y a plus de page disponible.|
|value|DocumentStatusDetail []|État détaillé des documents individuels listés ci-dessous.|
|value.path|string|Emplacement du document ou du dossier.|
|value.createdDateTimeUtc|string|Date et heure de création de l’opération.|
|value.lastActionDateTimeUt|string|Date et heure de mise à jour de l’état de l’opération.|
|value.status|status|Liste des états possibles pour un travail ou un document.<ul><li>Opération annulée</li><li>Cancelling</li><li>Failed</li><li>NotStarted</li><li>En cours d’exécution</li><li>Succès</li><li>ValidationFailed</li></ul>|
|value.to|string|Langue cible.|
|value.progress|string|Progression de la traduction, si elle est disponible.|
|value.id|string|Document ID.|
|value.characterCharged|entier|Caractères facturés par l’API.|

### <a name="error-response"></a>Réponse d’erreur

|Nom|Type|Description|
|--- |--- |--- |
|code|string|Enums contenant des codes d’erreur généraux. Valeurs possibles :<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Non autorisé</li></ul>|
|message|string|Obtient un message d’erreur général.|
|target|string|Obtient la source de l’erreur. Par exemple, il s’agirait de « documents » ou « document id » dans le cas d’un document non valide.|
|innerError|InnerErrorV2|Nouveau format d’erreur interne, conforme aux instructions de l’API Cognitive Services. Il contient les propriétés obligatoires ErrorCode, message et les propriétés facultatives target, details(paire clé-valeur), et l’erreur interne (qui peut être imbriquée).|
|innerError.code|string|Obtient la chaîne d’erreur de code.|
|innerError.message|string|Obtient un message d’erreur général.|

## <a name="examples"></a>Exemples

### <a name="example-successful-response"></a>Exemple de réponse positive

Voici un exemple de réponse positive :

```JSON
{
  "value": [
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
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
    "target": "Operation",
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
