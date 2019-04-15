---
title: Guide pratique pour déboguer les fonctions définies par l’utilisateur dans Azure Digital Twins | Microsoft Docs
description: Informations pratiques sur le débogage des fonctions définies par l’utilisateur dans Azure Digital Twins
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: 6122cd4507ed0883d1b78ca519269c25098e55ff
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961412"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Guide pratique pour utiliser des fonctions de débogage définies par l’utilisateur dans Azure Digital Twins

Cet article explique brièvement comment diagnostiquer et déboguer des fonctions définies par l’utilisateur. Il décrit ensuite certains des scénarios plus courants rencontrés lors de leur débogage.

>[!TIP]
> Consultez [Guide pratique pour configurer la supervision et la journalisation](./how-to-configure-monitoring.md) pour en savoir plus sur la configuration des outils de débogage dans Azure Digital Twins à l’aide des journaux d’activité, des journaux de diagnostic et d’Azure Monitor.

## <a name="debug-issues"></a>Résoudre les problèmes

Le fait de savoir comment diagnostiquer tout problème se produisant au sein de votre instance Azure Digital Twins vous aide à identifier efficacement celui-ci, ainsi que sa cause et une solution.

### <a name="enable-log-analytics-for-your-instance"></a>Activer l’analytique des journaux d’activité pour votre instance

Les journaux d’activité et les métriques de votre instance Azure Digital Twins sont affichés dans Azure Monitor. Cette documentation suppose que vous avez créé un [Azure Monitor enregistre](../azure-monitor/log-query/log-query-overview.md) espace de travail via le [Azure Portal](../azure-monitor/learn/quick-create-workspace.md), jusqu'à [Azure CLI](../azure-monitor/learn/quick-create-workspace-cli.md), ou via [ PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Vous pouvez rencontrer un délai de 5 minutes lors de l’envoi des événements dans les journaux d’Azure Monitor pour la première fois.

Pour configurer la supervision et la journalisation pour les ressources Azure Digital Twins, consultez [Guide pratique pour configurer la supervision et la journalisation](./how-to-configure-monitoring.md).

Pour savoir comment configurer les paramètres du journal de diagnostic dans Azure Digital Twins via le portail Azure, Azure CLI ou PowerShell, voir l’article [Collecter et utiliser des données de journaux à partir de vos ressources Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

>[!IMPORTANT]
> Veillez à sélectionner la totalité des catégories de journaux et des métriques, ainsi que votre espace de travail Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Tracer les données de télémétrie des capteurs

Pour suivre la télémétrie du capteur, vérifiez que les paramètres de diagnostic sont activés pour votre instance Azure Digital Twins. Ensuite, assurez-vous que toutes les catégories de journaux souhaitées sont sélectionnées. Enfin, vérifiez que les journaux de votre choix sont envoyés aux journaux d’Azure Monitor.

Pour faire correspondre un message de télémétrie de capteur à ses journaux d’activité respectifs, vous pouvez spécifier un ID de corrélation sur les données d’événement envoyées. Pour ce faire, définissez la propriété `x-ms-client-request-id` sur un GUID.

Après l’envoi des données de télémétrie, ouvrez analytique de journal pour rechercher des journaux à l’aide de l’ensemble des ID de corrélation :

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valeur de la requête | Remplacer par |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | ID de corrélation qui a été spécifié dans les données d’événement |

Si vous activez la journalisation pour votre fonction définie par l’utilisateur, ces journaux apparaissent dans votre instance d’analytique de journal avec la catégorie `UserDefinedFunction`. Pour les récupérer, entrez la condition de requête suivante dans l’analytique de journal :

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Pour plus d’informations sur les opérations de requêtes puissantes, consultez [Bien démarrer avec les requêtes](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identifier les problèmes courants

Le diagnostic et l’identification des problèmes courants sont importants pour résoudre les problèmes liés à votre solution. Plusieurs problèmes couramment rencontrés durant le développement de fonctions définies par l’utilisateur sont récapitulés dans les sous-sections suivantes.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Vérifier si une vérification a été créée

À défaut d’attribution de rôle créée dans l’API de gestion, la fonction définie par l’utilisateur ne dispose pas de l’accès nécessaire pour effectuer des actions telles que l’envoi de notifications, la récupération de métadonnées et la définition de valeurs calculées au sein de la topologie.

Vérifiez s’il existe une attribution de rôle pour votre fonction définie par l’utilisateur par le biais de votre API de gestion :

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valeur du paramètre | Remplacer par |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ID de la fonction définie par l’utilisateur pour laquelle récupérer les attributions de rôle|

Consultez cet article sur la [façon de créer une attribution de rôle pour votre fonction définie par l’utilisateur](./how-to-user-defined-functions.md) si aucune attribution de rôle n’existe.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Vérifier si le système de correspondance (« matcher ») fonctionne pour les données de télémétrie d’un capteur

En utilisant l’appel suivant de l’API de gestion de vos instances Azure Digital Twins, vous pouvez déterminer si un matcher donné s’applique pour le capteur donné.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | ID du matcher à évaluer |
| *YOUR_SENSOR_IDENTIFIER* | ID du capteur à évaluer |

Réponse :

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Vérifier ce qu’un capteur déclenche

En utilisant l’appel suivant de l’API de gestion de vos instances Azure Digital Twins, vous pouvez déterminer les identificateurs de vos fonctions définies par l’utilisateur qui sont déclenchées par les données de télémétrie entrantes du capteur donné :

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | ID du capteur pour l’envoi de la télémétrie |

Réponse :

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problème de réception des notifications

Si vous ne recevez aucune notification de la fonction définie par l’utilisateur déclenchée, vérifiez que votre paramètre de type d’objet de topologie correspond au type d’identificateur utilisé.

Exemple **incorrect** :

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Dans ce scénario, l’identificateur utilisé fait référence à un capteur alors que le type d’objet de topologie spécifié est `Space`.

Exemple **correct** :

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Le moyen le plus simple d’éviter ce problème consiste à utiliser la méthode `Notify` sur l’objet des métadonnées.

Exemple :

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Exceptions de diagnostics courantes

Si vous activez les paramètres de diagnostic, vous pouvez rencontrer ces exceptions courantes :

1. **Limitation** : si votre fonction définie par l’utilisateur dépasse les limites de taux d’exécution décrites dans l’article [Limites de service](./concepts-service-limits.md), elle fait l’objet d’une limitation. Aucune autre opération n’est exécutée correctement jusqu’à l’expiration de la limitation.

1. **Données introuvables** : si votre fonction définie par l’utilisateur tente d’accéder à des métadonnées inexistantes, l’opération échoue.

1. **Non autorisé** : si votre fonction définie par l’utilisateur n’a pas d’attribution de rôle définie ou ne dispose pas d’autorisations suffisantes pour accéder à certaines métadonnées à partir de la topologie, l’opération échoue.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment activer la [supervision et les journaux d’activité](../azure-monitor/platform/activity-logs-overview.md) dans Azure Digital Twins.
