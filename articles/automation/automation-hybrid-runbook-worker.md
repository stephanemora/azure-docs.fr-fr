---
title: Vue d’ensemble du Runbook Worker hybride d’Azure Automation
description: Cet article fournit une vue d’ensemble du Runbook Worker hybride qui vous permet d’exécuter des runbooks sur des machines de votre centre de données local ou de votre fournisseur de cloud.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 0960dfe067e5092f3d64f66cad1d49c2bea28ae6
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186246"
---
# <a name="hybrid-runbook-worker-overview"></a>Vue d’ensemble des Runbook Worker hybrides

Dans Azure Automation, les Runbooks peuvent ne pas avoir accès aux ressources d’autres clouds ou de votre environnement local, car ils s’exécutent dans la plateforme cloud Azure. Vous pouvez utiliser la fonctionnalité Runbook Worker hybride d’Azure Automation pour exécuter des runbooks directement sur la machine qui héberge le rôle et sur les ressources de l’environnement afin de gérer ces ressources locales. Les runbooks sont stockés et gérés dans Azure Automation et remis ensuite à une ou plusieurs machines assignées.

L’image suivante illustre cette fonctionnalité :

![Vue d’ensemble des Runbook Worker hybrides](media/automation-hybrid-runbook-worker/automation.png)

Un Runbook Worker hybride peut s’exécuter sur le système d’exploitation Windows ou Linux. Il dépend de l’agent [Log Analytics](../azure-monitor/platform/log-analytics-agent.md) qui est rattaché à un [espace de travail Log Analytics](../azure-monitor/platform/design-logs-deployment.md) Azure Monitor. L’espace de travail ne vise pas seulement à superviser la machine pour le système d’exploitation pris en charge ; il sert aussi à télécharger les composants nécessaires au Runbook Worker hybride.

Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent. Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité. Chaque machine peut héberger un seul Worker hybride associé à un seul compte Automation.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s’exécute. Chaque worker du groupe interroge Azure Automation pour voir si des travaux sont disponibles. Si un travail est disponible, le premier worker qui le reçoit s’en occupe. Le temps de traitement de la file d’attente des travaux varie selon la charge et le profil matériel du Worker hybride. Vous ne pouvez pas spécifier un worker en particulier.

Utilisez un Runbook Worker hybride plutôt qu’un [bac à sable (sandbox) Azure](automation-runbook-execution.md#runbook-execution-environment), parce qu’il est exempt de bon nombre des [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) du bac à sable (sandbox) en matière d’espace disque, de mémoire ou de sockets réseau. Les limites d’un Worker hybride n’ont trait qu’aux ressources propres de ce dernier.

> [!NOTE]
> Les Runbook Worker hybrides ne sont pas contraints par la limite de temps de [répartition de charge équilibrée](automation-runbook-execution.md#fair-share) imposée au bac à sable Azure.

## <a name="hybrid-runbook-worker-installation"></a>Installation de Runbook Worker hybride

Le processus d’installation d’un Runbook Worker hybride diffère selon le système d’exploitation. Le tableau ci-dessous définit les types de déploiement.

|Système d’exploitation  |Types de déploiement  |
|---------|---------|
|Windows     | [Automatisé](automation-windows-hrw-install.md#automated-deployment)<br>[Manuel](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

La méthode d’installation recommandée consiste à utiliser un runbook Azure Automation afin d’automatiser entièrement le processus de configuration d’une machine Windows. Si cela n’est pas possible, vous pouvez suivre une procédure pas à pas pour installer et configurer le rôle manuellement. Pour une machine Linux, vous exécutez un script Python pour installer l’agent.

## <a name="network-planning"></a><a name="network-planning"></a>Planification réseau

Pour que le Runbook Worker hybride se connecte à Azure Automation et y soit inscrit, il doit avoir accès au numéro de port et aux URL décrites dans cette section. Le Worker doit également avoir accès aux [ports et URL requis pour l’agent Log Analytics](../azure-monitor/platform/agent-windows.md) pour pouvoir se connecter à l’espace de travail Azure Monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Le port et les URL suivants sont requis pour le Runbook Worker hybride :

* Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : `*.azure-automation.net`
* URL globale de US Gov Virginie : `*.azure-automation.us`
* Service Agent : `https://<workspaceId>.agentsvc.azure-automation.net`

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez restreindre la communication du Runbook Worker hybride à ce centre de données régional. Examinez les [enregistrements DNS utilisés par Azure Automation](how-to/automation-region-dns-records.md) pour identifier les enregistrements DNS obligatoires.

### <a name="proxy-server-use"></a>Utilisation du serveur proxy

Si vous utilisez un serveur proxy pour la communication entre Azure Automation et les machines exécutant l’agent Log Analytics, veillez à ce que les ressources appropriées soient accessibles. Le délai d’expiration pour les demandes du Runbook Worker hybride et des services Automation est de 30 secondes. Après trois tentatives, une demande échoue.

### <a name="firewall-use"></a>Utilisation du pare-feu

Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer le pare-feu pour autoriser l’accès. Si vous utilisez la passerelle Log Analytics en tant que proxy, vérifiez qu’elle est configurée pour les Runbook Workers hybrides. Consultez [Configuration de la passerelle Log Analytics pour les Automation Hybrid Workers](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Étiquettes de service

Azure Automation prend en charge les étiquettes de service de réseau virtuel Azure, à commencer par l’étiquette de service [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Vous pouvez utiliser des étiquettes de service pour définir des contrôles d’accès réseau sur des [groupes de sécurité réseau](../virtual-network/security-overview.md#security-rules) ou sur le [pare-feu Azure](../firewall/service-tags.md). Les étiquettes de service peuvent être utilisées à la place d’adresses IP spécifiques pendant la création de règles de sécurité. En spécifiant le nom d’étiquette de service **GuestAndHybridManagement** dans le champ source ou de destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service Automation. Cette étiquette de service ne permet pas d’autoriser un contrôle plus précis en limitant les plages d’adresses IP à une région spécifique.

L’étiquette de service du service Azure Automation fournit uniquement les adresses IP utilisées pour les scénarios suivants :

* Déclencher des webhooks à partir de votre réseau virtuel
* Autoriser les Runbooks Worker hybrides ou les agents State Configuration de votre réseau virtuel à communiquer avec le service Automation

>[!NOTE]
>L’étiquette de service **GuestAndHybridManagement** ne prend actuellement pas en charge l’exécution de tâches de runbook dans un bac à sable (sandbox) Azure, uniquement directement sur un Runbook Worker hybride.

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management sur Runbook Worker hybride

Quand la solution [Update Management](automation-update-management.md) d’Azure Automation est activée, toute machine connectée à votre espace de travail Log Analytics est automatiquement configurée en tant que Runbook Worker hybride. Chaque Worker peut prendre en charge des runbooks ciblant la gestion des mises à jour.

Une machine configurée de cette manière n’est inscrite auprès d’aucun groupe de Runbooks Workers hybrides déjà défini dans votre compte Automation. Vous pouvez ajouter la machine à un groupe de Runbooks Workers hybrides, mais vous devez utiliser le même compte pour Update Management et l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Adresses Update Management pour Runbook Worker hybride

En plus des adresses et des ports standard que requiert le Runbook Worker hybride, Update Management a besoin des adresses figurant dans le tableau suivant. La communication avec ces adresses utilise le port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`     | `*.ods.opinsights.azure.us`         |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration sur un Runbook Worker hybride

Vous pouvez exécuter [Azure Automation State Configuration](automation-dsc-overview.md) sur un Runbook Worker hybride. Pour gérer la configuration des serveurs qui prennent en charge le Runbook Worker hybride, vous devez les ajouter en tant que nœuds DSC. Consultez [Activer la gestion des machines par Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks sur un Runbook Worker hybride

Vous pouvez faire en sorte que les runbooks gèrent les ressources sur la machine locale ou qu’ils s’exécutent par rapport aux ressources de l’environnement local dans lequel un Runbook Worker hybride est déployé. Dans ce cas, vous pouvez choisir d’exécuter vos runbooks sur le Worker hybride plutôt que dans un compte Automation. Les runbooks exécutés sur un runbook Worker hybride sont identiques en structure à ceux que vous exécutez dans le compte Automation. Consultez [Exécuter des runbooks sur un runbook Worker hybride](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Travaux Runbook Worker hybride

Les travaux du Runbook Worker hybride s’exécutent sous le compte **Système** local sur Windows, ou le compte [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) sur Linux. Azure Automation gère les travaux exécutés sur des runbooks Workers hybrides d’une manière légèrement différente des travaux exécutés dans des bacs à sable Azure. Consultez [Environnement d’exécution de runbook](automation-runbook-execution.md#runbook-execution-environment).

Si la machine hôte du runbook Worker hybride redémarre, tous les travaux du runbook en cours d’exécution redémarrent au début ou au dernier point de contrôle pour les runbooks PowerShell Workflow. Quand un travail de runbook a redémarré plus de trois fois, il est suspendu.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorisations des runbooks pour un runbook Worker hybride

Étant donné qu’ils accèdent à des ressources non-Azure, les runbooks s’exécutant sur un runbook Worker hybride ne peuvent pas utiliser le mécanisme d’authentification généralement utilisé par les runbooks pour s’authentifier auprès des ressources Azure. Un runbook peut fournir sa propre authentification auprès des ressources locales ou en configurer une à l’aide d’[identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Vous pouvez également spécifier un compte d’identification pour fournir un contexte utilisateur pour l’ensemble des runbooks.

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour savoir comment résoudre les problèmes de vos Runbook Workers hybrides, consultez [Résoudre les problèmes liés à la fonctionnalité Runbook Worker hybride](troubleshoot/hybrid-runbook-worker.md#general).
