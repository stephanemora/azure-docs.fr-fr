---
title: 'Référence : erreurs de Form Recognizer (2021-09-30-préversion)'
titleSuffix: Azure Applied AI Services
description: Découvrez comment les erreurs sont représentées dans Form Recognizer et consultez la liste des erreurs possibles retournées par le service.
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/30/2021
ms.author: paulhsu
ms.custom: cog-serv-seo-aug-2020
keywords: traitement de documents
ms.openlocfilehash: a67c98189198cf8df0f8f7e6fa88c9a552812bfa
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730648"
---
# <a name="form-recognizer-error-guide-v30-preview"></a>Guide des erreurs de Form Recognizer v 3.0 (préversion)

Form Recognizer utilise une conception unifiée pour représenter toutes les erreurs rencontrées dans les API REST.  Chaque fois qu’une opération d’API renvoie un code d’état 4xx ou 5xx, des informations supplémentaires sur l’erreur sont retournées dans le corps JSON de la réponse comme suit :

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

Pour les opérations longues où plusieurs erreurs peuvent être rencontrées, le code d’erreur de niveau supérieur est défini sur l’erreur la plus grave, et les erreurs individuelles sont listées sous la propriété *error.details*.  Dans de tels scénarios, la propriété *target* de chaque erreur individuelle spécifie le déclencheur de l’erreur.

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

La propriété *error.code* de niveau supérieur peut être l’un des messages de code d’erreur suivants :

| Code d'erreur           | Message                                                | État HTTP |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | Demande non valide.                                       | 400         |
| InvalidArgument      | Argument non valide.                                      | 400         |
| Interdit            | Accès interdit en raison d’une stratégie ou d’une autre configuration. | 403         |
| NotFound             | La ressource est introuvable.                                    | 404         |
| MethodNotAllowed     | La méthode HTTP demandée n’est pas autorisée.              | 405         |
| Conflit             | La demande n’a pas pu aboutir en raison d’un conflit.  | 409         |
| UnsupportedMediaType | Le type de contenu de la demande n’est pas pris en charge.                 | 415         |
| InternalServerError  | Une erreur inattendue s’est produite.                          | 500         |
| ServiceUnavailable   | Une erreur temporaire s’est produite. Réessayez.      | 503         |

Lorsque cela est possible, des détails supplémentaires sont spécifiés dans la propriété *inner error*.

| Code d’erreur principal | Code d’erreur interne | Message |
| -------------- | ---------------- | ------- |
| Conflit | ModelExists | Un modèle de ce nom existe déjà. |
| Interdit | AuthorizationFailed | Échec de l’autorisation : {details} |
| Interdit | InvalidDataProtectionKey | La clé de protection des données est incorrecte : {details} |
| Interdit | OutboundAccessForbidden | La demande contient un nom de domaine qui n’est pas autorisé par la stratégie de contrôle d’accès actuelle. |
| InternalServerError | Unknown | Erreur inconnue. |
| InvalidArgument | InvalidContentSourceFormat | La source de contenu est incorrecte : {details} |
| InvalidArgument | InvalidParameter | Le paramètre {parameterName} est incorrect : {details} |
| InvalidArgument | InvalidParameterLength | La longueur du paramètre {parameterName} ne doit pas dépasser {maxChars} caractères. |
| InvalidArgument | InvalidSasToken | La signature d’accès partagé est incorrecte : {details} |
| InvalidArgument | ParameterMissing | Le paramètre {parameterName} est requis. |
| InvalidRequest | ContentSourceNotAccessible | Le contenu n’est pas accessible : {details} |
| InvalidRequest | ContentSourceTimeout | Délai d’expiration lors de la réception du fichier du client. |
| InvalidRequest | DocumentModelLimit | Le compte ne peut pas créer plus de {maximumModels} modèles. |
| InvalidRequest | DocumentModelLimitComposed | Le compte ne peut pas créer un modèle avec plus de {details} modèles de composant. |
| InvalidRequest | InvalidContent | Le fichier est endommagé ou le format n’est pas pris en charge. Reportez-vous à la documentation pour consulter la liste des formats pris en charge. |
| InvalidRequest | InvalidContentDimensions | Les dimensions de l’image d’entrée sont hors limites. Reportez-vous à la documentation pour consulter les dimensions d’image prises en charge. |
| InvalidRequest | InvalidContentLength | L’image d’entrée est trop grande. Reportez-vous à la documentation pour consulter la taille de fichier maximale. |
| InvalidRequest | InvalidFieldsDefinition | Champs incorrects : {details} |
| InvalidRequest | InvalidTrainingContentLength | Le contenu d’entraînement contient {bytes} octets. L’entraînement est limité à {maxBytes} octets. |
| InvalidRequest | InvalidTrainingContentPageCount | Le contenu d’entraînement contient {pages} pages. L’entraînement est limité à {pages} pages. |
| InvalidRequest | ModelAnalyzeError | Impossible d’analyser à l’aide d’un modèle personnalisé : {details} |
| InvalidRequest | ModelBuildError | Impossible de générer le modèle : {details} |
| InvalidRequest | ModelComposeError | Impossible de composer le modèle : {details} |
| InvalidRequest | ModelNotReady | Le modèle n’est pas prêt pour l’opération demandée. Attendez la fin de l’entraînement ou recherchez des erreurs d’opération. |
| InvalidRequest | ModelReadOnly | Le modèle demandé est en lecture seule. |
| InvalidRequest | NotSupportedApiVersion | L’opération demandée requiert {minimumApiVersion} ou une version ultérieure. |
| InvalidRequest | OperationNotCancellable | L’opération ne peut plus être annulée. |
| InvalidRequest | TrainingContentMissing | Les données d’entraînement sont manquantes : {details} |
| InvalidRequest | UnsupportedContent | Le contenu n’est pas pris en charge : {details} |
| NotFound | ModelNotFound | Le modèle demandé est introuvable. Il a peut-être été supprimé ou est toujours en cours de génération. |
| NotFound | OperationNotFound | L’opération demandée est introuvable. L’identificateur est peut-être incorrect ou l’opération a peut-être expiré. |