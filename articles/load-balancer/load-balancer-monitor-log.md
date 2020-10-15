---
title: Surveiller les opérations, les événements et les compteurs pour une instance publique de Basic Load Balancer
titleSuffix: Azure Load Balancer
description: Découvrez comment activer les événements d’alerte, et sonder la journalisation de l’état d’intégrité pour une instance publique de Basic Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 42ec5a661bd7b42ba5de5bfa99b3898291cc60fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935600"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Journaux Azure Monitor pour une instance publique de Basic Load Balancer

Vous pouvez utiliser différents types de journaux d’activité dans Azure pour gérer les instances de Basic Load Balancer et résoudre les problèmes associés. Certains de ces journaux d’activité sont accessibles via le portail. Les journaux peuvent être envoyés en streaming à un hub d’événements ou à un espace de travail Log Analytics. Tous les journaux peuvent être extraits à partir du Stockage Blob Azure et affichés dans différents outils, comme Excel et Power BI.  Pour en savoir plus sur les différents types de journaux d’activité, consultez la liste ci-dessous.

* **Journaux d’activité :** Vous pouvez utiliser l’article [Afficher les journaux d’activité pour superviser les actions sur les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) afin d’afficher toutes les activités soumises à vos abonnements Azure et leur état. Les journaux d’activité sont activés par défaut et peuvent être affichés dans le portail Azure.
* **Journaux d’activité d’événements d’alerte** : Vous pouvez utiliser ces journaux pour afficher les alertes générées par l’équilibreur de charge. L'état de l'équilibreur de charge est collecté toutes les cinq minutes. Ce journal est écrit uniquement si un événement d'alerte d’équilibreur de charge est généré.
* **Journaux d’activité de sonde d’intégrité** : Vous pouvez utiliser ces journaux pour connaître les problèmes détectés par votre sonde d’intégrité, tels que le nombre d’instances du pool principal qui ne reçoivent pas les demandes de l’équilibreur de charge en raison d’échecs de sonde d’intégrité. Toute modification de l’état de la sonde d’intégrité est indiquée dans ce journal.

> [!IMPORTANT]
> **Les journaux d’événements de sonde d’intégrité ne sont pas actuellement fonctionnels et sont listés parmi les [problèmes connus avec Azure Load Balancer](whats-new.md#known-issues).** Les journaux d’activité ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour plus d’informations sur les modèles de déploiement, consultez [Présentation du déploiement Resource Manager et du déploiement classique](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Activation de la journalisation

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Activez la journalisation des événements et des sondes d’intégrité pour commencer à collecter les données disponibles dans ces journaux. Utilisez les étapes suivantes pour activer la journalisation.

Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne disposez pas déjà d'un équilibreur de charge, [créez un équilibreur de charge](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) avant de continuer.

1. Dans le portail, cliquez sur **Groupes de ressources**.
2. Sélectionnez **\<resource-group-name>** l’emplacement de votre équilibreur de charge.
3. Sélectionnez votre équilibreur de charge.
4. Sélectionnez **Journal d’activité** > **Paramètres de diagnostic**.
5. Dans le volet **Paramètres de diagnostic**, sous **Paramètres de diagnostic**, sélectionnez **+ Ajouter un paramètre de diagnostic**.
6. Dans le volet de création de **Paramètres de diagnostic**, entrez **myLBDiagnostic** dans le champ **Nom**.
7. Trois options s’offrent à vous pour les **Paramètres de diagnostic**.  Vous pouvez en choisir une, deux ou les trois, et configurer chacune d’elles selon vos besoins :
   * **Archiver dans un compte de stockage**
   * **Diffuser vers un hub d’événements**
   * **Envoyer à Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archiver dans un compte de stockage
    Vous avez besoin d’un compte de stockage déjà créé pour ce processus.  Pour créer un compte de stockage, consultez [Créez un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Cochez la case en regard d’**Archiver dans un compte de stockage**.
    2. Sélectionnez **Configurer** pour ouvrir le volet **Sélectionnez un compte de stockage**.
    3. Dans la zone déroulante, sélectionnez l’**Abonnement** dans lequel votre compte de stockage a été créé.
    4. Sélectionnez le nom de votre compte de stockage sous **Compte de stockage** dans la zone déroulante.
    5. Sélectionnez OK.

    ### <a name="stream-to-an-event-hub"></a>Diffuser vers un hub d’événements
    Vous avez besoin d’un hub d’événements déjà créé pour ce processus.  Pour créer un hub d’événements, consultez [Démarrage rapide : Créer un hub d’événements avec le portail Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Cochez la case en regard de **Diffuser vers un hub d’événements**.
    2. Sélectionnez **Configurer** pour ouvrir le volet **Sélectionner un hub d’événements**.
    3. Dans la zone déroulante, sélectionnez l’**Abonnement** dans lequel votre hub d’événements a été créé.
    4. **Sélectionnez un espace de noms de hub d’événements** dans la zone déroulante.
    5. **Sélectionnez un nom de stratégie de hub d’événements** dans la zone déroulante.
    6. Sélectionnez OK.

    ### <a name="send-to-log-analytics"></a>Envoyer à Log Analytics
    Vous devez avoir un espace de travail Log Analytics déjà créé et configuré pour ce processus.  Pour créer un espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

    1. Cochez la case en regard de l’option **Envoyer à Log Analytics**.
    2. Dans la zone déroulante, sélectionnez l’**Abonnement** dans lequel se trouve votre espace de travail Log Analytics.
    3. Sélectionnez l’**Espace de travail Log Analytics** dans la zone déroulante.


8. Sous la section **JOURNAL** du volet **Paramètres de diagnostic**, cochez la case en regard des deux options :
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Sous la section **JOURNAL** du volet **MÉTRIQUE**, cochez la case en regard de :
   * **AllMetrics**

11. Vérifiez que tout semble correct, puis cliquez sur **Enregistrer** en haut du volet de création de **Paramètres de diagnostic**.

## <a name="activity-log"></a>Journal d’activité

Le journal d’activité est généré par défaut. Les journaux d’activité sont conservés pendant 90 jours dans la banque de Journaux d’activité d’événements d’Azure. Pour en savoir plus sur ces journaux, lisez l’article [Afficher les journaux d’activité pour superviser les actions sur les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

## <a name="archive-to-storage-account-logs"></a>Archiver dans des journaux de compte de stockage

### <a name="alert-event-log"></a>Journal des événements d'alerte

Ce journal n’est généré que si vous l’avez activé au niveau de chaque équilibreur de charge. Les événements sont journalisés au format JSON et stockés dans le compte de stockage spécifié lors de l’activation de la journalisation. L’exemple suivant est celui d’un événement.

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

La sortie JSON affiche la propriété *eventname*, qui décrit la raison pour laquelle l’équilibreur de charge a créé une alerte. Dans le cas présent, l’alerte a été générée en raison de l’épuisement du port TCP à cause des limites de traduction NAT d’adresses IP sources (SNAT).

### <a name="health-probe-log"></a>Journal des sondes d’intégrité

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

La sortie JSON apparaît dans le champ des propriétés des informations de base pour l'état des sondes d’intégrité. La propriété *dipDownCount* indique le nombre total d’instances sur le back-end qui ne reçoivent pas le trafic réseau en raison d’une absence de réponse de la part de la sonde.

### <a name="view-and-analyze-the-activity-log"></a>Afficher et analyser le journal d’activité

Vous pouvez afficher et analyser les données du journal d’activité en utilisant l’une des méthodes suivantes :

* **Outils Azure** : Récupérez les informations du journal d'activité par le biais d'Azure PowerShell, de l'interface de ligne de commande (CLI) Azure, de l'API REST Azure ou du portail Azure. Des instructions détaillées pour chaque méthode sont détaillées dans l’article [Opérations d’audit avec Resource Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI :** si vous n’avez pas encore de compte [Power BI](https:// .microsoft.com/pricing), vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité d’audit Azure pour Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs), vous pouvez analyser vos données avec des tableaux de bord préconfigurés ou personnaliser les vues selon vos besoins.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Afficher et analyser les journaux des sondes d’intégrité et des événements

Connectez-vous à votre compte de stockage et récupérez les entrées de journal JSON pour les journaux des événements et des sondes d’intégrité. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, Power BI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.

## <a name="stream-to-an-event-hub"></a>Diffuser vers un hub d’événements
Quand les informations de diagnostic sont envoyées en streaming vers un hub d’événements, elles peuvent être utilisées pour une analyse centralisée des journaux dans un outil SIEM tiers avec intégration à Azure Monitor. Pour plus d’informations, consultez [Envoyer en streaming des données de supervision Azure vers un hub d’événements](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Envoyer à Log Analytics
Les informations de diagnostic des ressources dans Azure peuvent être envoyées directement à un espace de travail Log Analytics où des requêtes complexes peuvent être exécutées sur les informations en vue d’un dépannage et d’une analyse.  Pour plus d’informations, consultez [Collecter les journaux de ressources Azure dans l’espace de travail Log Analytics dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace).

## <a name="next-steps"></a>Étapes suivantes

[Comprendre les sondes de l’équilibrage de charge](load-balancer-custom-probe-overview.md)
