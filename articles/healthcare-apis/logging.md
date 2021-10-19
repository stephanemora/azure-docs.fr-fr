---
title: Journalisation des API de santé Azure
description: Cet article explique comment fonctionne la journalisation et comment activer la journalisation pour les API de santé Azure
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: b71e6088a1a60c17ec16c1a5a265cf20fec53777
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787524"
---
# <a name="logging-for-azure-healthcare-apis-preview"></a>Journalisation des API Azure Healthcare (version préliminaire)

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

la plateforme Azure propose trois types de journaux : journaux d’activité, journaux de ressources et journaux de Azure Active Directory. Pour plus d’informations sur les [journaux d’activité](../azure-monitor/essentials/platform-logs-overview.md), consultez. Dans cet article, vous allez découvrir comment fonctionne la journalisation pour les API de santé Azure.

## <a name="auditlogs"></a>AuditLogs
Alors que les journaux d’activité sont disponibles pour chaque ressource Azure à partir de la Portail Azure, les API de santé émettent des journaux de ressources, qui incluent deux catégories de journaux, AuditLogs et DiagnosticLogs.

- AuditLogs fournit des pistes d’audit pour les services de santé, par exemple l’adresse IP et l’URL de ressource de l’appelant lorsqu’un utilisateur ou une application accède au service FHIR. Chaque service émet les propriétés requises et implémente éventuellement des propriétés supplémentaires.
- DiagnosticLogs fournit des informations sur le fonctionnement du service, par exemple, le niveau de journalisation (information, avertissement ou erreur) et le message de journal.

Actuellement, les API de santé prennent uniquement en charge AuditLogs pour la version préliminaire publique. DiagnosticLogs sera disponible lorsque le service sera mis à la disposition générale.

Vous trouverez ci-dessous un exemple de l’AuditLog.

```
{
    "time": "2021-08-02 16:01:29Z",
    "resourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/xxx/PROVIDERS/MICROSOFT.HEALTHCAREAPIS/SERVICES/xxx",
    "operationName": "Microsoft.HealthcareApis/services/fhir-R4/search-type",
    "category": "AuditLogs",
    "resultType": "Started",
    "resultSignature": 0,
    "durationMs": 0,
    "callerIpAddress": "::ffff:73.164.17.31",
    "correlationId": "5d04211aaf172d43b83d9eb500464ec5",
    "identity": {
        "claims": {
            "iss": "https://sts.windows.net/xxx/",
            "oid": "xxx"
        }
    },
    "level": "Informational",
    "location": "South Central US",
    "uri": "https://xxx.azurehealthcareapis.com:443/Patient",
    "properties": {
        "fhirResourceType": "Patient"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez les dernières mesures prises en charge pour les API de santé avec Azure Monitor [ici](../azure-monitor/essentials/metrics-supported.md).

Pour plus d’informations sur les journaux et les métriques du service DICOM, voir [ici](./dicom/enable-diagnostic-logging.md).

pour plus d’informations sur les journaux et les métriques de IoT Connector, voir [ici](./azure-api-for-fhir/iot-metrics-display.md).