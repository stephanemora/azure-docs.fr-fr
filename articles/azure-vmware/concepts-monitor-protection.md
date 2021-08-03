---
title: Concepts - Supervision et protection
description: Découvrez les services natifs Azure qui vous aident à sécuriser et à protéger vos charges de travail Azure VMware Solution.
ms.topic: conceptual
ms.date: 06/14/2021
ms.openlocfilehash: d735e705ffbccaa0e5fb38951c09b01a22818a0c
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758135"
---
# <a name="monitor-and-protect-azure-vmware-solution-workloads"></a>Superviser et protéger les charges de travail Azure VMware Solution

Les services natifs Microsoft Azure vous permettent de superviser, gérer et protéger vos machines virtuelles sur les machines virtuelles locales et Azure VMware Solution. Les services natifs Azure que vous pouvez intégrer à Azure VMware Solution sont les suivants :

- L’**espace de travail Log Analytics** est un environnement unique pour stocker des données de journal. Chaque espace de travail a son propre dépôt de données et sa propre configuration. Les solutions et sources de données sont configurées pour stocker leurs données dans un espace de travail spécifique. Déployez facilement l’agent Log Analytics à l’aide des serveurs Azure Arc prenant en charge l’extension de machine virtuelle pour les machines virtuelles nouvelles et existantes. 
- **Azure Security Center** est un système unifié d’administration de la sécurité de l’infrastructure. Il renforce la sécurité des centres de données et offre une protection avancée contre les menaces dans les charges de travail hybrides dans le cloud ou en local. Il évalue la vulnérabilité des machines virtuelles Azure VMware Solution et déclenche des alertes en fonction des besoins. Ces alertes de sécurité peuvent être transférées à Azure Monitor à des fins de résolution. Vous pouvez définir des stratégies de sécurité dans Azure Security Center. Pour plus d’informations, consultez [Intégrer Azure Security Center à Azure VMware Solution](azure-security-integration.md).
- **[Azure Monitor](../azure-monitor/vm/vminsights-enable-overview.md)** est une solution complète pour collecter et analyser la télémétrie de vos environnements cloud et locaux et agir en conséquence. Il ne nécessite aucun déploiement. Avec Azure Monitor, vous pouvez surveiller les performances du système d’exploitation invité, et découvrir et mapper les dépendances d’application pour Azure VMware Solution ou des machines virtuelles locales. Votre espace de travail Log Analytics dans Azure Monitor active la collecte des journaux et la collecte des compteurs de performances à l’aide de l’agent ou d’extensions Log Analytics. Collectez les données et les journaux sur un point unique, et présentez-les à différents services natifs Azure.
- **Azure Arc** étend la gestion d’Azure à toute infrastructure, notamment Azure VMware Solution, infrastructure locale ou autre plateforme cloud. Les [serveurs avec Azure Arc](../azure-arc/servers/overview.md) vous permettent de gérer vos machines virtuelles et vos serveurs physiques Windows et Linux hébergés *en dehors* d’Azure sur votre réseau d’entreprise ou un autre fournisseur de cloud. Vous pouvez attacher un cluster Kubernetes hébergé dans votre environnement Azure VMware Solution à l’aide de [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md). 
- **[Azure Update Management](../automation/update-management/overview.md)** dans Azure Automation gère les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux dans un environnement hybride. Il surveille la conformité des mises à jour correctives, et transfère les alertes d’écart de mise à jour corrective à Azure Monitor à des fins de correction. La solution Azure Update Management doit se connecter à votre espace de travail Log Analytics pour utiliser les données stockées afin d’évaluer l’état des mises à jour sur vos machines virtuelles. 
 


## <a name="topology"></a>Topologie

Le diagramme illustre l’architecture de surveillance intégrée pour les machines virtuelles du service Azure VMware Solution.

:::image type="content" source="media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png" alt-text="Diagramme montrant l’architecture de supervision Azure intégrée." border="false":::

L’agent Log Analytics active la collecte de données de journal à partir d’Azure, d’Azure VMware Solution et de machines virtuelles locales. Les données de journal sont envoyées aux journaux Azure Monitor et stockées dans un espace de travail Log Analytics. Vous pouvez déployer l’agent Log Analytics à l’aide de serveurs compatibles avec Arc [prenant en charge les extensions de machine virtuelle](../azure-arc/servers/manage-vm-extensions.md) pour les machines virtuelles nouvelles et existantes. 

Une fois les journaux collectés par l’espace de travail Log Analytics, vous pouvez configurer l’espace de travail Log Analytics avec Azure Security Center. Azure Security Center évalue l’état des vulnérabilités des machines virtuelles Azure VMware Solution et déclenche une alerte pour toute vulnérabilité critique. Il évalue par exemple les correctifs de système d’exploitation manquants, les configurations de sécurité incorrectes et la [protection des points de terminaison](../security-center/security-center-services.md).

Vous pouvez configurer l’espace de travail Log Analytics avec Azure Sentinel pour la détection des alertes, la visibilité des menaces, la chasse et la réponse aux menaces. Dans le diagramme précédent, Azure Security Center est connecté à Azure Sentinel à l’aide d’un connecteur Azure Security Center. Azure Security Center transmet la vulnérabilité de l’environnement à Azure Sentinel pour créer un incident et la mappe à d’autres menaces. Vous pouvez également créer la requête de règles planifiées pour détecter les activités indésirables et les convertir en incidents.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez couvert les concepts de réseau et d’interconnexion d’Azure VMware Solution, vous voudrez peut-être en savoir plus :

- [Intégration des services natifs Azure dans Azure VMware Solution](integrate-azure-native-services.md)
- [Intégrer Azure Security Center avec Azure VMware Solution](azure-security-integration.md)
- [Authentification des comptes Automation](../automation/automation-security-overview.md)
- [Conception de votre déploiement de journaux Azure Monitor](../azure-monitor/logs/design-logs-deployment.md) et [Azure Monitor](../azure-monitor/overview.md)
- [Planification Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) et [Plateforme prises en charge pour Security Center](../security-center/security-center-os-coverage.md)


