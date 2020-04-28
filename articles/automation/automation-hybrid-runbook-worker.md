---
title: Runbook Worker hybride Azure Automation
description: Cet article fournit des informations sur l’installation et l’utilisation de la fonctionnalité Runbook Worker hybride d’Azure Automation, qui vous permet d’exécuter des Runbooks sur les machines de votre centre de données local ou de votre fournisseur de cloud.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 861c7ffa647c8d2f37b32c359253ca991eeb314f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457703"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatiser les ressources de votre centre de données ou de votre cloud à l’aide d’un Runbook Worker hybride

Dans Azure Automation, les Runbooks peuvent ne pas avoir accès aux ressources d’autres clouds ou de votre environnement local, car ils s’exécutent dans la plateforme cloud Azure. Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs assignés.

L’image suivante illustre cette fonctionnalité :

![Vue d’ensemble des Runbook Worker hybrides](media/automation-hybrid-runbook-worker/automation.png)

Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent. Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité. Chaque ordinateur peut héberger un seul worker hybride associé à un seul compte Automation.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s’exécute. Chaque worker du groupe interroge Azure Automation pour voir si des travaux sont disponibles. Si un travail est disponible, le premier worker qui le reçoit s’en occupe. Le temps de traitement de la file d’attente des travaux varie selon la charge et le profil matériel du Worker hybride. Vous ne pouvez pas spécifier un worker en particulier. Les Runbook Workers hybrides ne partagent pas la plupart des limites propres aux bacs à sable Azure. Ils n’ont pas les mêmes limites d’espace disque, de mémoire ou de sockets réseau. Les Runbook Workers hybrides ne sont pas limités par les ressources sur le Runbook Worker hybride lui-même. En outre, les Runbook Workers hybrides ne partagent pas la limite de 180 minutes pour la [répartition de charge équilibrée](automation-runbook-execution.md#fair-share) des bacs à sable Azure. Pour en savoir plus sur les limites de service des bacs à sable Azure et des Runbook Workers hybrides, consultez les [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) du travail.

## <a name="install-a-hybrid-runbook-worker"></a>Installer un Runbook Worker hybride

Le processus d’installation d’un Runbook Worker hybride diffère selon le système d’exploitation. Le tableau ci-dessous définit les types de déploiement.

|Système d''exploitation  |Types de déploiement  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuel](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

La méthode d'installation recommandée consiste à utiliser un runbook Automation afin d’automatiser totalement le processus de configuration d’un ordinateur Windows. La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement. Pour une machine Linux, vous exécutez un script Python pour installer l’agent.

> [!NOTE]
> Pour gérer la configuration des serveurs qui prennent en charge le rôle Runbook Worker hybride avec Desired State Configuration (DSC), vous devez ajouter les serveurs en tant que nœuds DSC. Pour plus d’informations sur leur intégration pour les gérer avec DSC, consultez la page [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).
>
>Si vous activez la [solution Update Management](automation-update-management.md), tout ordinateur connecté à votre espace de travail Azure Log Analytics est automatiquement configuré en tant que Runbook Worker hybride pour prendre en charge les runbooks inclus dans cette solution. Toutefois, l’ordinateur n’est inscrit auprès d’aucun des groupes de Workers hybrides déjà définis dans votre compte Automation. L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation, à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

Examinez les [informations de planification de votre réseau](#network-planning) avant de commencer à déployer un Runbook Worker hybride. Une fois que vous avez déployé le worker, consultez [Exécution de runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre à configurer vos runbooks en vue d’automatiser les processus dans votre centre de données local ou autre environnement cloud.

L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation, à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Supprimer un Runbook Worker hybride à partir d’un ordinateur local

Vous pouvez supprimer un Runbook Worker hybride d’un ordinateur local, comme décrit dans cette section pour Windows et Linux.

### <a name="remove-the-worker-on-windows"></a>Supprimer le Worker sur Windows

1. Dans le portail Azure, accédez à votre compte Automation.
2. Sous **Paramètres du compte**, sélectionnez **Clés**, puis notez les valeurs des champs **URL** et **Clé d’accès primaire**.

3. Ouvrez une session PowerShell en mode administrateur et exécutez la commande suivante avec votre URL et les valeurs de clé d'accès principal. Utilisez le paramètre `Verbose` pour afficher un journal détaillé du processus de suppression. Pour supprimer des ordinateurs obsolètes à partir de votre groupe Worker hybride, utilisez le paramètre facultatif `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Supprimer le Worker sur Linux

Vous pouvez utiliser la commande `ls /var/opt/microsoft/omsagent` sur le Runbook Worker hybride pour obtenir l’ID d’espace de travail. Un dossier portant l'ID de l'espace de travail est créé.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ce code ne supprime pas l'agent Log Analytics pour Linux de l'ordinateur. Il supprime uniquement les fonctionnalités et la configuration du rôle Runbook Worker hybride.

## <a name="remove-a-hybrid-worker-group"></a>Supprimer un groupe de Workers hybrides

Pour supprimer un groupe de Runbooks Workers hybrides, vous devez tout d’abord supprimer les Runbooks Workers hybrides de chaque ordinateur membre du groupe. Ensuite, suivez les étapes ci-dessous pour supprimer le groupe :

1. Dans le portail Azure, ouvrez le compte Automation.
2. Sélectionnez **Groupes Worker hybride** sous **Automatisation des processus**. Sélectionnez le groupe à supprimer. La page Propriétés du groupe s’affiche.

   ![Page Propriétés](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Dans la page Propriétés du groupe sélectionné, cliquez sur **Supprimer**. Un message vous invite à confirmer cette action. Si vous êtes sûr de vouloir continuer, sélectionnez **Oui**.

   ![Message de confirmation](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

## <a name="configure-your-network"></a><a name="network-planning"></a>Configurer votre réseau

### <a name="hybrid-worker-role"></a>Worker hybride

Pour que le Runbook Worker hybride se connecte à Azure Automation et y soit inscrit, il doit avoir accès au numéro de port et aux URL décrits dans cette section. Cet accès se situe au-dessus des [ports et URL requis pour que l'agent Log Analytics](../azure-monitor/platform/agent-windows.md) se connecte aux journaux Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Si vous utilisez un serveur proxy pour la communication entre l’agent et le service Azure Automation, assurez-vous que les ressources appropriées sont accessibles. Le délai d’expiration pour les demandes du Runbook Worker hybride et des services Automation est de 30 secondes. La demande échoue après 3 tentatives. Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès. Si vous utilisez la passerelle Log Analytics en tant que proxy, vérifiez qu’elle est configurée pour les workers hybrides. Pour obtenir des instructions à ce sujet, consultez [Configuration de la passerelle Log Analytics pour les Workers hybrides Automation](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Les port et URL suivants sont requis pour que le rôle Runbook Worker hybride communique avec Automation :

* Port : seul le port TCP 443 est nécessaire pour l’accès Internet sortant.
* URL globale : *.azure-automation.net
* URL globale d’US Gov Virginie : *.azure-automation.us
* Service de l’agent : https://\<ID_espace_de_travail\>.agentsvc.azure-automation.net

Il est recommandé d’utiliser les adresses répertoriées lors de la définition des exceptions. Pour les adresses IP, vous pouvez télécharger les [plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ce fichier, qui est mis à jour chaque semaine, possède les plages actuellement déployées et tous les changements à venir des plages d’adresses IP.

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez limiter les communications à ce centre de données régional. Le tableau suivant indique l’enregistrement DNS pour chaque région :

| **Région** | **Enregistrement DNS** |
| --- | --- |
| Centre-USA Ouest | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA Est 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| USA Ouest 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europe Ouest |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europe Nord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asie Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japon Est |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Centre de l’Australie |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australie Est |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Pour obtenir la liste des adresses IP régionales plutôt que celle des noms des régions, téléchargez le fichier XML [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft.

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Le fichier comprend les plages Compute, SQL et Stockage.
>
>Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum.
>
> Pensez à télécharger le nouveau fichier XML chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services en cours d’exécution dans Azure. Les utilisateurs d’Azure ExpressRoute doivent noter que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois.

### <a name="update-management"></a>Update Management

En plus des adresses et ports standard exigés par le Runbook Worker hybride, les adresses suivantes sont obligatoires pour Update Management. La communication avec ces adresses s’effectue par le biais du port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour savoir comment résoudre les problèmes de vos Runbooks Workers hybrides, consultez [Résolution des problèmes relatifs aux Runbooks Workers hybrides](troubleshoot/hybrid-runbook-worker.md#general).