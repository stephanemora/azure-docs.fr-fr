---
title: Activité Webhook dans Azure Data Factory
description: L’activité Webhook interrompt l’exécution du pipeline jusqu’à ce qu’elle valide le jeu de données attaché avec certains critères spécifiés par l’utilisateur.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: b2f7c35e6ddb3c6ed0a3032d7ea6d4c53043c17b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122915"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Activité Webhook dans Azure Data Factory
Vous pouvez utiliser une activité de webhook pour contrôler l’exécution des pipelines dans votre code personnalisé. Grâce à l’activité de webhook, les clients peuvent appeler un point de terminaison et passer une URL de rappel. L’exécution du pipeline attend que le rappel soit appelé avant de passer à l’activité suivante.

## <a name="syntax"></a>Syntaxe

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Propriétés type



Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité de webhook | Chaîne | OUI |
Type | Doit avoir la valeur **WebHook** | Chaîne | OUI |
method | Méthode d’API REST pour le point de terminaison cible. | Chaîne. Types pris en charge : 'POST' | OUI |
url | Point de terminaison cible et chemin d’accès | Chaîne (ou expression avec resultType de chaîne). | OUI |
headers | En-têtes envoyés à la demande. Par exemple, pour définir la langue et le type sur une requête : "headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }. | Chaîne (ou expression avec resultType de chaîne) | Oui, l’en-tête Content-type est obligatoire. "headers":{ "Content-Type":"application/json"} |
body | Représente la charge utile envoyée au point de terminaison. | Code JSON valide (ou expression avec resultType de JSON). Voir le schéma de la charge utile de demande dans la section [Schéma de la charge utile](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | OUI |
Authentification | Méthode d’authentification utilisée pour appeler le point de terminaison. Les types pris en charge sont « Basic » et « ClientCertificate ». Pour en savoir plus, voir la section [Authentification](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Si l’authentification n’est pas obligatoire, excluez cette propriété. | Chaîne (ou expression avec resultType de chaîne) | Non |
timeout | Durée pendant laquelle l’activité attendra que &#39;callBackUri&#39; soit rappelée. Durée pendant laquelle l’activité attendra que « callBackUri » soit rappelée. La valeur par défaut est 10 minutes (“00:10:00”). Le format est un intervalle de temps (autrement dit j.hh:mm:ss) | Chaîne | Non |
Signaler l’état lors du rappel | Autorise l’utilisateur à signaler l’état d’échec de l’activité de webhook, marquant alors l’activité comme ayant échoué | Boolean | Non |

## <a name="additional-notes"></a>Remarques supplémentaires

Azure Data Factory transmettra une propriété supplémentaire « callBackUri » dans le corps au point de terminaison d’URL, et s’attendra à ce que cet URI soit rappelé avant la valeur de délai d’attente spécifiée. Si l’URI n’est pas appelé, l’activité échoue avec l’état « TimedOut ».

L’activité de webhook proprement dite échoue quand l’appel au point de terminaison personnalisé échoue. Tout message d’erreur peut être ajouté dans le corps du rappel et utilisé dans une activité ultérieure.

Le corps retransmis à l’URI de rappel doit être du code JSON valide. Vous devez définir l’en-tête Content-Type sur `application/json`.

Lorsque vous utilisez l’option « Signaler l’état lors du rappel », vous devez ajouter l’extrait de code suivant au corps lors du rappel :

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Étapes suivantes

Consultez les autres activités de flux de contrôle prises en charge par Data Factory :

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
