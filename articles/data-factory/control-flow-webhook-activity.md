---
title: Activité de Webhook dans Azure Data Factory | Microsoft Docs
description: L’activité de Webhook ne continue pas l’exécution du pipeline jusqu'à ce qu’il valide le jeu de données attaché à certains critères Spécifie l’utilisateur.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764303"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Activité de Webhook dans Azure Data Factory
Vous pouvez utiliser une activité de raccordement web pour contrôler l’exécution des pipelines via votre code personnalisé. Utilisation de l’activité de webhook, les clients peuvent appeler un point de terminaison et passer une URL de rappel. L’exécution du pipeline attend le rappel à appeler avant de passer à l’activité suivante.

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
Nom | Nom de l’activité de raccordement web | Chaîne | Oui |
Type | Doit être définie sur **WebHook**. | Chaîne | Oui |
method | Méthode d’API REST pour le point de terminaison cible. | Chaîne. Types pris en charge : 'POST' | Oui |
url | Point de terminaison cible et chemin d’accès | Chaîne (ou expression avec resultType de chaîne). | Oui |
headers | En-têtes envoyés à la demande. Par exemple, pour définir la langue et le type sur une demande : « headers » : {« Accept-Language » : « en-us », « Content-Type » : « application/json »}. | Chaîne (ou expression avec resultType de chaîne) | Oui, l’en-tête Content-type est obligatoire. "headers":{ "Content-Type":"application/json"} |
body | Représente la charge utile envoyée au point de terminaison. | Le corps est retransmis à l’appel de retour URI doit être un JSON valide. Voir le schéma de la charge utile de demande dans la section [Schéma de la charge utile](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Oui |
Authentification | Méthode d’authentification utilisée pour appeler le point de terminaison. Types pris en charge sont « Basic » ou « ClientCertificate ». Pour en savoir plus, voir la section [Authentification](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Si l’authentification n’est pas obligatoire, excluez cette propriété. | Chaîne (ou expression avec resultType de chaîne) | Non  |
timeout | Délai d’attente pour l’activité de la &#39;callBackUri&#39; à appeler. La durée pendant laquelle l’activité attend 'callBackUri' à appeler. Valeur par défaut est 10mins (« 00 : 10:00 »). Le format est d.hh:mm:ss par exemple, de l’intervalle de temps | Chaîne | Non  |

## <a name="additional-notes"></a>Remarques supplémentaires

Azure Data Factory transmettra une propriété supplémentaire « callBackUri » dans le corps au point de terminaison url et s’attendent à cet uri à appeler avant la valeur de délai d’attente spécifiée. Si l’uri n’est pas appelé, l’activité échoue avec l’état « TimedOut ».

L’activité de raccordement web elle-même échoue uniquement lorsque l’appel au point de terminaison personnalisé échoue. N’importe quel message d’erreur peut être ajouté dans le corps du rappel et utilisé dans une activité ultérieure.

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory :

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
