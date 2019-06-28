---
title: Surveiller les opérations, les événements et les compteurs pour une instance publique de Basic Load Balancer
titlesuffix: Azure Load Balancer
description: Découvrez comment activer les événements d’alerte, et sonder la journalisation de l’état d’intégrité pour une instance publique de Basic Load Balancer.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: 0d7c792c5230a5d82e97f4598a5dcfb864cead74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861177"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Journaux Azure Monitor pour une instance publique de Basic Load Balancer

>[!IMPORTANT] 
>Azure Load Balancer prend en charge deux types différents : De base et Standard. Cet article décrit Basic Load Balancer. Pour plus d’informations sur Standard Load Balancer, consultez [Présentation de Standard Load Balancer](load-balancer-standard-overview.md), qui expose les données de télémétrie par le biais de plusieurs métriques multidimensionnelles dans Azure Monitor.

Vous pouvez utiliser différents types de journaux d’activité dans Azure pour gérer les instances de Basic Load Balancer et résoudre les problèmes associés. Certains de ces journaux d’activité sont accessibles via le portail. Tous les journaux d’activité peuvent être extraits à partir d’un stockage Blob Azure et affichés dans différents outils, comme Excel et PowerBI. Pour en savoir plus sur les différents types de journaux d’activité, consultez la liste ci-dessous.

* **Journaux d’audit** : Vous pouvez utiliser les [journaux d’activité d’audit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement journaux d’activité des opérations) pour afficher toutes les opérations soumises à votre/vos abonnement(s) Azure, ainsi que leur état. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure.
* **Journaux d’activité d’événements d’alerte** : Vous pouvez utiliser ces journaux pour afficher les alertes générées par l’équilibreur de charge. L'état de l'équilibreur de charge est collecté toutes les cinq minutes. Ce journal est écrit uniquement si un événement d'alerte d’équilibreur de charge est généré.
* **Journaux d’activité de sonde d’intégrité** : Vous pouvez utiliser ces journaux pour connaître les problèmes détectés par votre sonde d’intégrité, tels que le nombre d’instances du pool principal qui ne reçoivent pas les demandes de l’équilibreur de charge en raison d’échecs de sonde d’intégrité. Toute modification de l’état de la sonde d’intégrité est indiquée dans ce journal.

> [!IMPORTANT]
> Les journaux Azure Monitor peuvent uniquement être utilisés pour les instances publiques de Basic Load Balancer. Les journaux d’activité ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour plus d’informations sur les modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Activation de la journalisation

La journalisation d’audit est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation des événements et des sondes d’intégrité pour commencer à collecter les données disponibles dans ces journaux d’activité. Utilisez les étapes suivantes pour activer la journalisation.

Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne disposez pas déjà d'un équilibreur de charge, [créez un équilibreur de charge](load-balancer-get-started-internet-arm-ps.md) avant de continuer.

1. Dans le portail, cliquez sur **Parcourir**.
2. Sélectionnez **Équilibreurs de charge**.

    ![portail - équilibreur-charge](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Sélectionnez un équilibreur de charge existant >> **Tous les paramètres**.
4. Sur le côté droit de la boîte de dialogue, sous le nom de l’équilibrage de charge, accédez à **Surveillance**, puis cliquez sur **Diagnostics**.

    ![portail - paramètres-équilibreur-charge](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Dans le volet **Diagnostics**, sous **État**, sélectionnez **Activé**.
6. Cliquez sur **Compte de stockage**.
7. Sous **JOURNAUX D’ACTIVITÉ**, sélectionnez un compte de stockage existant ou créez-en un nouveau. Utilisez le curseur pour déterminer la durée en jours pendant laquelle les données d’événement sont stockées dans les journaux d’événements. 
8. Cliquez sur **Enregistrer**.

Les diagnostics sont enregistrés dans le Stockage Table du compte de stockage spécifié. Si les journaux d’activité ne sont pas enregistrés, c’est qu’aucun journal approprié n’a été généré.

![Portail - Journaux de diagnostics](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Les journaux d’audit ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation des événements et des sondes d’intégrité occasionnera des frais de service.

## <a name="audit-log"></a>Journal d’audit

Le journal d’audit est généré par défaut. Les journaux d’activité sont conservés pendant 90 jours dans la banque de Journaux d’activité d’événements d’Azure. Pour en savoir plus sur ces journaux d’activité, lisez l’article [Affichage des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Journal des événements d'alerte

Ce journal n’est généré que si vous l’avez activé au niveau de chaque équilibreur de charge. Les événements sont journalisés au format JSON et stockés dans le compte de stockage spécifié lors de l’activation de la journalisation. Vous trouverez ci-dessous un exemple d’événement.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

La sortie JSON affiche la propriété *eventname* qui décrit la raison pour laquelle l'équilibreur de charge a créé une alerte. Dans ce cas, l'alerte a été générée en raison de l'épuisement du port TCP à cause des limites de l’IP NAT source (SNAT).

## <a name="health-probe-log"></a>Journal des sondes d’intégrité

Ce journal n’est généré que si vous l’avez activé au niveau de chaque équilibreur de charge, comme détaillé ci-dessous. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Un conteneur nommé « insights-logs-loadbalancerprobehealthstatus » est créé et les données suivantes sont enregistrées :

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

La sortie JSON apparaît dans le champ des propriétés des informations de base pour l'état des sondes d’intégrité. La propriété *dipDownCount* indique le nombre total d'instances sur le serveur principal qui ne reçoivent pas le trafic réseau en raison d’une absence de réponse de la part de la sonde.

## <a name="view-and-analyze-the-audit-log"></a>Afficher et analyser le journal d’audit

Vous pouvez afficher et analyser les données du journal d’audit en utilisant l’une des méthodes suivantes :

* **Outils Azure** : Récupérez les informations des journaux d’audit via Azure PowerShell, l’interface de ligne de commande (CLI) Azure, l’API REST Azure ou le portail Azure en préversion. Des instructions détaillées pour chaque méthode sont détaillées dans l’article [Opérations d’audit avec Resource Manager](../azure-resource-manager/resource-group-audit.md) .
* **Power BI** : Si vous ne disposez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing), vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité d’audit Azure pour Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), vous pouvez analyser vos données avec des tableaux de bord préconfigurés ou personnaliser les vues selon vos besoins.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Afficher et analyser les journaux des sondes d’intégrité et des événements

Vous devez vous connecter à votre compte de stockage et récupérer les entrées de journal d’activité JSON pour les journaux des événements et des sondes d’intégrité. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous connaissez bien Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Visualiser vos journaux d’activité d’audit Azure avec Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Afficher et analyser les journaux d’activité d’audit Azure dans Power BI et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) .

## <a name="next-steps"></a>Étapes suivantes

[Comprendre les sondes de l’équilibrage de charge](load-balancer-custom-probe-overview.md)
