---
title: Fonctionnalités de l’API REST FHIR pour l’API Azure pour FHIR
description: Cet article décrit les interactions et les fonctionnalités RESTful pour l’API Azure pour FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: c69c3838693c3e59aa7a024c0a67c28c6f1a5d21
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187552"
---
# <a name="fhir-rest-api-capabilities-for-azure-api-for-fhir"></a>Fonctionnalités de l’API REST FHIR pour l’API Azure pour FHIR

Dans cet article, nous allons aborder certaines des nuances des interactions RESTful de l’API Azure pour FHIR.


## <a name="conditional-createupdate"></a>Création/mise à jour conditionnelle

L’API Azure pour FHIR prend en charge les mises à jour de création, conditionnelle, de mise à jour et conditionnelles définies par la spécification FHIR. Un en-tête utile dans ces scénarios est l’en-tête [If-Match](https://www.hl7.org/fhir/http.html#concurrency) . L' `If-Match` en-tête est utilisé et valide la version mise à jour avant d’effectuer la mise à jour. Si `ETag` ne correspond pas à la attendue `ETag` , le message d’erreur *412 échec de la précondition a* été généré. 

## <a name="delete"></a>Supprimer

La [suppression](https://www.hl7.org/fhir/http.html#delete) définie par la spécification FHIR nécessite qu’après la suppression, les lectures suivantes non spécifiques à la version d’une ressource renvoient un code d’état HTTP 410 et la ressource n’est plus trouvée via la recherche. En outre, l’API Azure pour FHIR vous permet de supprimer entièrement (y compris l’historique) de la ressource. Pour supprimer entièrement la ressource, vous pouvez passer une valeur de paramètre `hardDelete` définie sur true (`DELETE {server}/{resource}/{id}?hardDelete=true`). Si vous ne transmettez pas ce paramètre ou que vous affectez `hardDelete` la valeur false, les versions historiques de la ressource seront toujours disponibles.

> [!NOTE]
> Si vous souhaitez supprimer uniquement l’historique, l’API Azure pour FHIR prend en charge une opération personnalisée, `$purge-history` , qui vous permet de supprimer l’historique d’une ressource. 

## <a name="conditional-delete"></a>Suppression conditionnelle

En plus de supprimer, l’API Azure pour FHIR prend en charge la suppression conditionnelle, qui vous permet de passer un critère de recherche pour supprimer une ressource. Par défaut, la suppression conditionnelle vous permet de supprimer un élément à la fois. Vous pouvez également spécifier le `_count` paramètre pour supprimer jusqu’à 100 éléments à la fois. Voici quelques exemples d’utilisation de la suppression conditionnelle.

Pour supprimer un seul élément à l’aide de la suppression conditionnelle, vous devez spécifier des critères de recherche qui renvoient un seul élément.

DELETE `https://{{hostname}}/Patient?identifier=1032704`

Vous pouvez effectuer la même recherche, mais inclure hardDelete = true pour supprimer également tout l’historique.

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

Si vous souhaitez supprimer plusieurs ressources, vous pouvez inclure `_count=100` , qui supprimera jusqu’à 100 ressources correspondant aux critères de recherche.

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>Correctif logiciel et correctif conditionnel

Le correctif est une opération RESTful importante lorsque vous devez mettre à jour uniquement une partie de la ressource FHIR. L’utilisation de patch vous permet de spécifier le ou les éléments que vous souhaitez mettre à jour dans la ressource sans avoir à mettre à jour l’intégralité de l’enregistrement. FHIR définit trois types de solutions pour corriger les ressources dans FHIR : correctif JSON, correctif XML et correctif logiciel FHIR Path. Le service FHIR prend en charge le correctif JSON et le correctif JSON conditionnel (ce qui vous permet d’appliquer un correctif à une ressource en fonction d’un critère de recherche au lieu d’un ID). Pour découvrir quelques exemples d’utilisation du correctif JSON, reportez-vous à l’exemple de [fichier Rest](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http).

> [!NOTE]
> Lors de l’utilisation de `PATCH` STU3, si vous demandez un bundle d’historique, les ressources corrigées `Bundle.entry.request.method` sont mappées à `PUT` . Cela est dû au fait que STU3 ne contient pas de définition pour le `PATCH` verbe dans le [jeu de valeurs HTTPVerb](http://hl7.org/fhir/STU3/valueset-http-verb.html).

### <a name="testing-patch"></a>Test du correctif

Dans Patch, il existe une opération de test qui vous permet de vérifier qu’une condition est vraie avant d’effectuer le correctif. Par exemple, si vous souhaitez définir un patient décédé, uniquement s’ils n’étaient pas déjà marqués comme défunts, vous pouvez utiliser l’exemple ci-dessous : 

Contenu du correctif `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` -type : `application/json-patch+json`

```
[
    {
        “op”: “test”,
        “path”: “/deceasedBoolean”,
        “value”: false
    },
    {
        “op”: “replace”
        “path”: “/deceasedBoolean”,
        “value”: true
    }
]

```

### <a name="patch-in-bundles"></a>Correctifs dans les offres groupées

Par défaut, le correctif JSON n’est pas pris en charge dans les ressources de regroupement. Cela est dû au fait qu’un bundle ne prend en charge que les ressources FHIR et que le correctif JSON n’est pas une ressource FHIR. Pour contourner ce conflit, nous traiterons les ressources binaires avec un type de contenu `"application/json-patch+json"` comme encodage Base64 de la chaîne JSON lorsqu’un bundle est exécuté. Pour plus d’informations sur cette solution de contournement, connectez-vous à [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request). 

Dans l’exemple ci-dessous, nous voulons changer le sexe du patient en femme. Nous avons pris le correctif JSON `[{"op":"replace","path":"/gender","value":"female"}]` et l’avez encodé en base64.

POSTER `https://{FHIR-SERVICE-NAME}/` le contenu-type : `application/json`

```
{
    “resourceType”: “Bundle”
    “id”: “bundle-batch”,
    “type”: “batch”
    “entry”: [
        {
            “fullUrl”: “Patient/{PatientID}”,
            “resource”: {
                “resourceType”: “Binary”,
                “contentType”: “application/json-patch+json”,
                “data”: "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            “request”: { 
                “method”: “PATCH”,
                “url”: “Patient/{PatientID}”
            }
        }
    ]
}

```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris certaines des fonctionnalités REST de l’API Azure pour FHIR. Ensuite, vous pouvez en savoir plus sur les principaux aspects de la recherche de ressources dans FHIR. 

>[!div class="nextstepaction"]
>[Vue d’ensemble de la recherche dans l’API Azure pour FHIR](overview-of-search.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.