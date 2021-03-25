---
title: Rapports et journaux de flux Azure DDoS Protection Standard
description: Découvrez comment configurer des rapports et des journaux de flux.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: cc5b3b85d6d13fda532da0993fa7f733126b8eae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591878"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Afficher et configurer la journalisation des diagnostics DDoS

La norme Azure DDoS Protection fournit une visualisation et des insights détaillés sur les attaques avec l’analyse des attaques DDoS. Les clients qui protègent leurs réseaux virtuels contre les attaques DDoS ont une visibilité détaillée du trafic des attaques et des mesures prises pour leur prévention par le biais de rapports de prévention des attaques et de journaux de flux de prévention des attaques. La télémétrie enrichie est exposée via Azure Monitor et inclut des métriques détaillées pendant la durée d’une attaque DDoS. La génération d’alertes peut être configurée pour l’une des métriques Azure Monitor exposées par DDoS Protection. La journalisation peut être également intégrée à [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics et Stockage Azure pour l’analyse avancée par le biais de l’interface de diagnostic d’Azure Monitor.

Les journaux de diagnostic suivants sont disponibles pour Azure DDoS Protection Standard : 

- **DDoSProtectionNotifications** : Les notifications vous informent chaque fois qu’une ressource IP publique est attaquée, et lorsque l’atténuation des attaques est terminée.
- **DDoSMitigationFlowLogs** : Les journaux de flux de prévention des attaques vous permettent de passer en revue le trafic abandonné, le trafic transféré et d’autres points de données intéressants pendant une attaque DDoS active en temps quasi réel. Vous pouvez ingérer le flux constant de ces données dans Azure Sentinel ou des systèmes SIEM tiers par le biais d’un hub d’événements pour la supervision en temps quasi réel, prendre des actions éventuelles et répondre aux besoins de vos opérations de défense.
- **DDoSMitigationReports** : La fonctionnalité Rapports de prévention des attaques utilise les données de protocole Netflow qui sont ensuite regroupées pour fournir des informations détaillées concernant l’attaque de votre ressource. Chaque fois qu’une ressource IP publique est attaquée, la génération de rapport démarre en même temps que la prévention. Un rapport incrémentiel est généré toutes les 5 minutes et un rapport post-prévention concernant toute la période de prévention. Cela permet de garantir que dans le cas où l’attaque DDoS se poursuit pendant une durée plus longue, vous pourrez afficher l’instantané le plus récent du rapport de prévention toutes les 5 minutes et un récapitulatif complet une fois la prévention de l’attaque terminée. 
- **AllMetrics** : Fournit toutes les métriques possibles disponibles pendant la durée d’une attaque DDoS. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurez les journaux de diagnostic DDoS, y compris les notifications, les rapports d’atténuation et les journaux de flux d’atténuation. 
> * Activez la journalisation des diagnostics sur toutes les adresses IP publiques dans une étendue définie.
> * Affichez les données de journal dans des classeurs.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Avant de pouvoir effectuer les étapes de ce didacticiel, vous devez d’abord créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md), et la protection DDoS Standard doit être activée sur un réseau virtuel.
- Le service DDoS surveille les adresses IP publiques affectées aux ressources dans un réseau virtuel. Si aucune ressource dans le réseau virtuel ne possède une adresse IP publique, vous devez d’abord créer une ressource dotée d’une adresse IP publique. Vous pouvez surveiller l’IP publique de toutes les ressources déployées par le biais de Resource Manager (non Classic) qui figurent dans [Réseau virtuel pour services Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (y compris les équilibreurs de charge Azure pour lesquels les machines virtuelles principales se trouvent dans le réseau virtuel), à l’exception des instances Azure App Service Environment. Pour poursuivre ce tutoriel, vous pouvez créer rapidement une machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).    

## <a name="configure-ddos-diagnostic-logs"></a>Configurer les journaux de diagnostic DDoS

Si vous souhaitez activer automatiquement la journalisation des diagnostics sur toutes les adresses IP publiques au sein d’un environnement, passez à [Activer la journalisation des diagnostics sur toutes les adresses IP publiques](#enable-diagnostic-logging-on-all-public-ips).

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sous **Paramètres**, sélectionnez **Paramètres de diagnostic**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique que vous souhaitez journaliser.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource**, puis sélectionnez l’adresse IP publique spécifique dont vous souhaitez activer les journaux.
6. Sélectionnez **Ajouter le paramètre de diagnostic**. Sous **Détails de la catégorie**, choisissez autant d’options que vous le souhaitez, puis sélectionnez **Enregistrer**.

    ![Paramètres de diagnostic DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. Sous **Détails de la destination**, sélectionnez autant d’options que nécessaire :

    - **Archiver dans un compte de stockage** : les données sont écrites dans un compte Stockage Azure. Pour en savoir plus sur cette option, consultez [Archiver les journaux de ressources](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Diffuser vers un hub d’événements** : permet à un récepteur de journal d’activité de sélectionner les journaux d’activité à l’aide d’un hub d’événements Azure. Les hubs d’événements permettent l’intégration à Splunk ou à d’autres systèmes SIEM. Pour en savoir plus sur cette option, consultez [Diffuser les journaux de ressources sur un hub d’événements](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Envoyer à Log Analytics** : écrit les journaux d’activité dans le service Azure Monitor. Pour en savoir plus sur cette option, consultez [Collecte des journaux d’activité et des métriques des services Azure dans l’espace de travail Log Analytics d’Azure Monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Schémas des journaux

Le tableau ci-après répertorie les noms de champ et leurs descriptions :

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Nom du champ | Description |
| --- | --- |
| **TimeGenerated** | Date et heure UTC de création de la notification. |
| **ResourceId** | L’ID de ressource de votre adresse IP publique. |
| **Catégorie** | Pour les notifications, ce sera `DDoSProtectionNotifications`.|
| **ResourceGroup** | Le groupe de ressources qui contient votre adresse IP publique et votre réseau virtuel. |
| **SubscriptionId** | ID d’abonnement du plan de protection DDoS. |
| **Ressource** | Le nom de votre adresse IP publique. |
| **ResourceType** | Ce sera toujours `PUBLICIPADDRESS`. |
| **OperationName** | Pour les notifications, ce sera `DDoSProtectionNotifications`.  |
| **Message** | Détails de l’attaque. |
| **Type** | Type de notification. Les valeurs possibles incluent `MitigationStarted`. `MitigationStopped`. |
| **PublicIpAddress** | Votre adresse IP publique. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Nom du champ | Description |
| --- | --- |
| **TimeGenerated** | Date et heure UTC de création du journal de flux. |
| **ResourceId** | L’ID de ressource de votre adresse IP publique. |
| **Catégorie** | Pour les journaux de flux, ce sera `DDoSMitigationFlowLogs`.|
| **ResourceGroup** | Le groupe de ressources qui contient votre adresse IP publique et votre réseau virtuel. |
| **SubscriptionId** | ID d’abonnement du plan de protection DDoS. |
| **Ressource** | Le nom de votre adresse IP publique. |
| **ResourceType** | Ce sera toujours `PUBLICIPADDRESS`. |
| **OperationName** | Pour les journaux de flux, ce sera `DDoSMitigationFlowLogs`. |
| **Message** | Détails de l’attaque. |
| **SourcePublicIpAddress** | Adresse IP publique du client générant le trafic vers votre adresse IP publique. |
| **SourcePort** | Numéro de port compris entre 0 et 65535. |
| **DestPublicIpAddress** | Votre adresse IP publique. |
| **DestPort** | Numéro de port compris entre 0 et 65535. |
| **Protocole** | Type de protocole. Les valeurs possibles incluent `tcp`, `udp`, `other`.|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Nom du champ | Description |
| --- | --- |
| **TimeGenerated** | Date et heure UTC de création du rapport. |
| **ResourceId** | L’ID de ressource de votre adresse IP publique. |
| **Catégorie** | Pour les notifications, ce sera `DDoSProtectionNotifications`.|
| **ResourceGroup** | Le groupe de ressources qui contient votre adresse IP publique et votre réseau virtuel. |
| **SubscriptionId** | ID d’abonnement du plan de protection DDoS. |
| **Ressource** | Le nom de votre adresse IP publique. |
| **ResourceType** | Ce sera toujours `PUBLICIPADDRESS`. |
| **OperationName** | Pour les rapports d’atténuation, il s’agit de `DDoSMitigationReports`. |
| **ReportType** | Les valeurs possibles incluent `Incremental`, `PostMitigation`.|
| **MitigationPeriodStart** | Date et heure UTC de début de l’atténuation.  |
| **MitigationPeriodEnd** | Date et heure UTC de fin de l’atténuation. |
| **IPAddress** | Votre adresse IP publique. |
| **AttackVectors** |  Répartition des types d’attaques. Les clés incluent `TCP SYN flood`, `TCP flood`, `UDP flood`, `UDP reflection` et `Other packet flood`.|
| **TrafficOverview** |  Répartition du trafic d’attaque. Les clés incluent `Total packets`, `Total packets dropped`, `Total TCP packets`, `Total TCP packets dropped`, `Total UDP packets`, `Total UDP packets dropped`, `Total Other packets` et `Total Other packets dropped`. |
| **Protocoles** | Répartition des protocoles impliqués. Les clés incluent `TCP`, `UDP` et `Other`. |
| **DropReasons** | Répartition des motifs pour les paquets ignorés. Les clés sont `Protocol violation invalid TCP syn`, `Protocol violation invalid TCP`, `Protocol violation invalid UDP`, `UDP reflection`, `TCP rate limit exceeded`, `UDP rate limit exceeded`, `Destination limit exceeded`, `Other packet flood`, `Rate limit exceeded` et `Packet was forwarded to service`. |
| **TopSourceCountries** | Répartition des 10 principaux pays sources de trafic entrant. |
| **TopSourceCountriesForDroppedPackets** | Répartition des 10 principaux pays sources de trafic d’attaque atténué. |
| **TopSourceASNs** | Répartition des 10 principaux numéros de système autonome source (ASN) du trafic entrant.  |
| **SourceContinents** | Répartition des continents sources du trafic entrant. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Activer la journalisation des diagnostics sur toutes les adresses IP publiques

Ce [modèle](https://aka.ms/ddosdiaglogs) crée une définition Azure Policy pour activer automatiquement la journalisation des diagnostics sur tous les journaux d’IP publiques dans une étendue définie.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Afficher les données de journal dans des classeurs

### <a name="azure-sentinel-data-connector"></a>Connecteur de données Azure Sentinel

Vous pouvez connecter vos journaux à Azure Sentinel, afficher et analyser vos données dans des classeurs, créer des alertes personnalisées et les incorporer dans des processus d’investigation. Pour vous connecter à Azure Sentinel, consultez [Se connecter à Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Connecteur Azure Sentinel DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Classeur Azure DDoS Protection

Vous pouvez utiliser [ce modèle Azure Resource Manager (ARM)](https://aka.ms/ddosworkbook) pour déployer un classeur d’analyse des attaques. Ce classeur vous permet de visualiser les données d’attaque sur plusieurs panneaux filtrables pour comprendre facilement ce qui est en jeu. 

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![Classeur Protection DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Valider et tester

Pour simuler une attaque DDoS afin de valider vos journaux, consultez [Valider la détection d’une attaque DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

- Configurez les journaux de diagnostic DDoS, y compris les notifications, les rapports d’atténuation et les journaux de flux d’atténuation. 
- Activez la journalisation des diagnostics sur toutes les adresses IP publiques dans une étendue définie.
- Affichez les données de journal dans des classeurs.

Pour savoir comment configurer les alertes d’attaque, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Afficher et configurer les alertes de protection DDoS](alerts.md)
