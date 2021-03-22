---
title: 'Azure Defender pour Kubernetes : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 83d0215ebca9d60d61937cb20bb82c7ccb30aac1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100625"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Présentation d’Azure Defender pour Kubernetes

Azure Kubernetes Service (AKS) est le service managé de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées.

Azure Security Center et AKS constituent une offre de sécurité Kubernetes native Cloud, avec une sécurisation renforcée de l’environnement, une protection de charge de travail et une protection à l’exécution, comme indiqué dans [Sécurité des conteneurs dans Security Center](container-security.md).

Pour la détection des menaces sur vos clusters Kubernetes, activez **Azure Defender pour Kubernetes**.

La détection des menaces au niveau de l’hôte sur les nœuds AKS Linux est disponible à condition d’activer [Azure Defender pour les serveurs](defender-for-servers-introduction.md) et son agent Log Analytics. Par contre, si votre cluster AKS est déployé sur un groupe de machines virtuelles identiques, l’agent Log Analytics n’est pas pris en charge pour le moment.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour Kubernetes** est facturé conformément à la [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/)|
|Rôles et autorisations obligatoires :|L’**administrateur de sécurité** peut ignorer les alertes.<br>Le **Lecteur de sécurité** peut afficher les résultats.|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) National/souverain (US Gov, Chine Gov, autres Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quels sont les avantages d’Azure Defender pour Kubernetes ?

Azure Defender pour Kubernetes fournit une **protection contre les menaces au niveau du cluster** en supervisant vos services managés par AKS, via les journaux récupérés par Azure Kubernetes Service (AKS).

Les tableaux de bord Kubernetes exposés, la création de rôles dotés de privilèges élevés et la création de montages sensibles sont des exemples d’événements de sécurité qu’Azure Defender pour Kubernetes supervise. Pour obtenir la liste complète des alertes au niveau du cluster AKS, consultez la [table de référence des alertes](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Vous pouvez simuler des alertes de conteneur en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

De plus, notre équipe mondiale d’experts en sécurité surveille en permanence l’évolution des menaces. Ils ajoutent des alertes et des vulnérabilités propres aux conteneurs au fur et à mesure de leur découverte.

>[!NOTE]
> Security Center génère des alertes de sécurité pour les actions et les déploiements Azure Kubernetes Service se produisant **après** votre activation d’Azure Defender pour Kubernetes.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender pour Kubernetes – FAQ

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Puis-je quand même bénéficier des protections AKS sans l’agent Log Analytics ?

Le plan **Azure Defender pour Kubernetes** fournit des protections au niveau du cluster. Si vous déployez également l’agent Log Analytics d’**Azure Defender pour les serveurs**, vous bénéficiez de la protection contre les menaces sur vos nœuds avec ce plan. Pour en savoir plus, consultez [Présentation d’Azure Defender pour les serveurs](defender-for-servers-introduction.md).

Nous vous recommandons de déployer les deux pour bénéficier de la protection la plus complète possible.

Si vous choisissez de ne pas installer l’agent sur vos hôtes, vous ne bénéficiez que d’une partie de la protection contre les menaces et des alertes de sécurité. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Est-ce qu’AKS m’autorise à installer des extensions de machine virtuelle personnalisées sur mes nœuds AKS ?
Pour qu’Azure Defender supervise vos nœuds AKS, ceux-ci doivent exécuter l’agent Log Analytics. 

AKS est un service managé et, l’agent Log Analytics étant une extension managée par Microsoft, il est également pris en charge sur les clusters AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Si mon cluster exécute déjà un agent Azure Monitor pour conteneurs, ai-je besoin de l’agent Log Analytics ?
Pour qu’Azure Defender supervise vos nœuds AKS, ceux-ci doivent exécuter l’agent Log Analytics.

Si vos clusters exécutent déjà l’agent Azure Monitor pour conteneurs, vous pouvez également installer l’agent Log Analytics, et les deux agents peuvent fonctionner conjointement sans aucun problème.

[En savoir plus sur l’agent Azure Monitor pour conteneurs](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert la protection Kubernetes de Security Center, notamment Azure Defender pour Kubernetes. 

> [!div class="nextstepaction"]
> [Activer Azure Defender](enable-azure-defender.md)

Pour des informations connexes, consultez les articles suivants : 

- [Diffuser des alertes vers un système SIEM, SOAR ou une solution de gestion des services informatiques](export-to-siem.md)
- [Table de référence des alertes](alerts-reference.md)
