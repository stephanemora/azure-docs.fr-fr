---
title: Schéma JSON de jumeau de module Azure Video Analyzer
description: Cet article fournit une vue d’ensemble du schéma JSON de jumeau de module Azure Video Analyzer.
ms.topic: how-to
ms.date: 04/30/2021
ms.openlocfilehash: b87074040a50f0b3a68a71e987e4cac3c3b48398
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386140"
---
# <a name="module-twin-configuration-schema"></a>Schéma de configuration de jumeau de module

Les jumeaux d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). Azure IoT Hub conserve un jumeau d’appareil pour chaque appareil que vous y connectez. Pour une explication détaillée, consultez [Comprendre et utiliser les jumeaux de module dans IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md).

Cette rubrique décrit le schéma JSON de jumeau de module Azure Video Analyzer.

## <a name="module-twin-properties"></a>Propriétés de jumeau de module

Azure Video Analyzer expose les propriétés de jumeau de module suivantes.

| Propriété                    | Obligatoire | Dynamique | Description                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | Oui      | Non      | Jeton d’authentification permettant de valider le module de périphérie et de provisionner les services cloud (notamment l’accès au compte Video Analyzer) |
| `ApplicationDataDirectory`    | Oui      | Non      | Chemin dans le système de fichiers du module, mappé à un volume monté pour une configuration persistante.       |
| `DiagnosticsEventsOutputName` | Non       | Oui     | Sortie hub pour les événements de diagnostic. (Vide signifie que les diagnostics ne sont pas publiés) |
| `OperationalEventsOutputName` | Non       | Oui     | Sortie hub pour les événements opérationnels. (Vide signifie que les événements opérationnels ne sont pas publiés) |
| `LogLevel`                    | Non       | Oui     | Une des valeurs suivantes : · Verbose · Information (par défaut) · Warning · Error · None |
| `LogCategories`               | Non       | Oui     | Liste des éléments suivants séparés par des virgules : Application, MediaPipeline, Events Par défaut : Application, Events |
| `AllowUnsecuredEndpoints`     | Non       | Oui     | Paramètre booléen autorisant la création de topologies avec des points de terminaison non sécurisés tels que `#Microsoft.VideoAnalyzer.UnsecuredEndpoint`. Valeur par défaut -true        |
| `TelemetryOptOut`             | Non       | Non     | Paramètre booléen permettant de refuser l’envoi de télémétries. Valeur par défaut -false       |
| `DebugLogsDirectory`          | Non       | Oui     | Répertoire pour les journaux de débogage. S’il est présent, les journaux sont générés, s’il n’est pas présent, les journaux de débogage sont désactivés.       |

Les propriétés dynamiques peuvent être mises à jour sans redémarrage du module. 

Pour plus d’informations sur le rôle des paramètres de diagnostic facultatifs, consultez l’article [Surveillance et journalisation](monitor-log-edge.md).

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

[Méthodes directes](direct-methods.md)
