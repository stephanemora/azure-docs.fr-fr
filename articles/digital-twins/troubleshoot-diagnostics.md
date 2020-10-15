---
title: Configurer les diagnostics
titleSuffix: Azure Digital Twins
description: Découvrez commet activer la journalisation avec les paramètres de diagnostic.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: f4abf78c153bd3d61068e4b7607794d6ccf1ed04
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047673"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Dépannage d’Azure Digital Twins : Journalisation des diagnostics

Azure Digital Twins collecte des [métriques](troubleshoot-metrics.md) pour votre instance de service qui fournissent des informations sur l’état de vos ressources. Vous pouvez utiliser ces métriques pour évaluer l’intégrité globale du service Azure Digital Twins et des ressources qui y sont connectées. Ces statistiques accessibles à l’utilisateur vous permettent d’effectuer le suivi de votre Azure Digital Twins et d’analyser les causes des problèmes sans avoir à contacter le support Azure.

Cet article explique comment activer la **journalisation des diagnostics** pour vos données de métriques à partir de votre instance Azure Digital Twins. Vous pouvez utiliser ces journaux pour résoudre les problèmes liés à ce service et pour configurer les paramètres de diagnostic afin d’envoyer des métriques Azure Digital Twins à différentes destinations. Pour en savoir plus sur ces paramètres, consultez [*Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Activer les paramètres de diagnostic avec le portail Azure

Voici comment activer les paramètres de diagnostic pour votre instance Azure Digital Twins :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre instance Azure Digital Twins. Vous pouvez la trouver en tapant son nom dans la barre de recherche du portail. 

2. Sélectionnez **Paramètres de diagnostic** dans le menu, puis **Ajouter un paramètre de diagnostic**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Capture d’écran montrant la page des paramètres de diagnostic et le bouton à ajouter":::

3. Dans la page qui suit, renseignez les valeurs suivantes :
     * **Nom du paramètre de diagnostic** : Nommez les paramètres de diagnostic.
     * **Détails de la catégorie** : choisissez les opérations à surveiller, puis cochez les cases pour activer les diagnostics pour ces opérations. Les opérations sur lesquelles les paramètres de diagnostic peuvent établir des rapports sont :
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Pour plus d’informations sur ces options, consultez la section [*Détails de la catégorie*](#category-details) ci-dessous.
     * **Détails de la destination** : Indiquez où vous voulez envoyer les journaux d’activité. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :
        - Envoyer à Log Analytics
        - Archiver dans un compte de stockage
        - Diffuser vers un hub d’événements

        Il se peut que vous soyez invité à fournir des détails supplémentaires s’ils sont nécessaires pour la sélection de votre destination.  
    
4. Enregistrez les nouveaux paramètres. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Capture d’écran montrant la page des paramètres de diagnostic et le bouton à ajouter":::

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux réapparaissent dans la cible configurée sur la page **Paramètres de diagnostic** de votre instance. 

## <a name="category-details"></a>Détails de la catégorie

Voici des détails supplémentaires sur les catégories de journaux qui peuvent être sélectionnées sous **Détails de la catégorie** lors de la configuration des paramètres de diagnostic.

| Catégorie de journal | Description |
| --- | --- |
| ADTModelsOperation | Consigner tous les appels d’API se rapportant à des modèles |
| ADTQueryOperation | Consigner tous les appels d’API se rapportant à des requêtes |
| ADTEventRoutesOperation | Consigner tous les appels d’API se rapportant aux itinéraires d’événements, ainsi que la sortie d’événements provenant d’Azure Digital Twins vers un service de point de terminaison comme Event Grid, Event Hubs et Service Bus |
| ADTDigitalTwinsOperation | Consigner tous les appels d’API se rapportant à Azure Digital Twins |

Chaque catégorie de journal se compose d'opérations d'écriture, de lecture, de suppression et d'action.  Ces opérations sont mappées à des appels d'API REST, comme suit :

| Type d'événement | Opérations de l'API REST |
| --- | --- |
| Write | PUT et PATCH |
| Lire | GET |
| DELETE | Suppression |
| Action | POST |

Voici une liste complète des opérations et des [appels d’API REST Azure Digital Twins](/rest/api/azure-digitaltwins/) correspondants consignés dans chaque catégorie. 

>[!NOTE]
> Chaque catégorie de journal contient plusieurs opérations/appels d’API REST. Dans le tableau ci-dessous, chaque catégorie de journal est mappée à l’ensemble des opérations/appels d’API REST en dessous, jusqu’à ce que la catégorie suivante du journal soit affichée. 

| Catégorie de journal | Opération | Appels d’API REST et autres événements |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | API de mise à jour des modèles de jumeaux numériques |
|  | Microsoft.DigitalTwins/models/read | API de modèles de jumeaux numériques Get by ID et Liste |
|  | Microsoft.DigitalTwins/models/delete | API de suppression des modèles de jumeaux numériques |
|  | Microsoft.DigitalTwins/models/action | API d’ajout des modèles de jumeaux numériques |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | API de jumeaux numériques de requête |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | API d’ajout de routages d’événements |
|  | Microsoft.DigitalTwins/eventroutes/read | API de routages d'événements Get by ID et Liste |
|  | Microsoft.DigitalTwins/eventroutes/delete | API de suppression de routages d’événements |
|  | Microsoft.DigitalTwins/eventroutes/action | Échec lors de la tentative de publication des événements sur un service de point de terminaison (pas un appel d’API) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Ajouter des jumeaux numériques, Ajouter une relation, Mettre à jour, Mettre à jour un composant |
|  | Microsoft.DigitalTwins/digitaltwins/read | Jumeaux numériques Get by ID, Obtenir un composant, Obtenir une relation par ID, Relations entrantes, Lister les relations |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Supprimer des jumeaux numériques, Supprimer une relation |
|  | Microsoft.DigitalTwins/digitaltwins/action | Envoyer des données de télémétrie de composant de jumeaux numériques, Envoyer des données de télémétrie |

## <a name="log-schemas"></a>Schémas des journaux 

Chaque catégorie de journal dispose d'un schéma qui définit la façon dont les événements de cette catégorie sont signalés. Chaque entrée de journal est stockée sous forme de texte et formatée en tant qu'objet blob JSON. Les champs du journal et des exemples de corps JSON sont fournis pour chaque type de journal ci-dessous. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` et `ADTQueryOperation` utilisent un schéma de journal d'API cohérent ; `ADTEventRoutesOperation` dispose d'un schéma distinct.

### <a name="api-log-schemas"></a>Schéma des journaux d'API

Ce schéma de journal est cohérent pour `ADTDigitalTwinsOperation`, `ADTModelsOperation` et `ADTQueryOperation`. Il contient des informations relatives aux appels d'API à une instance d'Azure Digital Twins.

Vous trouverez ci-dessous les descriptions des champs et des propriétés des journaux d'API.

| Nom du champ | Type de données | Description |
|-----|------|-------------|
| `Time` | DateTime | Date et heure (UTC) auxquelles l'événement s'est produit |
| `ResourceID` | String | ID Azure Resource Manager de la ressource sur laquelle l'événement s'est produit |
| `OperationName` | String  | Type d'action réalisée pendant l'événement |
| `OperationVersion` | String | Version de l'API utilisée pendant l'événement |
| `Category` | String | Type de ressource émise |
| `ResultType` | String | Résultat de l'événement |
| `ResultSignature` | String | Code d'état HTTP de l'événement |
| `ResultDescription` | String | Détails supplémentaires sur l'événement |
| `DurationMs` | String | Temps nécessaire pour exécuter l'événement, en millisecondes |
| `CallerIpAddress` | String | Adresse IP source masquée de l'événement |
| `CorrelationId` | Guid | Le client a fourni un identificateur unique pour l'événement |
| `Level` | String | Gravité de la journalisation de l'événement |
| `Location` | String | Région où s'est produit l'événement |
| `RequestUri` | Uri | Point de terminaison utilisé pendant l'événement |

Vous trouverez ci-dessous des exemples de corps JSON correspondant à ces types de journaux.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>Schéma des journaux de sortie

Il s'agit du schéma des journaux `ADTEventRoutesOperation`. Ceux-ci contiennent des détails sur les exceptions et les opérations d'API relatives aux points de terminaison de sortie connectés à une instance d'Azure Digital Twins.

|Nom du champ | Type de données | Description |
|-----|------|-------------|
| `Time` | DateTime | Date et heure (UTC) auxquelles l'événement s'est produit |
| `ResourceId` | String | ID Azure Resource Manager de la ressource sur laquelle l'événement s'est produit |
| `OperationName` | String  | Type d'action réalisée pendant l'événement |
| `Category` | String | Type de ressource émise |
| `ResultDescription` | String | Détails supplémentaires sur l'événement |
| `Level` | String | Gravité de la journalisation de l'événement |
| `Location` | String | Région où s'est produit l'événement |
| `EndpointName` | String | Nom du point de terminaison de sortie créé dans Azure Digital Twins |

Vous trouverez ci-dessous des exemples de corps JSON correspondant à ces types de journaux.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la configuration des diagnostics, consultez [*Collecter et utiliser des données de journaux à partir de vos ressources Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Pour plus d’informations sur les métriques d’Azure Digital Twins, consultez [*Dépannage : Afficher les métriques avec Azure Monitor*](troubleshoot-metrics.md).
* Pour savoir comment activer les alertes pour vos métriques, consultez [*Résolution des problèmes : Configurer des alertes*](troubleshoot-alerts.md).