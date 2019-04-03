---
title: Schéma d’alerte commun pour les alertes Azure monitor
description: Présentation du schéma d’alerte courantes, pourquoi vous devez utiliser et comment l’activer
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 8f8dcff0b72ea92e835c0702113a9cb6a7678e86
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851796"
---
# <a name="common-alert-schema"></a>Schéma commun de l’alerte

Cet article décrit ce qu’est le schéma d’alerte courantes, les avantages d’utilisation et comment l’activer.

## <a name="what-is-the-common-alert-schema"></a>Qu’est le schéma d’alerte commun ?

Le schéma d’alerte commun standardise l’expérience de consommation pour les notifications d’alerte dans Azure dès aujourd'hui. Historiquement, les trois types d’alerte dans Azure dès aujourd'hui (mesures, journaux et journal d’activité) ont leurs propres modèles de courrier électronique, les schémas de webhook, etc. Avec le schéma d’alerte courantes, vous pouvez maintenant recevoir des notifications d’alerte avec un schéma cohérent.

N’importe quelle instance d’alerte décrit **la ressource qui a été affectée** et **la cause de l’alerte**, et ces instances sont décrites dans le schéma commun dans les sections suivantes :
* **Essentials**: Un ensemble de **standardisé champs**, parmi tous les types d’alerte, qui décrivent **quelle ressource** l’alerte est définie sur avec common alerte des métadonnées supplémentaires (par exemple, gravité ou la description). 
* **Contexte de l’alerte**: Un ensemble de champs qui décrivent le **cause de l’alerte**, avec des champs qui varient **selon le type d’alerte**. Par exemple, une alerte de métrique aurait des champs tels que le nom de la mesure et la valeur métrique dans le contexte de l’alerte, tandis qu’une alerte de journal d’activité aurait plus d’informations sur l’événement qui a généré l’alerte. 

Les scénarios d’intégration standard que nous entendons des clients impliquent le routage de l’instance d’alerte à l’équipe concernée en fonction de certains pivot (par exemple, le groupe de ressources), après laquelle l’équipe responsable commence à travailler dessus. Avec le schéma d’alerte courantes, peut avoir normalisé logique de routage entre les types d’alerte en exploitant les champs essentiels, en laissant les champs de contexte comme pour les équipes concernées approfondir vos recherches.

Cela signifie que vous pouvez avoir potentiellement moins d’intégrations, rendant le processus de gestion et de leur mise à jour un _beaucoup_ tâche plus simple. En outre, les enrichissements de charge utile et alerte futures (par exemple, personnalisation, diagnostic enrichissement, etc.) signalent uniquement inscrire dans le schéma commun.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Quelles sont les améliorations qu’apporte le schéma d’alerte commun ?

Le schéma commun de l’alerte sera principalement se manifester dans vos notifications d’alerte. Les améliorations que vous observerez sont répertoriées ci-dessous :

| Action | Améliorations|
|:---|:---|
| sms | Un modèle SMS cohérent pour tous les types d’alerte. |
| Email | Un modèle cohérent et détaillées un courrier électronique, ce qui vous permet de facilement diagnostiquer les problèmes en un clin de œil. Embedded profond des liens vers l’instance d’alerte sur le portail et de la ressource concernée vous assurer que vous pouvez rapidement accéder dans le processus de mise à jour. |
| Webhook/logique application/Azure Function | Une JSON structure cohérente pour tous les types d’alertes, ce qui vous permet de créer facilement des intégrations entre les différents types d’alerte. |

Le nouveau schéma permet également une expérience plus riche de la consommation d’alerte sur le portail Azure et l’application mobile Azure dans un futur proche. 

[En savoir plus sur les définitions de schéma pour la logique de Webhooks/applications/Azure Functions.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Les actions suivantes ne prennent pas en charge du schéma commun de l’alerte : Connecteur ITSM, Runbook Automation.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Comment activer le schéma d’alerte commun ?

Vous pouvez accepter ou refuser pour le schéma d’alerte commun par le biais des groupes d’actions sur les deux le portail et via l’API REST. Le bouton bascule pour basculer vers le nouveau schéma existe au niveau de l’action. Par exemple, vous devez accepter séparément pour une action de messagerie et une action de webhook.

> [!NOTE]
> 1. Types d’alerte suivants prennent en charge le schéma commun par défaut (aucun opt dans requis) :
>     * Alertes d’échec d’anomalie
> 1. Actuellement, les types d’alerte suivants ne prennent pas en charge le schéma commun :
>     * Alertes d’intégrité du service
>     * Journal d’activité - alertes de sécurité
>     * Alertes générées par [Azure Monitor pour les machines virtuelles](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)

### <a name="through-the-azure-portal"></a>Via le portail Azure

![Schéma d’alerte commun participer](media/alerts-common-schema/portal-opt-in.png)

1. Ouvrez n’importe quel existant ou une nouvelle action dans un groupe d’actions. 
1. Sélectionnez « Oui » pour le bouton bascule Activer le schéma d’alerte courantes, comme indiqué.

### <a name="through-the-action-groups-rest-api"></a>Via l’API REST groupes d’actions

Vous pouvez également utiliser le [API groupes d’Action](https://docs.microsoft.com/rest/api/monitor/actiongroups) pour participer le schéma commun de l’alerte. Tout en rendant le [créer ou mettre à jour](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) appel d’API REST, vous pouvez définir l’indicateur « useCommonAlertSchema » sur « true » (pour inclure la) ou « false » (participer au) pour une des actions suivantes : courrier électronique/webhook/logique application/Azure (fonction).

Par exemple, la requête suivante corps apporté à la [créer ou mettre à jour](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) API REST effectuerez les opérations suivantes :

* Activer le schéma d’alerte commun pour l’action de messagerie « E-mail de John Doe »
* Désactiver le schéma d’alerte commun pour l’action de messagerie « email Jane Smith »
* Activer le schéma d’alerte commun pour l’action de webhook « Exemple webhook »

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Étapes suivantes

- [Définitions de schéma alerte communes pour la logique de Webhooks/applications/Azure Functions.](https://aka.ms/commonAlertSchemaDefinitions)



