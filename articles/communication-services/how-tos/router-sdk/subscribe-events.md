---
title: S’abonner aux événements dans Job Router
titleSuffix: An Azure Communication Services how-to guide
description: Utiliser les kits de développement logiciel (SDK) Azure Communication Services pour vous abonner aux événements Job Router depuis Event Grid
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 8e2cff056a6fb96f5cbcdbaea6be4c4257c3752b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129032"
---
# <a name="subscribe-to-job-router-events"></a>S’abonner aux événements Job Router

Ce guide décrit les étapes à suivre pour s’abonner à des événements Job Router à partir de votre abonnement Azure Communication Services Event Grid. La réception des événements est une fonctionnalité critique que vos applications personnalisées devront exécuter. Les actions que Job Router effectue sur les travaux que vous envoyez se produisent de façon asynchrone et, tandis que le kit de développement logiciel (SDK) fournit des points de terminaison pour interroger le statut et l’état des objets du système, la création d’une application personnalisée réactive pilotée par les événements présente des avantages significatifs.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Facultatif : suivez le guide de démarrage rapide pour [commencer à utiliser Job Router](../../quickstarts/router/get-started-router.md).
- Installer le [client Azure Resource Manager](https://github.com/projectkudu/ARMClient)
- Examiner l'[exemple de projet GitHub à l’aide de la visionneuse Event Grid](https://github.com/Azure/communication-preview/tree/master/samples/Job-Router/Event-Grid-Viewer)

## <a name="create-an-event-grid-subscription"></a>Créer un abonnement Event Grid

> [!NOTE]
> Les scripts suivants sont exécutés à l’aide de PowerShell

**Connectez-vous à votre compte Azure**

```powershell
armclient azlogin
```

**Définissez un nom d’abonnement et de groupe de ressources**
```powershell
$env:SUB = 'subscriptions/<insert_subscription_id>'
$env:RG = 'resourcegroups/<insert_resource_group_name>'
```

**(Facultatif) Répertorier toutes les ressources ACS dans l’abonnement**
```powershell
armclient get "/$env:SUB/$env:RG/providers/Microsoft.Communication/communicationservices?api-version=2020-08-20"
```
**Sortie**

:::image type="content" source="media/create-subscription-output.png" alt-text="Résultat de commande PowerShell":::

Comme nous pouvons le voir, il n’existe actuellement qu’une seule ressource Azure Communication Services sous l’abonnement et le groupe de ressources donnés.

**Définir le nom de la ressource ACS**

Sélectionnez le nom de votre ressource Azure Communication Services. Par exemple, si le point de terminaison de votre ressource est `https://contoso.communication.azure.net`, définissez `ACS_RESOURCE_NAME` sur le préfixe du nom DNS ; `contoso`.

```powershell
$env:ACS_RESOURCE_NAME = "<insert_acs_resource_name>"
```

**(Facultatif) Répertorier les abonnements aux événements existants**

Vérifier s’il existe des abonnements aux événements existants

```powershell
$env:ACS_RESOURCE_ARM_ID = "/$env:SUB/$env:RG/providers/Microsoft.Communication/CommunicationServices/$env:ACS_RESOURCE_NAME"
$env:API_VERSION = "?api-version=2020-06-01"
$env:EVENT_SUBSCRIPTIONS_PATH = "providers/Microsoft.EventGrid/eventSubscriptions"

armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```
:::image type="content" source="media/check-subscriptions.png" alt-text="Afficher les abonnements existants":::

Comme nous pouvons le voir, aucun abonnement aux événements n’a été créé pour cette ressource.

**Créer un nouvel abonnement aux événements de routeur**

Copiez et collez la charge utile json suivante dans un fichier texte nommé `test.json`.

*Exemple de charge utile*
```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>"
      }
    },
    "filter": {
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobReceived",
        "Microsoft.Communication.RouterJobClassified",
        "Microsoft.Communication.RouterJobLabelsUpdated",
        "Microsoft.Communication.RouterJobClassificationFailed",
        "Microsoft.Communication.RouterJobCompleted",
        "Microsoft.Communication.RouterJobClosed",
        "Microsoft.Communication.RouterJobCancelled",
        "Microsoft.Communication.RouterJobExceptionTriggered",
        "Microsoft.Communication.RouterJobExceptionCleared",
        "Microsoft.Communication.RouterWorkerOfferIssued",
        "Microsoft.Communication.RouterWorkerOfferAccepted",
        "Microsoft.Communication.RouterWorkerOfferDeclined",
        "Microsoft.Communication.RouterWorkerOfferRevoked",
        "Microsoft.Communication.RouterWorkerOfferExpired",
        "Microsoft.Communication.RouterWorkerRegistered",
        "Microsoft.Communication.RouterWorkerDeregistered"
      ],
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    }
  }
}
```

**Définir le nom d’abonnement de l’événement**
```powershell
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_All"
```

**Créer un abonnement à l’événement**
```powershell
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test.json
```
**Sortie**

:::image type="content" source="media/create-subscription.png" alt-text="Créer un abonnement aux événements":::

Comme nous pouvons le voir, l’abonnement aux événements est en cours de création et est actuellement dans un état `Creating`. La création du réseau virtuel prend quelques secondes.

**Vérifier que l’abonnement aux événements a été créé avec succès**
```powershell
armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```

**Sortie**

:::image type="content" source="media/verify-subscription-created.png" alt-text="Vérifier que l’abonnement a été créé":::

Comme nous pouvons le voir, l’abonnement aux événements a été correctement créé pour tous les événements du routeur.

## <a name="creating-a-subscription-with-filters"></a>Création d’un abonnement avec des filtres

Lors de la configuration des abonnements aux événements, vous pouvez également utiliser des filtres avancés qui contrôlent les événements exacts qui doivent être envoyés à un abonnement particulier. Par exemple, étant donné l’exemple ci-dessous, seuls les événements `RouterJobCancelled` sont abonnés et envoyés au webhook dans les conditions suivantes :

- La **priorité** du travail est supérieure à `5`
- La tâche a été affectée à une file d’attente d’escalade
- Le travail a été annulé en raison d’une inactivité
- Le code de disposition pour les tâches annulées se termine par `_JobCancelledDueToInactivity`
- L’ID de la file d’attente se termine par le nom `EscalationQueue`

```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>",
        "maxEventsPerBatch": 1,
        "preferredBatchSizeInKilobytes": 64
      }
    },
    "filter": {
      "subjectEndsWith": "_JobCancelledDueToInactivity",
      "isSubjectCaseSensitive": true,
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobCancelled"
      ],
      "advancedFilters": [
        {
          "operatorType": "NumberGreaterThan",
          "key": "data.priority",
          "value": 5
        },
        {
          "operatorType": "StringEndsWith",
          "key": "data.queueId",
          "values": [
            "EscalationQueue"
          ]
        }
      ],
      "enableAdvancedFilteringOnArrays": true
    }
  }
}
```

Copiez et collez la charge utile JSON ci-dessus dans un texte et nommez-la `test-with-advanced-filters.json`, puis exécutez le code PowerShell suivant :

```powershell
$env:API_VERSION = "?api-version=2020-10-15-preview"
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_WithFilters"
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test-with-advanced-filters.json
```

**Sortie**

:::image type="content" source="media/advanced-filters.png" alt-text="Résultat de filtres avancés":::

> [!NOTE]
> Pour obtenir la liste complète des opérateurs qui peuvent être utilisés lors de la création d’abonnements, reportez-vous à [Event Grid | Filtrage d’événements - Opérateurs](../../../event-grid/event-filtering.md)

## <a name="events-catalog"></a>Catalogue d’événements

| Événements | Sous-domaine | Description |
|------|:------------:| ---------- |
| [`RouterJobReceived`](#microsoftcommunicationrouterjobreceived) | `Job` | Une nouvelle tâche a été créée pour le routage |
| [`RouterJobClassified`](#microsoftcommunicationrouterjobclassified)| `Job` | La stratégie de classification a été appliquée à une tâche |
| [`RouterJobLabelsUpdated`](#microsoftcommunicationrouterjoblabelsupdated) | `Job` | Les étiquettes de la tâche ont été modifiées |
| [`RouterJobClassificationFailed`](#microsoftcommunicationrouterjobclassificationfailed) | `Job` | Le routeur n’a pas pu classer la tâche à l’aide de la stratégie de classification |
| [`RouterJobCompleted`](#microsoftcommunicationrouterjobcompleted) | `Job` | Une tâche s’est terminée et est entrée en phase de conclusion |
| [`RouterJobClosed`](#microsoftcommunicationrouterjobclosed) | `Job` | Une tâche a été fermée et la phase de conclusion est terminée |
| [`RouterJobCancelled`](#microsoftcommunicationrouterjobcancelled) | `Job` | Une tâche a été annulée |
| [`RouterJobExceptionTriggered`](#microsoftcommunicationrouterjobexceptiontriggered) | `Job` | Une exception de tâche a été déclenchée |
| [`RouterJobExceptionCleared`](#microsoftcommunicationrouterjobexceptioncleared) | `Job` | Une exception de tâche a été supprimée |
| [`RouterWorkerOfferIssued`](#microsoftcommunicationrouterworkerofferissued) | `Worker` | Une tâche a été proposée à un travailleur |
| [`RouterWorkerOfferAccepted`](#microsoftcommunicationrouterworkerofferaccepted) | `Worker` | Une offre à un travailleur a été acceptée |
| [`RouterWorkerOfferDeclined`](#microsoftcommunicationrouterworkerofferdeclined) | `Worker` | Une offre à un travailleur a été refusée |
| [`RouterWorkerOfferRevoked`](#microsoftcommunicationrouterworkerofferrevoked)  | `Worker` | Une offre à un travailleur a été révoquée |
| [`RouterWorkerOfferExpired`](#microsoftcommunicationrouterworkerofferexpired)  | `Worker` | Une offre à un travailleur a expiré |
| [`RouterWorkerRegistered`](#microsoftcommunicationrouterworkerregistered)  | `Worker` | Le travailleur a été inscrit |
| [`RouterWorkerDeregistered`](#microsoftcommunicationrouterworkerderegistered)  | `Worker` | Le travailleur a été désinscrit |

## <a name="router-events"></a>Événements de routeur

### <a name="microsoftcommunicationrouterjobreceived"></a>Microsoft.Communication.RouterJobReceived

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "acdf8fa5-8ab4-4a65-874a-c1d2a4a97f2e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "PendingClassification",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "queue-id",
    "priority": 0,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | Valeurs possibles <ul> <li>PendingClassification</li> </ul> | Lorsque cet événement est envoyé, le processus de classification a déjà été exécuté. |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null` lorsque `queueId` est spécifié pour une tâche |
| queueId | `string` | ✔️ | | `null` lorsque `classificationPolicyId` est spécifié pour une tâche |
| priority | `int` | ✔️ | | Null lorsque `classificationPolicyId` est spécifié. Valeur non null s’il existe une affectation de file d’attente directe. |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterjobclassified"></a>Microsoft.Communication.RouterJobClassified

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/queue/{queue-id}",
  "data": {
    "queueInfo": {
      "id": "625fec06-ab81-4e60-b780-f364ed96ade1",
      "name": "Queue 1",
      "labels": {
        "Language": "en",
        "Product": "Office",
        "Geo": "NA"
      }
    },
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassified",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| queueInfo | `QueueInfo` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null` lorsque `queueId` est spécifié pour une tâche (affectation de file d’attente directe) |
| queueId | `string` | ✔️ | | `null` lorsque `classificationPolicyId` est spécifié pour une tâche |
| priority | `int` | ❌ |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterjoblabelsupdated"></a>Microsoft.Communication.RouterJobLabelsUpdated

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "Queued",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labelsAddedOrChanged": {
      "English": "5",
      "Office": "7"
    },
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken",
      "English": "5",
      "Office": "7"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobLabelsUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | Valeurs possibles <ul> <li>PendingClassification</li> <li>Mis en file d'attente.</li> <li>Attribué</li> <li>Completed</li> <li>Fermés</li> <li>Opération annulée</li> <li>ClassificationFailed</li> </ul> |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null` lorsque `queueId` est spécifié pour une tâche |
| queueId | `string` | ✔️ | | `null` lorsque `classificationPolicyId` est spécifié pour une tâche |
| priority | `int` | ❌ |
| labelsAddedOrChanged | `Dictionary<string, object>` | ✔️ | | Étiquettes ajoutées ou modifiées en fonction des entrées utilisateur. |
| étiquettes | `Dictionary<string, object>` | ✔️ | | Ensemble complet d’étiquettes associées au travail. |

### <a name="microsoftcommunicationrouterjobclassificationfailed"></a>Microsoft.Communication.RouterJobClassificationFailed

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/classificationpolicy/{classificationpolicy-id}",
  "data": {
    "errors": [
      {
        "code": null,
        "message": "Classification failed due to <reason>",
        "target": null,
        "innerError": null,
        "details": null
      }
    ],
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassificationFailed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| erreurs| `List<CommunicationError>` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ❌ | |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterjobcompleted"></a>Microsoft.Communication.RouterJobCompleted

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "queue-id",
    "assignmentId": "6f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
    "workerId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCompleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| assignmentId| `string` | ❌ | |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |
| workerId | `string` | ❌ | |

### <a name="microsoftcommunicationrouterjobclosed"></a>Microsoft.Communication.RouterJobClosed

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "",
    "dispositionCode": "",
    "workerId": "",
    "assignmentId": "",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClosed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| dispositionCode| `string` | ✔️ | | En fonction de l’entrée utilisateur |
| workerId | `string` | ❌ | |
| assignmentId | `string` | ❌ | |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterjobcancelled"></a>Microsoft.Communication.RouterJobCancelled

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/disposition/{disposition-code}",
  "data": {
    "note": "Cancelled due to <reason>",
    "dispositionCode": "100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "queueId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCancelled",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| remarque| `string` | ✔️ | | En fonction de l’entrée utilisateur |
| dispositionCode| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ✔️ | | Non NULL quand la tâche est annulée après une classification réussie. |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterjobexceptiontriggered"></a>Microsoft.Communication.RouterJobExceptionTriggered

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "exceptionRuleId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionTriggered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| exceptionRuleId| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterjobexceptioncleared"></a>Microsoft.Communication.RouterJobExceptionCleared

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionCleared",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| étiquettes | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

## <a name="worker-events"></a>Événements Worker

### <a name="microsoftcommunicationrouterworkerofferissued"></a>Microsoft.Communication.RouterWorkerOfferIssued

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "525fec06-ab81-4e60-b780-f364ed96ade1",
    "offerTimeUtc": "2021-06-23T02:43:30.3847144Z",
    "expiryTimeUtc": "2021-06-23T02:44:30.3847674Z",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferIssued",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId| `string` | ❌ |
| offerTimeUtc | `DateTimeOffset` | ❌ |
| expiryTimeUtc| `DateTimeOffset` | ❌ |
| JobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |

### <a name="microsoftcommunicationrouterworkerofferaccepted"></a>Microsoft.Communication.RouterWorkerOfferAccepted

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "assignmentId": "765fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferAccepted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| JobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |
| assignmentId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferdeclined"></a>Microsoft.Communication.RouterWorkerOfferDeclined

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferDeclined",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferrevoked"></a>Microsoft.Communication.RouterWorkerOfferRevoked

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferRevoked",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferexpired"></a>Microsoft.Communication.RouterWorkerOfferExpired

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferExpired",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerregistered"></a>Microsoft.Communication.RouterWorkerRegistered

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
    "totalCapacity": 100,
    "queueAssignments": [
      {
        "id": "MyQueueId2",
        "name": "Queue 3",
        "labels": {
          "Language": "en",
          "Product": "Office",
          "Geo": "NA"
        }
      }
    ],
    "labels": {
      "x": "111",
      "y": "111"
    },
    "channelConfigurations": [
      {
        "channelId": "FooVoiceChannelId",
        "capacityCostPerJob": 10
      }
    ]
  },
  "eventType": "Microsoft.Communication.RouterWorkerRegistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| totalCapacity | `int` | ❌ |
| queueAssignments| `List<QueueInfo>` | ❌ |
| étiquettes| `Dictionary<string, object>` | ✔️ | | En fonction de l’entrée utilisateur |
| channelConfigurations| `List<ChannelConfiguration>` | ❌ |

### <a name="microsoftcommunicationrouterworkerderegistered"></a>Microsoft.Communication.RouterWorkerDeregistered

[Retour au catalogue des événements](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
  },
  "eventType": "Microsoft.Communication.RouterWorkerDeregistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```
**Liste d'attributs**

| Attribut | Type | Nullable |Description | Notes |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
