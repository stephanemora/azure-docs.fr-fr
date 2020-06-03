---
title: 'Azure Arc pour serveurs (préversion) : vue d’ensemble'
description: Découvrez comment utiliser Azure Arc pour serveurs afin de gérer les machines hébergées en dehors d’Azure comme s’il s’agissait d’une ressource Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, Update Management, Change Tracking, inventaire, runbooks, Python, graphique, hybride
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 95a01db7d4d889df4695390bfd0d01510d83a817
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648046"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Présentation d’Azure Arc pour serveurs (préversion)

Azure Arc pour serveurs (préversion) vous permet de gérer vos machines Windows et Linux hébergées en dehors d’Azure sur votre réseau d’entreprise ou un autre fournisseur de cloud, de la même façon que vous gérez des machines virtuelles Azure natives. Quand une machine hybride est connectée à Azure, elle devient une machine connectée et est traitée comme une ressource dans Azure. Chaque machine connectée possède un ID de ressource, est gérée dans le cadre d’un groupe de ressources au sein d’un abonnement et tire parti des constructions Azure standard, comme Azure Policy et l’application d’étiquettes.

Pour bénéficier de cette expérience avec vos machines hybrides hébergées en dehors d’Azure, vous devez installer Azure Connected Machine sur chaque machine que vous envisagez de connecter à Azure. Cet agent ne fournit aucune autre fonctionnalité et ne remplace pas l’agent Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez superviser de manière proactive le système d’exploitation et les charges de travail en cours d’exécution sur la machine, gérer le système d’exploitation à l’aide de runbooks Automation ou de solutions comme Update Management ou utiliser d’autres services Azure tels qu’[Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Cette préversion est conçue à des fins d’évaluation et nous vous recommandons de ne pas gérer les machines de production critiques.
>

## <a name="supported-scenarios"></a>Scénarios pris en charge

Azure Arc pour serveurs (préversion) prend en charge les scénarios suivants avec les ordinateurs connectés :

- Affecter des [configurations invité Azure Policy](../../governance/policy/concepts/guest-configuration.md) à l’aide de la même expérience que lors de l’attribution de stratégie pour des machines virtuelles Azure.
- Consigner les données collectées par l’agent Log Analytics et stockées dans l’espace de travail Log Analytics sur lequel l’ordinateur est inscrit. Les données de journal de l’ordinateur hybride contiennent désormais des propriétés spécifiques à l’ordinateur, telles qu’un ID de ressource, qui peuvent être utilisées pour prendre en charge l’accès au journal du [contexte de la ressource](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Régions prises en charge

Avec Azure Arc pour serveurs (préversion), seules certaines régions sont prises en charge :

- WestUS2
- WestEurope
- WestAsia

Dans la plupart des cas, l’emplacement que vous sélectionnez au moment de créer le script d’installation doit être la région Azure géographiquement la plus proche de l’emplacement de votre ordinateur. Les données au repos sont stockées dans la zone géographique Azure englobant la région que vous spécifiez, ce qui peut aussi affecter votre choix de région si vous avez des contraintes en matière de résidence des données. Si la région Azure à laquelle votre ordinateur est connecté subit une panne, l’ordinateur connecté n’est pas affecté, mais les opérations de gestion effectuées avec Azure risquent de ne pas aboutir. Pour bénéficier d’une résilience en cas de panne régionale, si vous avez plusieurs emplacements qui assurent un service géographiquement redondant, il est préférable de connecter les ordinateurs de chaque emplacement à une autre région Azure.

### <a name="agent-status"></a>État de l’agent

L’agent Connected Machine envoie des messages de pulsation au service de façon régulière (toutes les 5 minutes). Si le service cesse de recevoir ces messages de pulsation d’une machine, cette machine est considérée comme étant hors connexion, et l’état dans le portail est automatiquement remplacé par **Déconnectée** au bout de 15 à 30 minutes. À la prochaine réception d’un message de pulsation de l’agent Connected Machine, son état devient automatiquement **Connecté**.

## <a name="next-steps"></a>Étapes suivantes

Avant d’évaluer ou d’activer Arc pour les serveurs (préversion) sur plusieurs machines hybrides, consultez l’article [Vue d’ensemble de l’agent Machine connectée](agent-overview.md) pour comprendre ce qui est nécessaire, les détails techniques sur l’agent, et les méthodes de déploiement.
