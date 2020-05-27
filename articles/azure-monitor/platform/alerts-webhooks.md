---
title: Appeler un webhook avec une alerte de métrique classique dans Azure Monitor
description: Découvrez comment rediriger des alertes de métrique Azure vers d’autres systèmes que Azure.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 0677c7a0521fe1f63c9c2c9fce65d8dbd8e6d5c4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826908"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Appeler un webhook avec une alerte de métrique classique dans Azure Monitor

Les Webhooks permettent de rediriger une notification d’alerte Azure vers d’autres systèmes pour effectuer un post-traitement ou des actions personnalisées. Vous pouvez utiliser un Webhook sur une alerte pour rediriger cette dernière vers des services qui envoient des SMS, consignent des bogues, avertissent une équipe dans des services de conversation instantanée/messagerie ou effectuent d’autres actions. 

Cet article décrit comment définir un Webhook sur une alerte de métrique Azure. Il montre également à quoi ressemble la charge utile d’une requête HTTP POST sur un Webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte de journal d’activité Azure (alertes sur les événements), consultez [Appeler un Webhook sur une alerte de journal d’activité Azure](alerts-log-webhook.md).

Les alertes Azure utilisent une requête HTTP POST pour envoyer le contenu de l’alerte au format JSON vers un URI de Webhook que vous fournissez lors de la création de l’alerte. Le schéma est défini plus loin dans cet article. L’URI doit être un point de terminaison HTTP ou HTTPS valide. Azure publie une entrée par demande lorsqu’une alerte est activée.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configurer des Webhooks via le portail Azure
Pour ajouter ou mettre à jour l’URI du Webhook, dans le [portail Azure](https://portal.azure.com/), accédez à **Créer/mettre à jour des alertes**.

![Volet Ajouter une règle d’alerte](./media/alerts-webhooks/Alertwebhook.png)

Vous pouvez également configurer une alerte à publier vers un URI de Webhook à l’aide des [applets de commande Azure PowerShell](../samples/powershell-samples.md#create-metric-alerts), de [l’interface de ligne de commande multiplateforme](../samples/cli-samples.md#work-with-alerts) ou des [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Authentifier le Webhook
Le Webhook peut s’authentifier à l’aide de l’autorisation basée sur un jeton. L’URI du Webhook est enregistré avec un ID de jeton. Par exemple : `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schéma de la charge utile
L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes basées sur des métriques :

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Champ | Obligatoire | Ensemble fixe de valeurs | Notes |
|:--- |:--- |:--- |:--- |
| status |O |Activated, Resolved |État de l’alerte en fonction des conditions que vous avez définies. |
| contexte |O | |Contexte de l’alerte. |
| timestamp |O | |Heure à laquelle l’alerte a été déclenchée. |
| id |O | |Chaque règle d’alerte possède un ID unique. |
| name |O | |Nom de l’alerte. |
| description |O | |Description de l’alerte. |
| conditionType |O |Metric, Event |Deux types d’alertes sont pris en charge : métrique et événement. Les alertes de métrique sont basées sur une condition de métrique. Les alertes d’événement sont basées sur un événement dans le journal d’activité. Utilisez cette valeur pour vérifier si l’alerte est basée sur une métrique ou sur un événement. |
| condition |O | |Champs à vérifier en fonction de la valeur **conditionType**. |
| metricName |Pour les alertes de métrique | |Nom de la métrique qui définit ce que la règle surveille |
| metricUnit |Pour les alertes de métrique |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |Unité autorisée dans la métrique Consultez les [valeurs autorisées](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Pour les alertes de métrique | |Valeur réelle de la métrique ayant entraîné l’alerte. |
| threshold |Pour les alertes de métrique | |Valeur de seuil en fonction de laquelle l’alerte est activée. |
| windowSize |Pour les alertes de métrique | |Période de temps qui est utilisée pour surveiller l’activité d’alerte en fonction du seuil. La valeur doit être comprise entre 5 minutes et 1 jour. La valeur doit être au format de durée ISO 8601. |
| timeAggregation |Pour les alertes de métrique |Average, Last, Maximum, Minimum, None, Total |Détermine la façon dont les données collectées doivent être combinées au fil du temps. La valeur par défaut est Average. Consultez les [valeurs autorisées](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Pour les alertes de métrique | |Opérateur utilisé pour la comparaison des données de métrique actuelles au seuil défini. |
| subscriptionId |O | |L’ID d’abonnement Azure. |
| resourceGroupName |O | |Nom du groupe de ressources de la ressource affectée. |
| resourceName |O | |ID de la ressource affectée. |
| resourceType |O | |Type de la ressource affectée. |
| resourceId |O | |ID de la ressource affectée. |
| resourceRegion |O | |Région ou emplacement de la ressource affectée. |
| portalLink |O | |Lien direct vers la page de résumé de la ressource sur le portail. |
| properties |N |Facultatif |Ensemble de paires clé/valeur contenant les détails de l’événement. Par exemple : `Dictionary<String, String>`. Le champ properties est facultatif. Dans un workflow basé sur une application logique ou une interface utilisateur personnalisée, les utilisateurs peuvent entrer des paires clé/valeur transmissibles par le biais de la charge utile. Une autre manière de transmettre des propriétés personnalisées au Webhook consiste à utiliser l’URI du Webhook (sous la forme de paramètres de requête). |

> [!NOTE]
> Vous pouvez définir le champ **properties** uniquement à l’aide des [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les Webhooks et les alertes Azure par le biais de la vidéo décrivant [l’intégration d’alertes Azure à PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Découvrez comment [exécuter des scripts Azure Automation (runbooks) sur des alertes Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Découvrez comment [utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Découvrez comment [utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Découvrez comment [utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

