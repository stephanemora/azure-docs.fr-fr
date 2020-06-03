---
title: Vue d’ensemble du Runbook Worker hybride d’Azure Automation
description: Cet article fournit une vue d’ensemble du Runbook Worker hybride qui vous permet d’exécuter des runbooks sur des machines de votre centre de données local ou de votre fournisseur de cloud.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835289"
---
# <a name="hybrid-runbook-worker-overview"></a>Vue d’ensemble des Runbook Worker hybrides

Dans Azure Automation, les Runbooks peuvent ne pas avoir accès aux ressources d’autres clouds ou de votre environnement local, car ils s’exécutent dans la plateforme cloud Azure. Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs assignés.

L’image suivante illustre cette fonctionnalité :

![Vue d’ensemble des Runbook Worker hybrides](media/automation-hybrid-runbook-worker/automation.png)

Un Runbook Worker hybride peut exécuter le système d’exploitation Windows ou Linux. Pour la surveillance, il requiert l’utilisation d’Azure Monitor et d’un agent Log Analytics pour le système d’exploitation pris en charge. Pour plus d’informations, voir [Azure Monitor](automation-runbook-execution.md#azure-monitor).

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

La méthode d’installation recommandée consiste à utiliser un runbook Azure Automation afin d’automatiser totalement le processus de configuration d’un ordinateur Windows. La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement. Pour une machine Linux, vous exécutez un script Python pour installer l’agent.

## <a name="network-planning"></a><a name="network-planning"></a>Planification réseau

Pour que le Runbook Worker hybride se connecte à Azure Automation et y soit inscrit, il doit avoir accès au numéro de port et aux URL décrites dans cette section. Le Worker doit également avoir accès aux [ports et URL requis pour l’agent Log Analytics](../azure-monitor/platform/agent-windows.md) pour pouvoir se connecter à l’espace de travail Azure Monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Le port et les URL suivants sont requis pour le Runbook Worker hybride :

* Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : *.azure-automation.net
* URL globale d’US Gov Virginie : *.azure-automation.us
* Service de l’agent : https://\<ID_espace_de_travail\>.agentsvc.azure-automation.net

Il est recommandé d’utiliser les adresses répertoriées lors de la définition d’[exceptions](automation-runbook-execution.md#exceptions). Pour les adresses IP, vous pouvez télécharger les [plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ce fichier, qui est mis à jour chaque semaine, possède les plages actuellement déployées et tous les changements à venir des plages d’adresses IP.

### <a name="dns-records-per-region"></a>Enregistrements DNS par région

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez restreindre la communication du Runbook Worker hybride à ce centre de données régional. Le tableau suivant indique l’enregistrement DNS pour chaque région.

| **Région** | **Enregistrement DNS** |
| --- | --- |
| Centre de l’Australie |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australie Est |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA Est 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japon Est |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europe Nord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Asie Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Centre-USA Ouest | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europe Ouest |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA Ouest 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Pour obtenir la liste des adresses IP régionales plutôt que celle des noms des régions, téléchargez le fichier XML [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft. Un fichier d’adresses IP mis à jour est publié chaque semaine. 

Le fichier d’adresses IP répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Il comprend des plages de calcul, SQL et de stockage, et reflète les plages actuellement déployées ainsi que toutes les futures modifications des plages d’adresses IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum.

Il est recommandé de télécharger le nouveau fichier d’adresses IP chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services en cours d’exécution dans Azure. 

> [!NOTE]
> Si vous utilisez Azure ExpressRoute, n’oubliez pas que le fichier d’adresses IP est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

### <a name="proxy-server-use"></a>Utilisation du serveur proxy

Si vous utilisez un serveur proxy pour la communication entre Azure Automation et l’agent Log Analytics, assurez-vous que les ressources appropriées sont accessibles. Le délai d’expiration pour les demandes du Runbook Worker hybride et des services Automation est de 30 secondes. Après trois tentatives, une demande échoue. 

### <a name="firewall-use"></a>Utilisation du pare-feu

Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer le pare-feu pour autoriser l’accès. Si vous utilisez la passerelle Log Analytics en tant que proxy, vérifiez qu’elle est configurée pour les Runbook Workers hybrides. Consultez [Configuration de la passerelle Log Analytics pour les Automation Hybrid Workers](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management sur Runbook Worker hybride

Quand la solution [Update Management](automation-update-management.md) d’Azure Automation est activée, tout ordinateur connecté à votre espace de travail Log Analytics est automatiquement configuré en tant que Runbook Worker hybride. Chaque Worker peut prendre en charge des runbooks ciblant la gestion des mises à jour. 

Un ordinateur configuré de cette manière n’est inscrit auprès d’aucun des groupes de Runbook Workers hybrides déjà définis dans votre compte Automation. Vous pouvez ajouter l’ordinateur à un groupe de Runbook Workers hybrides, mais vous devez utiliser le même compte pour Update Management et l’appartenance au groupe de Runbook Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Adresses Update Management pour Runbook Worker hybride

En plus des adresses et des ports standard que requiert le Runbook Worker hybride, Update Management a besoin des adresses figurant dans le tableau suivant. La communication avec ces adresses utilise le port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration sur un Runbook Worker hybride

Vous pouvez exécuter [Azure Automation State Configuration](automation-dsc-overview.md) sur un Runbook Worker hybride. Pour gérer la configuration des serveurs qui prennent en charge le Runbook Worker hybride, vous devez les ajouter en tant que nœuds DSC. Consultez [Activer la gestion des machines par Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks sur un Runbook Worker hybride

Vous pouvez avoir des runbooks qui gèrent les ressources sur l’ordinateur local, ou s’exécutent par rapport aux ressources de l’environnement local dans lequel un runbook Worker hybride est déployé. Dans ce cas, vous pouvez choisir d’exécuter vos runbooks sur le Worker hybride plutôt que dans un compte Automation. Les runbooks exécutés sur un runbook Worker hybride sont identiques en structure à ceux que vous exécutez dans le compte Automation. Consultez [Exécuter des runbooks sur un runbook Worker hybride](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Travaux Runbook Worker hybride

Les travaux du Runbook Worker hybride s’exécutent sous le compte **Système** local sur Windows, ou le compte [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) sur Linux. Azure Automation gère les travaux exécutés sur des runbooks Workers hybrides d’une manière légèrement différente des travaux exécutés dans des bacs à sable Azure. Consultez [Environnement d’exécution de runbook](automation-runbook-execution.md#runbook-execution-environment).

Si la machine hôte du runbook Worker hybride redémarre, tous les travaux du runbook en cours d’exécution redémarrent au début ou au dernier point de contrôle pour les runbooks PowerShell Workflow. Quand un travail de runbook a redémarré plus de trois fois, il est suspendu.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorisations des runbooks pour un runbook Worker hybride

Étant donné qu’ils accèdent à des ressources non-Azure, les runbooks s’exécutant sur un runbook Worker hybride ne peuvent pas utiliser le mécanisme d’authentification généralement utilisé par les runbooks pour s’authentifier auprès des ressources Azure. Un runbook peut fournir sa propre authentification auprès des ressources locales ou en configurer une à l’aide d’[identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Vous pouvez également spécifier un compte d’identification pour fournir un contexte utilisateur pour l’ensemble des runbooks.

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour savoir comment résoudre les problèmes de vos Runbook Workers hybrides, consultez [Résoudre les problèmes liés à la fonctionnalité Runbook Worker hybride](troubleshoot/hybrid-runbook-worker.md#general).