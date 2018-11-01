---
title: Mettre automatiquement à l’échelle les unités de débit Azure Event Hubs | Microsoft Docs
description: Activez la majoration automatique sur un espace de noms pour automatiquement mettre à l’échelle les unités de débit.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 19525086b1bd41afcc730fb3860d7a01875e4832
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986999"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Mettre automatiquement à l’échelle les unités de débit Azure Event Hubs

Azure Event Hubs est une plateforme hautement évolutive de diffusion de données en continu. Ainsi, l’utilisation des unités Event Hubs augmente souvent une fois le service démarré. Ces augmentations obligent à rehausser les [unités de débit](event-hubs-features.md#throughput-units) prédéterminées pour mettre à l’échelle Event Hubs et gérer des taux de transfert plus conséquents. La fonctionnalité **Majoration automatique** d’Event Hubs augmente automatiquement la taille des instances en augmentant le nombre d’unités de débit pour répondre aux besoins d’utilisation. Cette opération permet d’éviter les situations de limitation, dans lesquelles :

* Les taux d’entrée de données sont supérieurs aux unités de débit définies.
* Les taux de demande de sortie de données sont supérieurs aux unités de débit définies.

Le service Event Hubs augmente le débit quand la charge dépasse le seuil minimal, sans qu’aucune demande échoue avec des erreurs ServerBusy.

## <a name="how-auto-inflate-works"></a>Fonctionnement de la majoration automatique

Le trafic Event Hubs est contrôlé par les [unités de débit](event-hubs-features.md#throughput-units). Une unité de débit autorise 1 Mo/s en entrée et le double en sortie. Les unités Event Hub standard peuvent être configurées avec 1 à 20 unités de débit. La majoration automatique vous permet de démarrer petit avec le nombre d’unités de débit minimal exigé que vous choisissez. Ensuite, la fonctionnalité met automatiquement à l’échelle le nombre d’unités de débit dont vous avez besoin sur la limite maximale, selon l’augmentation de votre trafic. La majoration automatique vous permet de bénéficier des avantages suivants :

- Un mécanisme de mise à l’échelle efficace pour démarrer avec la valeur minimale et monter en puissance à mesure de la croissance de votre activité.
- Mise à l’échelle automatique sur à la limite supérieure spécifiée sans problèmes de limitation.
- Contrôle amélioré de la mise à l’échelle, car vous contrôlez le moment et la quantité de la mise à l’échelle.

## <a name="enable-auto-inflate-on-a-namespace"></a>Activer la majoration automatique sur un espace de noms

Vous pouvez activer et désactiver la majoration automatique sur un espace de noms Event Hubs, en appliquant l’une des méthodes suivantes :

- Le [portail Azure](https://portal.azure.com).
- [Un modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Activer la majoration automatique via le portail

Vous pouvez activer la fonctionnalité de majoration automatique lors de la création d’un espace de noms Event Hub :
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Une fois cette option activée, vous pouvez commencer par utiliser le nombre minimal d’unités de débit, puis monter en puissance à mesure que vos besoins d’utilisation augmentent. La limite supérieure de la majoration n’affecte pas immédiatement les prix, qui dépendent du nombre d’unités de débit utilisées par heure.

Vous pouvez également activer la majoration automatique à l’aide de l’option **Mettre à l’échelle** dans le panneau des paramètres du portail :
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)


> [!NOTE]
> Quand vous appliquez la configuration avec augmentation automatique afin d’augmenter les unités de débit, le service Event Hubs émet des journaux de diagnostic qui vous indiquent quand et pourquoi le débit a augmenté. Pour activer la journalisation des diagnostics pour un hub d’événements, sélectionnez **Paramètres de diagnostic** dans le menu de gauche de la page Hub d’événements dans le portail Azure. Pour plus d’informations, consultez [Configurer les journaux de diagnostic pour un hub d’événements Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Activer la majoration automatique à l’aide d’un modèle Azure Resource Manager

Vous pouvez activer la majoration automatique durant le déploiement d’un modèle Azure Resource Manager. Par exemple, définissez la propriété `isAutoInflateEnabled` sur **true** et définissez `maximumThroughputUnits` sur 10. Par exemple : 

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Pour accéder au modèle complet, consultez le modèle [Create Event Hubs namespace and enable inflate](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) (Créer un espace de noms Event Hubs et activer la majoration) sur GitHub.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)

