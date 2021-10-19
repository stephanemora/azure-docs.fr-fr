---
title: Surveiller les données d’Azure Cache pour Redis à l’aide de paramètres de diagnostic
titleSuffix: Azure Cache for Redis
description: Découvrez comment utiliser les paramètres de diagnostic pour surveiller les adresses IP connectées à votre instance Azure Cache pour Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: e62215649a79f16fcb9cbfc20cfe16bc98f2251f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812432"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>Surveiller les données d’Azure Cache pour Redis à l’aide de paramètres de diagnostic

Les paramètres de diagnostic dans Azure sont utilisés pour collecter les journaux des ressources. Les journaux de ressources Azure sont émis par une ressource et fournissent des données riches et fréquentes sur le fonctionnement de cette ressource. Ces journaux sont capturés par requête et sont également appelés journaux de plans de données. Le contenu de ces journaux d’activité varie en fonction du type de ressource.

Azure Cache pour Redis utilise les paramètres de diagnostic Azure pour consigner des informations sur toutes les connexions clientes à votre cache. La journalisation puis l’analyse de ce paramètre de diagnostic vous permettent de comprendre qui se connecte à vos caches et fournissent le timestamp de ces connexions. Ces données peuvent être utilisées pour identifier l’étendue d’une violation de sécurité et pour effectuer un audit de sécurité.

Une fois configuré, votre cache commence à enregistrer les connexions clientes entrantes par adresse IP. Il enregistre également le nombre de connexions provenant de chaque adresse IP unique. Les journaux ne sont pas cumulatifs. Ils représentent des instantanés ponctuels pris à intervalles de dix secondes.

Vous pouvez activer les paramètres de diagnostic pour les instances Azure Cache pour Redis et envoyer les journaux des ressources aux destinations suivantes :

- **Event Hub** : Les paramètres de diagnostic ne peuvent pas accéder aux ressources de l’Event Hub lorsque les réseaux virtuels sont activés. Activez le paramètre **Autoriser les services Microsoft approuvés à contourner ce pare-feu ?** dans les Event Hubs pour accorder l’accès aux ressources de votre Event Hub. L’Event Hub doit se trouver dans la même région que le cache.
- **Compte de stockage** : Il doit se trouver dans la même région que le cache.

Pour plus d’informations sur les exigences de diagnostic, consultez [Paramètres de diagnostic](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD).

Lorsque vous envoyez des journaux de diagnostic vers l’une ou l’autre destination, les tarifs de données normaux pour l’utilisation du compte de stockage et de l’Event Hub vous sont facturés. Vous êtes facturé sous Azure Monitor et non sous Azure Cache pour Redis.
Pour plus d’informations, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Créer des paramètres de diagnostic via le Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Azure Cache pour Redis. Ouvrez le volet **Paramètres de diagnostic** sous la section **Supervision** à gauche. Sélectionnez ensuite **Ajouter un paramètre de diagnostic**.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="Sélectionner les diagnostics":::

1. Dans le volet **Paramètres de diagnostic**, sélectionnez **ConnectedClientList** dans **Détails de la catégorie**.

   |Category  | Définition  | Propriétés de clé   |
   |---------|---------|---------|
   |ConnectedClientList |  Adresses IP et nombre de clients connectés au cache, enregistrés à intervalles réguliers. | `connectedClients` et imbriqués dans : `ip`, `count`, `privateLinkIpv6` |
  
1. Après avoir sélectionné vos **détails de catégorie**, envoyez vos journaux à votre destination préférée. Sélectionnez les informations sur la droite.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="Sélectionnez Activer la ressource spécifique":::

## <a name="create-diagnostic-setting-via-rest-api"></a> Créer un paramètre de diagnostic via l’API REST

Utilisez l’API REST Azure Monitor pour créer un paramètre de diagnostic via la console interactive. Pour plus d’informations, voir [Créer ou mettre à jour](/rest/api/monitor/diagnostic-settings/create-or-update.md).

### <a name="request"></a>Requête

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>headers

   | Paramètres/en-têtes | Valeur/description |
   |---------|---------|
   | name | Nom de votre paramètre de diagnostic |
   | resourceUri | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | api-version | 2017-05-01-preview |
   | Content-Type | application/json |

### <a name="body"></a>Corps

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Créer un paramètre de diagnostic via Azure CLI

Pour créer un paramètre de diagnostic avec Azure CLI, utilisez la commande `az monitor diagnostic-settings create`. Pour plus d’informations sur cette commande et la description des paramètres, consultez [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../azure-monitor/essentials/diagnostic-settings.md).

```azurecli

az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace


```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez l’article [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).
