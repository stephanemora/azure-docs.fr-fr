---
title: Utiliser l’API REST pour gérer les travaux dans Azure IoT Central
description: Comment utiliser l’API REST IoT Central pour créer et gérer les travaux dans une application
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 47548b3c7e1fa7503e3f30c155755d6fe4f29031
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667043"
---
# <a name="how-to-use-the-iot-central-rest-api-to-create-and-manage-jobs"></a>Comment utiliser l’API REST IoT Central pour créer et gérer les travaux

L’API REST IoT Central vous permet de développer des applications clientes qui s’intègrent à des applications IoT Central. Vous pouvez utiliser l’API REST pour gérer et gérer les travaux dans votre application IoT Central. L’API REST vous permet de :

- Répertorier les travaux et afficher les détails d’un travail dans votre application.
- Créer des travaux dans votre application.
- Arrêter, reprendre et réexécuter des travaux dans votre application.

> [!IMPORTANT]
> L’API des travaux est actuellement en préversion. Tous les appels d’API REST décrits dans cet article doivent inclure `?api-version=preview`.

Cet article décrit comment utiliser l’API `/jobs/{job_id}` pour contrôler les appareils en bloc. Vous pouvez également contrôler les appareils individuellement.

Chaque appel d’API REST IoT Central nécessite un en-tête d’autorisation. Pour plus d’informations, consultez [Comment authentifier et autoriser les appels d’API REST d’IoT Central](howto-authorize-rest-api.md).

Pour obtenir la documentation de référence sur l’API REST IoT Central, consultez [Informations de référence sur l’API REST d’Azure IoT Central](/rest/api/iotcentral/).

> [!TIP]
> L’[API en préversion](/rest/api/iotcentral/1.1-previewdataplane/jobs) inclut la prise en charge de la nouvelle [fonctionnalité « organisations »](howto-create-organizations.md).

Pour savoir comment créer et gérer des travaux dans l’interface utilisateur, consultez [Gérer des appareils en bloc dans votre application Azure IoT Central](howto-manage-devices-in-bulk.md).

## <a name="job-payloads"></a>Charges utiles de travail

Nombre des API décrites dans cet article incluent une définition qui ressemble à l’extrait JSON suivant :

```json
{
  "id": "job-014",
  "displayName": "Set target temperature",
  "description": "Set target temperature for all thermostat devices",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "56"
    }
  ],
  "status": "complete"
}
```

Le tableau suivant décrit les champs de l’extrait JSON précédent :

| Champ | Description |
| ----- | ----------- |
| `id` | ID unique du travail dans votre application. |
| `displayName` | Nom d’affichage du travail dans votre application. |
| `description` | Description du travail. |
| `group` | ID du groupe d’appareils auquel le travail s’applique. Utilisez l’API REST `deviceGroups` en préversion pour obtenir la liste des groupes d’appareils dans votre application. |
| `status` | [État](howto-manage-devices-in-bulk.md#view-job-status) du travail. Valeurs possibles : `complete`, `cancelled`, `failed`, `pending`, `running`, `stopped`. |
| `batch` | Si elle est présente, cette section définit comment [regrouper par lots](howto-manage-devices-in-bulk.md#create-and-run-a-job) les appareils dans le travail. |
| `batch/type` | La taille de chaque lot correspond au `percentage` du nombre total d’appareils dans le groupe ou au `number` d’appareils. |
| `batch/value` | Pourcentage d’appareils ou nombre d’appareils dans chaque lot. |
| `cancellationThreshold` | Si elle est présente, cette section définit le [seuil d’annulation](howto-manage-devices-in-bulk.md#create-and-run-a-job) du travail. |
| `cancellationThreshold/batch` | `true` ou `false`. Si la valeur est true, le seuil d’annulation est défini pour chaque lot. Si la valeur est `false`, le seuil d’annulation s’applique à l’ensemble du travail. |
| `cancellationThreshold/type` | Le seuil d’annulation du travail est un `percentage` ou un `number` d’appareils. |
| `cancellationThreshold/value` | Pourcentage d’appareils ou nombre d’appareils qui définit le seuil d’annulation. |
| `data` | Tableau d’opérations effectuées par le travail. |
| `data/type` | Valeurs possibles : `PropertyJobData`, `CommandJobData` ou `CloudPropertyJobData` |
| `data/target` | ID de modèle des appareils cibles. |
| `data/path` | Nom de la propriété, de la commande ou de la propriété cloud. |
| `data/value` | Valeur de propriété à définir ou paramètre de commande à envoyer. |

## <a name="get-job-information"></a>Obtenir des informations sur un travail

Utilisez la requête suivante pour récupérer la liste des travaux dans votre application :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs?api-version=preview
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "value": [
    {
      "id": "job-006",
      "displayName": "Set customer name",
      "description": "Set the customer name cloud property",
      "group": "4fcbec3b-5ee8-4324-855d-0f03b56bcf7f",
      "data": [
        {
          "type": "CloudPropertyJobData",
          "target": "dtmi:modelDefinition:bojo9tfju:yfvu5gv2vl",
          "path": "CustomerName",
          "value": "Contoso"
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-005",
      "displayName": "Set target temperature",
      "description": "Set target temperature device property",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "data": [
        {
          "type": "PropertyJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "targetTemperature",
          "value": 56
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-004",
      "displayName": "Run device report",
      "description": "Call command to run the device reports",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "batch": {
        "type": "percentage",
        "value": 25
      },
      "cancellationThreshold": {
        "type": "percentage",
        "value": 10,
        "batch": false
      },
      "data": [
        {
          "type": "CommandJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "getMaxMinReport",
          "value": "2021-06-15T05:00:00.000Z"
        }
      ],
      "status": "complete"
    }
  ]
}
```

Utilisez la requête suivante pour récupérer un travail individuel par ID :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004?api-version=preview
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "id": "job-004",
  "displayName": "Run device report",
  "description": "Call command to run the device reports",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "CommandJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "getMaxMinReport",
      "value": "2021-06-15T05:00:00.000Z"
    }
  ],
  "status": "complete"
}
```

Utilisez la requête suivante pour récupérer les détails des appareils dans un travail :

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004/devices?api-version=preview
```

La réponse à cette demande ressemble à l’exemple suivant :

```json
{
  "value": [
    {
      "id": "therm-01",
      "status": "completed"
    },
    {
      "id": "therm-02",
      "status": "completed"
    },
    {
      "id": "therm-03",
      "status": "completed"
    },
    {
      "id": "therm-04",
      "status": "completed"
    }
  ]
}
```

## <a name="create-a-job"></a>Créer un travail

Utilisez la requête suivante pour récupérer les détails des appareils dans un travail :

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006?api-version=preview
{
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ]
}
```

La réponse à cette demande se présente comme dans l’exemple suivant. L’état initial du travail est `pending` :

```json
{
  "id": "job-006",
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ],
  "status": "pending"
}
```

## <a name="stop-resume-and-rerun-jobs"></a>Arrêter, reprendre et réexécuter des travaux

Utilisez la requête suivante pour arrêter un travail en cours d’exécution :

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/stop?api-version=preview
```

Si la requête réussit, elle retourne une réponse `204 - No Content`.

Utilisez la requête suivante pour reprendre un travail arrêté :

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/resume?api-version=preview
```

Si la requête réussit, elle retourne une réponse `204 - No Content`.

Utilisez la commande suivante pour réexécuter un travail existant sur les appareils défaillants :

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/rerun/rerun-001?api-version=preview
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les travaux avec l’API REST, l’étape suivante suggérée consiste à découvrir comment [Gérer les applications IoT Central avec l’API REST](/learn/modules/manage-iot-central-apps-with-rest-api/).
