---
title: Azure Event Grid sur Kubernetes - Distribution d’événements par lot
description: Cet article explique comment distribuer un lot d’événements à la destination.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 46ae19af49b827af857f5f224ee5f0013d620a43
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414590"
---
# <a name="event-grid-on-kubernetes---batch-event-delivery"></a>Event Grid sur Kubernetes - Distribuer des événements par lot 
Event Grid sur Kubernetes avec Azure Arc permet de distribuer plusieurs événements dans une même demande de distribution. Cette fonctionnalité permet d’augmenter le débit de distribution global sans augmenter les coûts par requête HTTP. La distribution d’événements par lot est désactivée par défaut et peut être activée en utilisant la configuration de l’abonnement aux événements. 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

> [!WARNING]
> La durée maximale autorisée pour traiter chaque demande de distribution ne change pas, même si le code du gestionnaire d’événements doit potentiellement faire plus de travail par demande traitée par lot. Par défaut, le délai de livraison est de 60 secondes.

## <a name="batch-event-delivery-policy"></a>Stratégie de distribution d’événements par lot
Le comportement de la distribution des événements par lots dans Event Grid sur Kubernetes peut être personnalisé selon l’abonnement aux événements, en modifiant les deux paramètres suivants :

- **Nombre maximal d’événements par lot**
    
    Ce paramètre fixe une limite supérieure au nombre d’événements qui peuvent être ajoutés à une demande de livraison traitée par lot.
- **Taille de lot par défaut en kilo-octets**
    
    Cet élément de configuration est utilisé pour contrôler davantage le nombre maximal de kilo-octets qui peuvent être envoyés par demande de distribution.

## <a name="batch-event-delivery-behavior"></a>Comportement de la distribution des événements par lot   

- **Tout ou aucun**

    Event Grid sur Kubernetes fonctionne avec une sémantique du tout ou rien. Il ne prend pas en charge la réussite partielle d’une distribution d’événements par lot. Les gestionnaires d’événements doivent veiller à demander seulement un nombre d’événements par lot ne dépassant pas ce qu’il peuvent raisonnablement gérer en 60 secondes.
- **Traitement par lot optimiste**

    Les paramètres de stratégie de traitement par lot n’imposent pas de limites strictes au comportement du traitement par lot : ils sont donc respectés dans la limite des possibilités. Lorsque les fréquences d’événements sont faibles, vous observerez souvent que la taille de lot est inférieure au nombre maximal d’événements demandés par lot.
- **La remise par lot est définie sur OFF (Désactivé) par défaut**

    Par défaut, Event Grid sur Kubernetes ajoute un seul événement à chaque demande de distribution. Pour activer la distribution d’événements par lot, définissez un des paramètres mentionnés plus haut dans l’article dans la charge utile de l’abonnement aux événements.
- **Valeurs par défaut**

    Lors de la création d’un abonnement aux événements, il n’est pas nécessaire de spécifier les deux paramètres (nombre maximal d’événements par lot et taille de lot approximative en kilo-octets). Si un seul paramètre est défini, Event Grid sur Kubernetes utilise des valeurs par défaut (configurables). 

## <a name="example"></a>Exemple
L’exemple suivant montre comment définir `maxEventsPerBatch` et `preferredBatchSizeInKilobytes` dans les propriétés de point de terminaison pour activer le traitement par lot. 

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les destinations et les gestionnaires pris en charge par Event Grid sur Azure Arc pour Kubernetes, consultez [Event Grid sur Kubernetes - Gestionnaires d’événements](event-handlers.md).