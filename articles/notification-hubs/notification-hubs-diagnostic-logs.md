---
title: Journaux de diagnostic Azure Notification Hubs | Microsoft Docs
description: Cet article fournit une vue d’ensemble de tous les journaux de diagnostics et d’opérations disponibles pour Azure Notification Hubs.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b532dca6ceba44a32132bf64b322e1b4764fd5fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416542"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Activer les journaux de diagnostics pour Notification Hubs

Quand vous commencez à utiliser votre espace de noms Azure Notification Hubs, vous souhaiterez peut-être superviser le moment et le déroulement de la création, de la suppression ou de l’accès à votre espace de noms. Cet article fournit une vue d’ensemble de tous les journaux de diagnostics et d’opérations disponibles.

Azure Notification Hubs prend actuellement en charge les journaux d’activité et d’opérations qui capturent les *opérations de gestion* effectuées sur l’espace de noms Azure Notification Hubs.

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux sont stockés au format JSON (JavaScript Object Notation) dans les deux emplacements suivants :

- **AzureActivity** : Affiche les journaux des opérations et actions qui sont effectuées en relation avec votre espace de noms dans le Portail Azure ou via des déploiements de modèles Azure Resource Manager.
- **AzureDiagnostics** : Affiche les journaux des opérations et actions qui sont effectuées en relation avec votre espace de noms à l’aide de l’API ou via les clients de gestion sur le kit SDK du langage.

Les chaînes JSON du journal de diagnostic incluent les éléments répertoriés dans le tableau suivant :

| Nom | Description |
| ------- | ------- |
| time | Horodatage UTC du journal |
| resourceId | Chemin d’accès relatif à la ressource Azure |
| operationName | Nom de l'opération de gestion |
| catégorie | Catégorie du journal. Valeurs valides : `OperationalLogs` |
| callerIdentity | Identité de l’appelant qui a initié l’opération de gestion |
| resultType | État de l'opération de gestion. Valeurs valides : `Succeeded` ou `Failed` |
| resultDescription | Description de l’opération de gestion |
| correlationId | ID de corrélation de l’opération de gestion (si spécifié) |
| callerIpAddress | Adresse IP de l’appelant. Vide pour les appels provenant du portail Azure |

Voici un exemple de chaîne JSON du journal des opérations :

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

Le champ `callerIdentity` peut être vide, ou contenir une chaîne JSON dans l’un des formats suivants.

Pour les appels provenant du portail Azure, le champ `identity` est vide. Le journal peut être mis en corrélation avec les journaux d’activité pour identifier l’utilisateur connecté.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Pour les appels effectués via Azure Resource Manager le champ `identity` contient le nom d’utilisateur de l’utilisateur connecté.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Pour les appels à l’API REST Notification Hubs, le champ `identity` contient le nom de la stratégie d’accès utilisée pour générer le jeton SharedAccessSignature.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Événements et opérations capturés dans les journaux des opérations

Les journaux des opérations capturent toutes les opérations de gestion qui sont effectuées sur l’espace de noms Azure Notification Hubs. Les opérations de données ne sont pas capturées en raison du volume élevé d’opérations de données effectuées sur Azure Notification Hubs.

Les opérations de gestion suivantes sont capturées dans les journaux des opérations : 

| Étendue | Nom d’opération | Description de l'opération |
| :-- | :-- | :-- |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Répertorier les règles d’autorisation |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Supprimer une règle d'autorisation |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Afficher la liste des clés |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obtenir une règle d'autorisation |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Régénérer les clés |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Créer ou mettre à jour une règle d’autorisation |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/delete | Supprimer un espace de noms |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/read | Récupération d'espace de noms |
| Espace de noms | Microsoft.NotificationHubs/Namespaces/write | Créer ou mettre à jour un espace de noms |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Répertorier les règles d’autorisation |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Supprimer une règle d'autorisation |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Afficher la liste des clés |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Lire la règle d'autorisation |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Régénérer les clés |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Créer ou mettre à jour une règle d’autorisation |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/delete | Suppression d’un hub de notification |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Créer, mettre à jour ou obtenir des informations d’identification PNS |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obtention d’un hub de notification |
| Hub de notification | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Créer ou mettre à jour un hub de notification |

## <a name="enable-operational-logs"></a>Activer les journaux des opérations

Les journaux des opérations sont désactivés par défaut. Pour activer les journaux, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre espace de noms Azure Notification Hubs, puis sous **Supervision**, sélectionnez **Paramètres de diagnostics**.

   ![Le lien « Paramètres de diagnostics »](./media/notification-hubs-diagnostic-logs/image-1.png)

1. Dans le volet **Paramètres de diagnostic**, sélectionnez **Ajouter un paramètre de diagnostic**.  

   ![Le lien « Ajouter le paramètre de diagnostic »](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Configurez les paramètres de diagnostics en procédant comme suit :

   a. Dans la zone **Nom**, entrez un nom pour les paramètres de diagnostics.  

   b. Sélectionnez l’une des trois destinations suivantes pour vos journaux de diagnostics :  
   - Si vous sélectionnez **Envoyer à l’espace de travail Log Analytics**, vous devez spécifier l’instance de Log Analytics vers laquelle les diagnostics seront envoyés.  
   - Si vous sélectionnez **Archiver dans un compte de stockage**, vous devez configurer le compte de stockage dans lequel les journaux de diagnostics seront stockés.  
   - Si vous sélectionnez **Diffuser vers Event Hub**, vous devez configurer Event Hub vers lequel vous souhaitez diffuser les journaux de diagnostics.

   c. Activez la case à cocher **OperationalLogs**.

    ![Le volet « Paramètres de diagnostics »](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Sélectionnez **Enregistrer**.

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Les journaux d’activité s’affichent dans la cible d’archivage configurée, dans le volet **Journaux de diagnostics**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration des paramètres de diagnostics, consultez :
* [Vue d’ensemble des journaux de diagnostics Azure](../azure-monitor/platform/platform-logs-overview.md).

Pour en savoir plus sur Azure Notification Hubs, consultez :
* [Présentation d’Azure Notification Hubs](notification-hubs-push-notification-overview.md)

