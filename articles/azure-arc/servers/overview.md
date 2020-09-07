---
title: Vue d’ensemble des serveurs avec Azure Arc (préversion)
description: Découvrez comment utiliser les serveurs avec Azure Arc (préversion) afin de gérer les serveurs hébergés en dehors d’Azure comme une ressource Azure.
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, Update Management, Change Tracking, inventaire, runbooks, Python, graphique, hybride
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: c368307df911f5143541bb5337eb76a208416909
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228085"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>Qu’est-ce qu’un serveur avec Azure Arc (préversion) ?

Les serveurs avec Azure Arc (préversion) vous permettent de gérer vos machines Windows et Linux hébergées en dehors d’Azure, sur votre réseau d’entreprise ou un autre fournisseur de cloud, de la même façon que vous gérez des machines virtuelles Azure natives. Quand une machine hybride est connectée à Azure, elle devient une machine connectée et est traitée comme une ressource dans Azure. Chaque machine connectée possède un ID de ressource, est gérée dans le cadre d’un groupe de ressources au sein d’un abonnement et tire parti des constructions Azure standard, comme Azure Policy et l’application d’étiquettes.

Pour bénéficier de cette expérience avec vos machines hybrides hébergées en dehors d’Azure, vous devez installer Azure Connected Machine sur chaque machine que vous envisagez de connecter à Azure. Cet agent ne fournit aucune autre fonctionnalité et ne remplace pas l’agent Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez superviser de manière proactive le système d’exploitation et les charges de travail en cours d’exécution sur la machine, gérer le système d’exploitation à l’aide de runbooks Automation ou de solutions comme Update Management ou utiliser d’autres services Azure tels qu’[Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Cette préversion est conçue à des fins d’évaluation et nous vous recommandons de ne pas gérer les machines de production critiques.
>

## <a name="supported-scenarios"></a>Scénarios pris en charge

Quand vous connectez votre machine aux serveurs avec Azure Arc (préversion), cela active la possibilité d’effectuer les tâches de gestion de la configuration suivantes :

- Affecter des [configurations invité Azure Policy](../../governance/policy/concepts/guest-configuration.md) à l’aide de la même expérience que lors de l’attribution de stratégie pour des machines virtuelles Azure.

- Surveillez les performances du système d’exploitation invité de votre machine connectée, et découvrez les composants de l’application afin de surveiller leurs processus et dépendances avec d’autres ressources que l’application communique à l’aide d’[Azure Monitor pour machines virtuelles](../../azure-monitor/insights/vminsights-overview.md).

- Simplifiez le déploiement avec d’autres services Azure tels que Azure Automation State Configuration et un espace de travail Azure Monitor Log Analytics à l’aide des [extensions de machine virtuelle Azure](manage-vm-extensions.md) prises en charge pour vos machines Windows ou Linux non-Azure. Cela comprend l’exécution de la configuration après déploiement ou de l’installation de logiciels à l’aide de l’extension de script personnalisé.

Les données de journal collectées et stockées dans un espace de travail Log Analytics à partir de la machine hybride contiennent désormais des propriétés spécifiques de la machine, telles qu’un ID de ressource. Cela peut être utilisé pour prendre en charge l’accès au journal de [contexte de ressource](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Régions prises en charge

Avec les serveurs avec Azure Arc (préversion), seules certaines régions sont prises en charge :

- USAEst
- WestUS2
- WestEurope
- AsieSudEst

Dans la plupart des cas, l’emplacement que vous sélectionnez au moment de créer le script d’installation doit être la région Azure géographiquement la plus proche de l’emplacement de votre ordinateur. Les données au repos sont stockées dans la zone géographique Azure englobant la région que vous spécifiez, ce qui peut aussi affecter votre choix de région si vous avez des contraintes en matière de résidence des données. Si la région Azure à laquelle votre ordinateur est connecté subit une panne, l’ordinateur connecté n’est pas affecté, mais les opérations de gestion effectuées avec Azure risquent de ne pas aboutir. En cas de panne régionale, si vous avez plusieurs emplacements qui prennent en charge un service géographiquement redondant, l’idéal est de connecter les machines de chaque emplacement à une région Azure distincte.

### <a name="agent-status"></a>État de l’agent

L’agent Connected Machine envoie des messages de pulsation au service de façon régulière (toutes les 5 minutes). Si le service cesse de recevoir ces messages de pulsation d’une machine, cette machine est considérée comme étant hors connexion, et l’état dans le portail est automatiquement remplacé par **Déconnectée** au bout de 15 à 30 minutes. À la prochaine réception d’un message de pulsation de l’agent Connected Machine, son état devient automatiquement **Connecté**.

## <a name="next-steps"></a>Étapes suivantes

Avant d’évaluer ou d’activer les serveurs compatibles avec Arc (préversion) sur plusieurs machines hybrides, consultez [Vue d’ensemble de l’agent Machine connectée](agent-overview.md) pour comprendre les exigences, les détails techniques concernant l’agent et les méthodes de déploiement.
