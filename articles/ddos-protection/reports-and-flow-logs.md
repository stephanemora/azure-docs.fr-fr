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
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5a6fc8e9b316f7c4740ee27fe72c5f056f071d73
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912692"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Configurer les rapports de prévention des attaques DDoS et les journaux de flux 

La norme Azure DDoS Protection fournit une visualisation et des insights détaillés sur les attaques avec l’analyse des attaques DDoS. Les clients qui protègent leurs réseaux virtuels contre les attaques DDoS ont une visibilité détaillée du trafic des attaques et des mesures prises pour leur prévention par le biais de rapports de prévention des attaques et de journaux de flux de prévention des attaques. La télémétrie enrichie est exposée via Azure Monitor et inclut des métriques détaillées pendant la durée d’une attaque DDoS. La génération d’alertes peut être configurée pour l’une des métriques Azure Monitor exposées par DDoS Protection. La journalisation peut être également intégrée à [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics et Stockage Azure pour l’analyse avancée par le biais de l’interface de diagnostic d’Azure Monitor.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Afficher et configurer les rapports de prévention des attaques DDoS
> * Afficher et configurer les journaux de flux de prévention des attaques DDoS

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Avant de pouvoir exécuter la procédure de ce tutoriel, vous devez créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Afficher et configurer les rapports de prévention des attaques DDoS

La fonctionnalité Rapports de prévention des attaques utilise les données de protocole Netflow qui sont ensuite regroupées pour fournir des informations détaillées concernant l’attaque de votre ressource. Chaque fois qu’une ressource IP publique est attaquée, la génération de rapport démarre en même temps que la prévention. Un rapport incrémentiel est généré toutes les 5 minutes et un rapport post-prévention concernant toute la période de prévention. Cela permet de garantir que dans le cas où l’attaque DDoS se poursuit pendant une durée plus longue, vous pourrez afficher l’instantané le plus récent du rapport de prévention toutes les 5 minutes et un récapitulatif complet une fois la prévention de l’attaque terminée. 

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sous **PARAMÈTRES** , sélectionnez **Paramètres de diagnostic**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique que vous souhaitez journaliser.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource** , puis sélectionnez l’adresse IP publique spécifique dont vous souhaitez journaliser les métriques.
6. Sélectionnez **Activer les diagnostics pour collecter le journal DDoSMitigationReports** , puis sélectionnez autant d’options que nécessaire parmi les suivantes :

    - **Archiver dans un compte de stockage** : les données sont écrites dans un compte Stockage Azure. Pour en savoir plus sur cette option, consultez [Archiver les journaux de ressources](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Diffuser vers un hub d’événements** : permet à un récepteur de journal d’activité de sélectionner les journaux d’activité à l’aide d’un hub d’événements Azure. Les hubs d’événements permettent l’intégration à Splunk ou à d’autres systèmes SIEM. Pour en savoir plus sur cette option, consultez [Diffuser les journaux de ressources sur un hub d’événements](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Envoyer à Log Analytics** : écrit les journaux d’activité dans le service Azure Monitor. Pour en savoir plus sur cette option, consultez [Collecte des journaux d’activité et des métriques des services Azure dans l’espace de travail Log Analytics d’Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le rapport incrémentiel et le rapport de prévention incluent tous les deux les champs suivants :
- Vecteurs d’attaque
- Statistiques de trafic
- Raison des paquets abandonnés
- Protocoles impliqués
- 10 principaux pays ou régions sources
- 10 principaux ASN sources

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Afficher et configurer les journaux de flux de prévention des attaques DDoS
Les journaux de flux de prévention des attaques vous permettent de passer en revue le trafic abandonné, le trafic transféré et d’autres points de données intéressants pendant une attaque DDoS active en temps quasi réel. Vous pouvez ingérer le flux constant de ces données dans Azure Sentinel ou des systèmes SIEM tiers par le biais d’un hub d’événements pour la supervision en temps quasi réel, prendre des actions éventuelles et répondre aux besoins de vos opérations de défense.

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sous **PARAMÈTRES** , sélectionnez **Paramètres de diagnostic**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique que vous souhaitez journaliser.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource** , puis sélectionnez l’adresse IP publique spécifique dont vous souhaitez journaliser les métriques.
6. Sélectionnez **Activer les diagnostics pour collecter le journal DDoSMitigationFlowLogs** , puis sélectionnez autant d’options que nécessaire parmi les suivantes :

    - **Archiver dans un compte de stockage** : les données sont écrites dans un compte Stockage Azure. Pour en savoir plus sur cette option, consultez [Archiver les journaux de ressources](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Diffuser vers un hub d’événements** : permet à un récepteur de journal d’activité de sélectionner les journaux d’activité à l’aide d’un hub d’événements Azure. Les hubs d’événements permettent l’intégration à Splunk ou à d’autres systèmes SIEM. Pour en savoir plus sur cette option, consultez [Diffuser les journaux de ressources sur un hub d’événements](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Envoyer à Log Analytics** : écrit les journaux d’activité dans le service Azure Monitor. Pour en savoir plus sur cette option, consultez [Collecte des journaux d’activité et des métriques des services Azure dans l’espace de travail Log Analytics d’Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-sentinel-data-connector"></a>Connecteur de données Azure Sentinel

Vous pouvez vous connecter à Azure Sentinel, afficher et analyser vos données dans des classeurs, créer des alertes personnalisées et les incorporer dans des processus d’investigation. Pour vous connecter à Azure Sentinel, consultez [Se connecter à Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection). 

![Connecteur Azure Sentinel DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Classeur Azure DDoS Protection

Pour afficher les données des journaux de flux dans le tableau de bord d’analyse Azure, vous pouvez importer l’exemple de tableau de bord à partir de https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Les journaux de flux comportent les champs suivants : 
- IP Source
- IP de destination
- Port source 
- Port de destination 
- Type de protocole 
- Action effectuée pendant la prévention

![Classeur Protection DDoS](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

L’analyse des attaques ne fonctionnera que si la protection DDoS standard est activée sur le réseau virtuel de l’adresse IP publique. 

## <a name="sample-log-outputs"></a>Exemples de sorties de journal

Les captures d’écran suivantes sont des exemples de sorties de journal :

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS Protection DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

- Afficher et configurer les rapports de prévention des attaques DDoS
- Afficher et configurer les journaux de flux de prévention des attaques DDoS

Pour savoir comment tester et simuler une attaque DDoS, consultez le guide de test de simulation :

> [!div class="nextstepaction"]
> [Tester à l’aide de simulations](test-through-simulations.md)

