---
title: Effectuer un scale-up automatique des unités de débit dans Azure Event Hubs
description: Activez la fonctionnalité Majoration automatique sur un espace de noms pour effectuer un scale-up automatique des unités de débit (niveau standard).
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 6f45e5a023110132db9904da7d8b84f4906dd8b7
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110617183"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units-standard-tier"></a>Effectuer un scale-up automatique des unités de débit Azure Event Hubs (niveau standard) 
Azure Event Hubs est une plateforme hautement évolutive de diffusion de données en continu. Ainsi, l’utilisation des unités Event Hubs augmente souvent une fois le service démarré. Ces augmentations obligent à rehausser les [unités de débit](event-hubs-scalability.md#throughput-units) prédéterminées pour mettre à l'échelle Event Hubs et gérer des taux de transfert plus conséquents. La fonctionnalité **Majoration automatique** d'Event Hubs procède à un scale-up automatique en augmentant le nombre d'unités de débit pour répondre aux besoins d'utilisation. L’augmentation du nombre d’unités de débit vous empêche d’être confronté à des scénarios de limitation, dans lesquels :

* Les taux d'entrée des données sont supérieurs aux unités de débit définies. 
* Les taux de demande de sortie de données sont supérieurs aux unités de débit définies.

Le service Event Hubs augmente le débit quand la charge dépasse le seuil minimal, sans qu’aucune demande échoue avec des erreurs ServerBusy.

> [!NOTE]
> Pour en savoir plus sur le niveau **Premium**, consultez [Niveau Premium d'Event Hubs](event-hubs-premium-overview.md).

## <a name="how-auto-inflate-works-in-standard-tier"></a>Fonctionnalité Majoration automatique au niveau standard
Le trafic Event Hubs est contrôlé par les unités de débit (niveau standard). Pour connaître les limites, comme les taux d'entrée et de sortie par unité de débit, consultez [Quotas et limites Event Hubs](event-hubs-quotas.md). La fonctionnalité Majoration automatique vous permet de commencer petit avec le nombre minimum d'unités de débit requises. Ensuite, la fonctionnalité met automatiquement à l'échelle le nombre d'unités de débit requis en le définissant sur la limite maximale, conformément à l'augmentation de votre trafic. La majoration automatique vous permet de bénéficier des avantages suivants :

- Un mécanisme de mise à l’échelle efficace pour démarrer avec la valeur minimale et monter en puissance à mesure de la croissance de votre activité.
- Mise à l’échelle automatique sur à la limite supérieure spécifiée sans problèmes de limitation.
- Contrôle amélioré de la mise à l’échelle, car vous contrôlez le moment et la quantité de la mise à l’échelle.

 ## <a name="enable-auto-inflate-on-a-namespace"></a>Activer la majoration automatique sur un espace de noms
Vous pouvez activer ou désactiver la fonctionnalité Majoration automatique sur un espace de noms Event Hubs de niveau standard à l'aide du [portail Azure](https://portal.azure.com) ou d'un [modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate).

Pour un espace de noms Event Hubs Premium, la fonctionnalité est automatiquement activée. Vous ne pouvez pas la désactiver. 

> [!NOTE]
> Les espaces de noms Event Hubs de niveau De base ne prennent pas en charge la majoration automatique.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Sur le portail Azure, vous pouvez activer la fonctionnalité pendant ou après la création d'un espace de noms Event Hubs standard. Vous pouvez également définir des unités de débit pour l'espace de noms et spécifier la limite maximale de celles-ci. 

Vous pouvez activer la fonctionnalité Majoration automatique **lors de la création d'un espace de noms Event Hubs** : L'image suivante montre comment activer la fonctionnalité Majoration automatique pour un espace de noms de niveau standard, et comment configurer les unités de débit de départ et le nombre maximal d'unités de débit. 

:::image type="content" source="./media/event-hubs-auto-inflate/event-hubs-auto-inflate.png" alt-text="Capture d'écran illustrant l'activation de la fonctionnalité Majoration automatique lors de la création d'un espace de noms Event Hubs de niveau standard":::

Une fois cette option activée, vous pouvez commencer petit avec le nombre minimum d'unités de débit, puis monter en puissance à mesure que vos besoins augmentent. La limite supérieure de la majoration n'affecte pas immédiatement les prix, qui dépendent du nombre d'unités de débit utilisées par heure.

Pour activer la fonctionnalité Majoration automatique et modifier ses paramètres, procédez comme suit :

1. Sur la page **Espace de noms Event Hubs**, accédez au menu de gauche et sélectionnez **Mettre à l'échelle** sous **Paramètres**.
2. Dans la page **Paramètres de mise à l’échelle**, cochez la case **Activer** (si la fonctionnalité de mise à l’échelle automatique n’a pas été activée).

    :::image type="content" source="./media/event-hubs-auto-inflate/scale-settings.png" alt-text="Capture d'écran illustrant l'activation de la fonctionnalité Majoration automatique pour un espace de noms standard existant":::
3. Entrez le nombre **maximal** d’unités de débit ou utilisez la barre de défilement pour définir une valeur.
4. (facultatif) Mettez à jour nombre **minimal** d’unités de débit en haut de cette page.

> [!NOTE]
> Quand vous appliquez la configuration avec augmentation automatique afin d’augmenter les unités de débit, le service Event Hubs émet des journaux de diagnostic qui vous indiquent quand et pourquoi le débit a augmenté. Pour activer la journalisation des diagnostics pour un hub d’événements, sélectionnez **Paramètres de diagnostic** dans le menu de gauche de la page Hub d’événements dans le portail Azure. Pour plus d’informations, consultez [Configurer les journaux de diagnostic pour un hub d’événements Azure](event-hubs-diagnostic-logs.md).


## <a name="use-an-azure-resource-manager-template"></a>Utiliser un modèle Azure Resource Manager

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

Pour accéder au modèle complet, consultez le modèle [Create Event Hubs namespace and enable inflate](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate) (Créer un espace de noms Event Hubs et activer la majoration) sur GitHub.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](./event-hubs-about.md)
