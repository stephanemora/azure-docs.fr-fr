---
title: Solution Update Management dans Azure
description: Cet article vise à vous aider à comprendre comment utiliser la solution Azure Update Management pour gérer les mises à jour de vos ordinateurs Windows et Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0afb8a09fa9780755bcfeef678b76e176f11b348
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246032"
---
# <a name="update-management-solution-in-azure"></a>Solution Update Management dans Azure

Vous pouvez utiliser la solution Update Management dans Azure Automation pour gérer les mises à jour du système d’exploitation de vos ordinateurs Windows et Linux déployés dans Azure, des environnements locaux ou d’autres fournisseurs cloud. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur tous les ordinateurs d’agent et gérer le processus d’installation des mises à jour requises pour les serveurs.

Vous pouvez activer Update Management pour les machines virtuelles directement depuis votre compte Azure Automation. Pour découvrir comment activer Update Management pour les machines virtuelles depuis votre compte Automation, consultez l’article [Gérer les mises à jour pour plusieurs machines virtuelles](manage-update-multi.md). Vous pouvez également activer Update Management pour une machine virtuelle à partir de sa page dans le Portail Azure. Ce scénario est disponible pour les machines virtuelles [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) et [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management).

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Les ordinateurs gérés par Update Management utilisent les configurations suivantes pour effectuer l’évaluation et les déploiements de mises à jour :

* Microsoft Monitoring Agent (MMA) pour Windows ou Linux
* PowerShell DSC (Desired State Configuration, configuration d’état souhaité) pour Linux
* Runbook Worker hybride Automation
* Services Microsoft Update ou Windows Server Update (WSUS) pour ordinateurs Windows

Le schéma suivant présente une vue conceptuelle du comportement et du flux de données liés à l’évaluation des mises à jour de sécurité par la solution et à leur application à tous les ordinateurs Windows Server et Linux connectés dans un espace de travail :

![Flux du processus Update Management](media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management peut être utilisé pour intégrer des machines en mode natif dans plusieurs abonnements du même locataire.

Après la publication d’un CVE, comptez un délai de deux à trois heures avant l’affichage du correctif pour l’évaluation sur des machines Linux.  Sur les machines Windows, ce délai est de 12 à 15 heures.

Après qu’une machine a terminé l’analyse de conformité de la mise à jour, l’agent transfère les informations en bloc à Azure Log Analytics. Sur une machine Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut.

En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes si MMA est redémarré, avant et après l’installation de la mise à jour.

Sur un ordinateur Linux, l’analyse de conformité est effectuée toutes les 3 heures par défaut. Si l’agent MMA est redémarré, une analyse de conformité est lancée dans les 15 minutes.

La solution rapporte l’état de mise à jour de l’ordinateur en fonction de la source avec laquelle vous avez configuré la synchronisation. Si l’ordinateur Windows est configuré pour rapporter à WSUS, en fonction de la date de dernière synchronisation de WSUS avec Microsoft Update, les résultats peuvent être différents de ce que Microsoft Updates indique. Le comportement est le même pour les machines Linux qui sont configurées pour rapporter à un référentiel local et non pas à un référentiel public.

> [!NOTE]
> Pour pouvoir rapporter au service, Update Management nécessite certaines URL et l’activation de ports. Pour en savoir plus sur la configuration requise, consultez la section [Planification du réseau pour les Workers hybrides](automation-hybrid-runbook-worker.md#network-planning).

Vous pouvez déployer et installer des mises à jour logicielles sur des ordinateurs qui nécessitent les mises à jour en créant un déploiement planifié. Les mises à jour considérées comme *facultatives* ne sont pas incluses dans le déploiement des ordinateurs Windows. Seules les mises à jour nécessaires sont incluses dans le déploiement. 

Le déploiement planifié définit les ordinateurs cibles qui reçoivent les mises à jour applicables, soit en spécifiant explicitement les ordinateurs ou en sélectionnant un [groupe d’ordinateurs](../azure-monitor/platform/computer-groups.md) d’après des recherches dans les journaux d’un ensemble spécifique d’ordinateurs. Vous spécifiez également une planification pour approuver et définir la période pendant laquelle les mises à jour peuvent être installées.

Les mises à jour sont installées par des Runbooks dans Azure Automation. Vous ne pouvez pas visualiser ces Runbooks, qui ne nécessitent aucune configuration. Lorsqu’un déploiement de mises à jour est créé, il génère une planification qui démarre un Runbook de mises à jour principal au moment indiqué pour les ordinateurs inclus. Ce runbook principal lance un runbook enfant sur chaque agent pour installer les mises à jour obligatoires.

À la date et l’heure spécifiées dans le déploiement de mises à jour, les ordinateurs cibles exécutent le déploiement en parallèle. Avant l’installation, une analyse est lancée pour vérifier que les mises à jour sont encore requises. Pour les ordinateurs clients WSUS, si les mises à jour ne sont pas approuvées dans WSUS, leur déploiement échoue.

L’inscription d’une machine auprès du service Update Management dans plusieurs espaces de travail Log Analytics (multihébergement) n’est pas prise en charge.

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Types de clients pris en charge

Le tableau suivant répertorie la liste des systèmes d’exploitation pris en charge :

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Prend uniquement en charge les évaluations de mises à jour.         |
|Windows Server 2008 R2 SP1 et versions ultérieures (y compris Windows Server 2012 et 2016)    |.NET Framework 4.5.1 ou version ultérieure est requis. ([Télécharger .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 ou une version ultérieure est nécessaire. ([Télécharger WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 est recommandé pour accroître la fiabilité.  ([Télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) et 7 (x64)      | Les agents Linux doivent avoir accès à un référentiel de mise à jour. La mise à jour corrective basée sur la classification nécessite que 'yum' retourne les données de sécurité que CentOS n’a pas directement.         |
|Red Hat Enterprise 6 (x86/x64) et 7 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 (x86/x64)      |Les agents Linux doivent avoir accès à un référentiel de mise à jour.         |

### <a name="unsupported-client-types"></a>Types de clients non pris en charge

Le tableau suivant répertorie les systèmes d’exploitation qui ne sont pas pris en charge :

|Système d’exploitation  |Notes  |
|---------|---------|
|Client Windows     | Les systèmes d’exploitation client (par exemple, Windows 7 et Windows 10) ne sont pas pris en charge.        |
|Windows Server 2016 Nano Server     | Non pris en charge.       |

### <a name="client-requirements"></a>Configuration requise des clients

#### <a name="windows"></a> Windows

Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS ou avoir accès à Microsoft Update. Vous pouvez utiliser Update Management avec System Center Configuration Manager. Pour en savoir plus sur les scénarios d’intégration, consultez la section [Intégrer System Center Configuration Manager à Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). L’[agent Windows](../azure-monitor/platform/agent-windows.md) est obligatoire. L’agent est automatiquement installé si vous intégrez une machine virtuelle Azure.

#### <a name="linux"></a>Linux

Pour Linux, la machine doit avoir accès à un référentiel de mises à jour, qui peut être privé ou public. TLS 1.1 ou TLS 1.2 est exigé pour interagir avec Update Management. La configuration de Log Analytics Agent pour Linux afin d’envoyer des rapports à plusieurs espaces de travail Log Analytics n’est pas prise en charge avec cette solution.

Pour savoir comment installer Log Analytics Agent pour Linux et comment télécharger la dernière version, consultez [Agent Operations Management Suite pour Linux](https://github.com/microsoft/oms-agent-for-linux). Pour savoir comment installer Log Analytics Agent pour Windows et comment télécharger la dernière version, consultez [Agent Operations Management Suite pour Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Autorisations

Pour créer et gérer des déploiements de mises à jour, vous devez disposer d’autorisations spécifiques. Pour en savoir plus sur ces autorisations, consultez [Accès en fonction du rôle - Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Composants de la solution

La solution se compose des éléments suivants. Les ressources sont ajoutées à votre compte Automation. Il s’agit soit d’agents connectés directement ou se trouvant dans un groupe d'administration connecté à Operations Manager.

### <a name="hybrid-worker-groups"></a>Groupes de Workers hybrides

Après avoir activé cette solution, tout ordinateur Windows directement connecté à votre espace de travail Log Analytics est automatiquement configuré comme un Runbook Worker hybride afin de prendre en charge les Runbooks inclus dans cette solution.

Chaque ordinateur Windows géré par la solution est répertorié dans le panneau **Groupes de Workers hybrides** en tant que **Groupe de Workers hybrides système** du compte Automation. Les solutions utilisent la convention d’affectation de noms *Hostname FQDN_GUID*. Vous ne pouvez pas cibler ces groupes avec des Runbooks dans votre compte. Si vous essayez, l’opération échouera. Ces groupes sont destinés à prendre en charge uniquement la solution de gestion.

Vous pouvez ajouter les ordinateurs Windows à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du Runbook Worker hybride.

### <a name="management-packs"></a>Packs d’administration

Si votre groupe d’administration System Center Operations Manager est connecté à un espace de travail Log Analytics, les packs d’administration suivants sont installés dans Operations Manager. Ces packs d’administration sont également installés sur des ordinateurs Windows directement connectés après l’ajout de la solution. Il n’est pas nécessaire de les configurer ou de les gérer.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pack d’administration du déploiement des mises à jour

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Pour les systèmes dotés de l’agent Operations Manager, pour pouvoir être entièrement gérés par Update Management, l’agent doit être mis à jour vers Microsoft Monitoring Agent. Pour savoir comment mettre à jour l’agent, consultez [Guide pratique pour mettre à niveau un agent Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents).

## <a name="onboard"></a>Activer Update Management

Pour commencer à appliquer des correctifs aux systèmes, vous devez activer la solution Update Management. Vous pouvez intégrer des machines à Update Management de différentes manières. Voici des méthodes recommandées et prises en charge pour intégrer la solution :

* [Depuis une machine virtuelle](automation-onboard-solutions-from-vm.md)
* [Depuis plusieurs machines](automation-onboard-solutions-from-browse.md)
* [Depuis votre compte Automation](automation-onboard-solutions-from-automation-account.md)
* [Avec un runbook Azure Automation](automation-onboard-solutions.md)
  
### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Vérifier que les ordinateurs non-Azure sont intégrés

Pour confirmer que les ordinateurs directement connectés communiquent avec Log Analytics, vous pouvez exécuter l’une des recherches suivantes dans les journaux au bout de quelques minutes.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a> Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Sur un ordinateur Windows, vous pouvez vérifier les informations suivantes pour tester la connectivité de l’agent avec Log Analytics :

1. Dans le Panneau de configuration, ouvrez **Microsoft Monitoring Agent**. Sous l’onglet **Azure Log Analytics**, l’agent affiche le message suivant : **Microsoft Monitoring Agent s’est correctement connecté à Log Analytics.**
2. Ouvrez le journal des événements Windows. Accédez à **Application and Services Logs\Operations Manager**, puis recherchez l’ID d’événement 3000 et 5002 à partir du **connecteur de service** source. Ces événements indiquent que l’ordinateur est enregistré sur l’espace de travail Log Analytics et qu’il reçoit la configuration.

Si l’agent ne parvient pas à communiquer avec Log Analytics et s’il est configuré pour communiquer avec Internet par le biais d’un pare-feu ou d’un serveur proxy, vérifiez que le pare-feu ou le serveur proxy est correctement configuré. Pour savoir comment vérifier la configuration du pare-feu ou du serveur proxy, consultez [Configuration réseau de l’agent Windows](../azure-monitor/platform/agent-windows.md) ou [Configuration réseau de l’agent Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Si vos systèmes Linux sont configurés pour communiquer avec un proxy ou Log Analytics Gateway et que vous intégrez cette solution, vous devez mettre à jour les autorisations *proxy.conf* pour accorder au groupe omiuser une autorisation d’accès en lecture sur le fichier. Pour cela, exécutez les commandes suivantes :
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Les nouveaux agents Linux ajoutés affichent l’état **Mis à jour** après l’exécution d’une évaluation. Ce processus peut prendre jusqu’à 6 heures.

Pour vérifier qu’un groupe d’administration Operations Manager communique avec Log Analytics, consultez la rubrique [Validation de l’intégration entre Operations Manager et Log Analytics](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Collecte des données

### <a name="supported-agents"></a>Agents pris en charge

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution :

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| Agents Windows |OUI |La solution collecte des informations sur les mises à jour système auprès des agents Windows et lance l’installation des mises à jour obligatoires. |
| Agents Linux |OUI |La solution collecte des informations sur les mises à jour système auprès des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |OUI |La solution collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté.<br/>Une connexion directe entre l’agent Operations Manager et Log Analytics n’est pas obligatoire. Les données sont transférées du groupe d’administration à l’espace de travail Log Analytics. |

### <a name="collection-frequency"></a>Fréquence de collecte

Une analyse est effectuée deux fois par jour sur chaque ordinateur Windows géré. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé. Si l’état a changé, une analyse de conformité est lancée.

Une analyse est effectuée toutes les 3 heures sur chaque ordinateur Linux géré.

L’affichage sur le tableau de bord des données mises à jour provenant des ordinateurs gérés peut prendre entre 30 minutes et 6 heures.

## <a name="viewing-update-assessments"></a>Afficher les évaluations des mises à jour

Dans votre compte Automation, cliquez sur **Update Management** pour afficher l’état de vos ordinateurs.

Cet affichage fournit des informations sur vos ordinateurs, les mises à jour manquantes, les déploiements de mises à jour et les déploiements de mises à jour planifiés. Dans la **COLONNE CONFORMITÉ**, vous pouvez voir à quel moment l’ordinateur a été évalué pour la dernière fois. Dans la colonne **METTRE À JOUR LA DISPONIBILITÉ DE L’AGENT**, vous pouvez voir l’intégrité de l’agent de mise à jour. En cas de problème, sélectionnez le lien pour accéder à la documentation de résolution des problèmes qui vous expliquera les étapes à suivre pour corriger le problème.

Pour exécuter une recherche dans les journaux qui permet de retourner des informations sur l’ordinateur, la mise à jour ou le déploiement, sélectionnez l’élément dans la liste. Le volet **Recherche dans les journaux** s’ouvre avec une requête sur l’élément sélectionné :

![Vue par défaut de Update Management](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Installer les mises à jour

Une fois les mises à jour évaluées pour tous les ordinateurs Linux et Windows dans votre espace de travail, vous pouvez installer les mises à jour obligatoires en créant une opération de *déploiement de mises à jour*. Un déploiement de mises à jour est une installation planifiée de mises à jour obligatoires pour un ou plusieurs ordinateurs. Vous pouvez spécifier la date et l’heure du déploiement ainsi qu’un ordinateur ou groupe d’ordinateurs à inclure dans un déploiement. Pour en savoir plus sur les groupes d’ordinateurs, consultez [Groupes d’ordinateurs dans Log Analytics](../azure-monitor/platform/computer-groups.md).

 Lorsque vous incluez des groupes d’ordinateurs dans votre déploiement de mises à jour, l’appartenance au groupe n’est évaluée qu’une seule fois au moment de la création de la planification. Les modifications ultérieures apportées à un groupe ne sont pas répercutées. Pour contourner ce problème, utilisez des [groupes dynamiques](#using-dynamic-groups), qui sont résolus au moment du déploiement et sont définis par une requête.

> [!NOTE]
> Les machines virtuelles Windows déployées à partir de Place de marché Microsoft Azure sont configurées par défaut pour recevoir des mises à jour automatiques de Windows Update Service. Ce comportement ne change pas lorsque vous ajoutez cette solution ou des machines virtuelles Windows à votre espace de travail. Si vous n’avez pas géré activement les mises à jour avec cette solution, le comportement par défaut (appliquer automatiquement les mises à jour) s’applique.

Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations sur la configuration du package, consultez la rubrique [Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure [RHUI (Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) déployée dans Azure. Toute autre distribution Linux doit être mise à jour à partir du référentiel de fichiers de distribution en ligne en tenant compte de leurs méthodes de distribution prises en charge.

Pour créer un déploiement de mises à jour, sélectionnez **Planifier le déploiement de la mise à jour**. Le volet **Nouveau déploiement de mises à jour** s’ouvre. Entrez les valeurs des propriétés décrites dans le tableau suivant, puis cliquez sur **Créer** :

| Propriété | Description |
| --- | --- |
| Nom |Nom unique identifiant le déploiement de mises à jour. |
|Système d’exploitation| Linux ou Windows|
| Groupes à mettre à jour (préversion)|Définissez une requête basée sur une combinaison de l’abonnement, des groupes de ressources, des emplacements et des étiquettes pour créer un groupe dynamique de machines virtuelles Azure à inclure dans votre déploiement. Pour plus d’informations, consultez [Groupes dynamiques](automation-update-management.md#using-dynamic-groups)|
| Ordinateurs à mettre à jour |Sélectionnez une recherche enregistrée, un groupe importé ou choisissez un ordinateur dans la liste déroulante, puis sélectionnez des ordinateurs individuels. Si vous choisissez **Machines**, l’état de préparation de la machine est indiqué dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT**.</br> Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans Log Analytics, consultez [Groupes d’ordinateurs dans Log Analytics](../azure-monitor/platform/computer-groups.md) |
|Classifications des mises à jour|Sélectionnez toutes les classifications des mises à jour dont vous avez besoin.|
|Inclure/exclure des mises à jour|La page **Inclure/Exclure** s’ouvre. Les mises à jour à inclure ou à exclure sont sous des onglets distincts. Pour plus d’informations sur la façon dont l’inclusion est gérée, consultez [Comportement d’inclusion](automation-update-management.md#inclusion-behavior) |
|Paramètres de planification|Sélectionnez l’heure de début, puis la périodicité.|
| Préscripts + postscripts|Sélectionnez les scripts à exécuter avant et après votre déploiement|
| Fenêtre de maintenance |Nombre de minutes défini pour les mises à jour. La valeur ne peut pas être inférieure à 30 minutes ni supérieure à 6 heures |
| Contrôle du redémarrage| Détermine la façon dont doivent être gérés les redémarrages. Options disponibles :</br>Redémarrer si nécessaire (par défaut)</br>Toujours redémarrer</br>Ne jamais redémarrer</br>Redémarrer uniquement : les mises à jour ne sont pas installées|

Vous pouvez également créer des déploiements de mises à jour par programmation. Pour savoir comment créer un déploiement de mises à jour avec l’API REST, consultez [Configurations des mises à jour logicielles - Créer](/rest/api/automation/softwareupdateconfigurations/create). Vous pouvez également utiliser un exemple de runbook fourni pour créer un déploiement de mises à jour hebdomadaires. Pour en savoir plus sur ce runbook, consultez [Créer un déploiement de mises à jour hebdomadaires pour une ou plusieurs machines virtuelles dans un groupe de ressources](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="multi-tenant"></a>Déploiements de mise à jour inter-tenants

Si des machines se trouvent dans un autre rapport de tenant Azure pour Update Management que vous devez corriger, vous devez utiliser la solution de contournement suivante pour planifier l’opération. Vous pouvez utiliser la cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule?view=azurermps-6.13.0) avec le commutateur `-ForUpdate` pour créer une planification, et utiliser la cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration?view=azurermps-6.13.0
) et faire passer les machines de l’autre tenant au paramètre `-NonAzureComputer`. L’exemple suivant vous montre comment procéder :

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Afficher les mises à jour manquantes

Sélectionnez **Mises à jour manquantes** pour afficher la liste des mises à jour qui manquent à vos ordinateurs. Chaque mise à jour est répertoriée et peut être sélectionnée. Les informations sur le nombre d’ordinateurs qui nécessitent la mise à jour, le système d’exploitation et le lien vers plus d’informations s’affichent. Le volet **Recherche dans les journaux** affiche plus d’informations sur les mises à jour.

## <a name="view-update-deployments"></a>Afficher les déploiements de mises à jour

Sélectionnez l’onglet **Déploiements de mises à jour** pour afficher la liste des déploiements de mises à jour existants. Sélectionnez l’un des déploiements de mises à jour dans le tableau pour ouvrir le volet **Exécution du déploiement des mises à jour** de ce déploiement de mises à jour.

![Vue d’ensemble des résultats d’un déploiement de mises à jour](./media/automation-update-management/update-deployment-run.png)

Pour afficher un déploiement de mises à jour à partir de l’API REST, consultez [Exécutions de configurations de mises à jour logicielles](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Classifications des mises à jour

Les tableaux suivants répertorient les classifications des mises à jour dans Update Management, avec une définition de chaque classification.

### <a name="windows"></a> Windows

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques     | Mise à jour d’un problème spécifique qui concerne un bogue critique non lié à la sécurité.        |
|Mises à jour de sécurité     | Mise à jour pour un problème de sécurité propre à un produit.        |
|Correctifs cumulatifs     | Cumul de correctifs logiciels regroupés pour faciliter leur déploiement.        |
|Packs de fonctionnalités     | Nouvelles fonctionnalités de produit distribuées en dehors d’une version de produit.        |
|Service Packs     | Cumul de correctifs logiciels appliqués à une application.        |
|Mises à jour de définitions     | Mise à jour de fichiers de virus ou d’autres définitions.        |
|Outils     | Utilitaire ou fonctionnalité permettant d’effectuer une ou plusieurs tâches.        |
|Mises à jour     | Mise à jour d’une application ou d’un fichier actuellement installé.        |

### <a name="linux"></a>Linux

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques et de sécurité     | Mises à jour pour un problème spécifique ou un problème de sécurité propre à un produit.         |
|Autres mises à jour     | Toutes les autres mises à jour qui ne sont pas critiques par nature ni des mises à jour de sécurité.        |

Pour Linux, Update Management peut faire la distinction entre les mises à jour de sécurité et critiques dans le cloud tout en affichant les données d’évaluation en raison de l’enrichissement des données dans le cloud. Pour la mise à jour corrective, Update Management s’appuie sur les données de classification disponibles sur l’ordinateur. Contrairement à d’autres distributions, CentOS n’a pas directement accès à ces informations. Si vous disposez d’ordinateurs CentOS configurés de manière à retourner les données de sécurité pour la commande suivante, Update Management va pouvoir appliquer la mise à jour corrective en fonction des classifications.

```bash
sudo yum -q --security check-update
```

Il n’existe actuellement aucune méthode prise en charge permettant d’activer la disponibilité des données de classification natives sur CentOS. Pour le moment, seule une prise en charge au mieux est proposée aux clients l’ayant activé eux-mêmes.

## <a name="firstparty-predownload"></a>Mise à jour corrective interne et prétéléchargement

Update Management s’appuie sur Windows Update pour télécharger et installer les mises à jour de Windows. Par conséquent, nous respectons la plupart des paramètres utilisés par Windows Update. Si vous utilisez des paramètres pour activer les mises à jour non-Windows, Update Management gérera également ces mises à jour. Si vous souhaitez activer le téléchargement des mises à jour avant le déploiement de mises à jour, le déploiement des mise à jour peut être plus rapide et être moins susceptible de dépasser la fenêtre de maintenance.

### <a name="pre-download-updates"></a>Prétélécharger les mises à jour

Pour configurer automatiquement le téléchargement des mises à jour dans la stratégie de groupe, vous pouvez définir le paramètre [Configurer les mises à jour automatiques](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#BKMK_comp5) sur **3**. Cela télécharge les mises à jour nécessaires en arrière-plan, mais ne les installe pas. Cela permet à Update Management de garder le contrôle de la planification, mais aussi d’autoriser les mises à jour à être téléchargées en dehors de la fenêtre de maintenance d’Update Management. Ceci peut empêcher les erreurs de **fenêtre de maintenance dépassée** dans la Update Management.

Vous pouvez également définir ce paramètre avec PowerShell. Exécutez la commande PowerShell suivante sur le système sur lequel vous souhaitez activer le téléchargement automatique des mises à jour.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="enable-updates-for-other-microsoft-products"></a>Activer les mises à jour pour d’autres produits Microsoft

Par défaut, Windows Update fournit uniquement des mises à jour pour Windows. Si vous activez l’option **Me communiquer les mises à jour d’autres produits Microsoft lorsque je mets à jour Windows**, les mises à jour des autres produits vous sont fournies, y compris les correctifs de sécurité pour SQL Server ou d’autres logiciels. Cette option ne peut pas être configurée par la stratégie de groupe. Exécutez la commande PowerShell suivante sur les systèmes sur lesquels vous souhaitez activer d’autres correctifs tiers. Update Management appliquera ce paramètre.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a> Correctifs tiers sur Windows

Update Management s’appuie sur WSUS ou Windows Update pour corriger les systèmes Windows pris en charge. Des outils tels que l’[éditeur de mise à jour System Center ](/sccm/sum/tools/updates-publisher
) (Éditeur de mise à jour) vous permettent de publier des mises à jour personnalisées dans WSUS. Ce scénario permet à Update Management de corriger les machines qui utilisent WSUS comme référentiel de mise à jour avec des logiciels tiers. Pour savoir comment configurer l’éditeur de mise à jour, consultez [Installer l’éditeur de mise à jour](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Planification réseau

Les adresses suivantes sont exigées particulièrement pour Update Management. La communication avec ces adresses s’effectue par le biais du port 443.

|Azure (public)  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|* .azure-automation.net|*.azure-automation.us|

Pour plus d’informations sur les ports exigés par le Runbook Worker hybride, consultez [Ports du rôle de Worker hybride](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Il est recommandé d’utiliser les adresses répertoriées lors de la définition des exceptions. Pour les adresses IP, vous pouvez télécharger les [Plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ce fichier, qui est mis à jour chaque semaine, reflète les plages actuellement déployées et tous les changements à venir des plages d’adresses IP.

## <a name="search-logs"></a>Rechercher dans les journaux

En plus des détails fournis dans le portail Azure, vous pouvez effectuer des recherches dans les journaux. Dans les pages de solutions, sélectionnez **Log Analytics**. Le volet **Recherche dans les journaux** s’ouvre.

Vous pouvez également apprendre à personnaliser les requêtes ou les utiliser à partir de différents clients et plus en consultant :  [Documentation de l’API de recherche Log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Exemples de requêtes

Les sections suivantes fournissent des exemples de requêtes de journal pour les enregistrements de mise à jour qui sont collectés par cette solution :

#### <a name="single-azure-vm-assessment-queries-windows"></a>Requêtes d’évaluation de la machine virtuelle Azure unique (Windows)

Remplacez la valeur VMUUID par le GUID VM de la machine virtuelle que vous interrogez. Pour trouver le VMUUID à utiliser, exécutez la requête suivante dans Log Analytics : `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Récapitulatif des mises à jour manquantes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Liste des mises à jour manquantes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Requêtes d’évaluation de la machine virtuelle Azure unique (Linux)

Pour certaines versions de Linux, il existe une incompatibilité de [mode Endian](https://en.wikipedia.org/wiki/Endianness) entre la valeur VMUUID qui provient d’Azure Resource Manager, et ce qui est stocké dans Log Analytics. La requête suivante recherche une correspondance sur l’un des modes Endian. Remplacez les valeurs VMUUID avec le format big-endian et little-endian du GUID afin de retourner correctement les résultats. Pour trouver le VMUUID à utiliser, exécutez la requête suivante dans Log Analytics : `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Récapitulatif des mises à jour manquantes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Liste des mises à jour manquantes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Requêtes d’évaluation sur plusieurs VM

##### <a name="computers-summary"></a>Récapitulatif des ordinateurs

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Récapitulatif des mises à jour manquantes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Liste des ordinateurs

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Liste des mises à jour manquantes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Utilisation de groupes dynamiques (préversion)

Update Management permet de cibler un groupe dynamique de machines virtuelles Azure pour les déploiements de mises à jour. Ces groupes sont définis par une requête et, au démarrage d’un déploiement de mise à jour, les membres de ce groupe sont évalués. Quand vous définissez votre requête, les éléments suivants peuvent être utilisés ensemble pour remplir le groupe dynamique

* Abonnement
* Groupes de ressources
* Emplacements
* Balises

![Sélection de groupes](./media/automation-update-management/select-groups.png)

Pour prévisualiser les résultats d’un groupe dynamique, cliquez sur le bouton **Aperçu**. Cet aperçu montre l’appartenance au groupe à ce moment-là. Dans cet exemple, nous recherchons les machines dont la balise **Role** (Rôle) est définie à **BackendServer**. Si plusieurs machines ont cette balise, elles seront ajoutées à tous les déploiements ultérieurs effectués pour ce groupe.

![aperçu des groupes](./media/automation-update-management/preview-groups.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Intégrer avec System Center Configuration Manager

Les clients qui ont investi dans System Center Configuration Manager pour gérer des PC, serveurs et appareils mobiles s’appuient aussi sur la puissance et la maturité de Configuration Manager pour les aider à gérer les mises à jour logicielles. Configuration Manager fait partie de leur cycle de gestion des mises à jour logicielles (SUM).

Pour découvrir comment intégrer la solution de gestion à System Center Configuration Manager, consultez l’article [Intégrer System Center Configuration Manager à Update Management](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Comportement d’inclusion

L’inclusion de mise à jour vous permet de spécifier des mises à jour spécifiques à appliquer. Tous les correctifs ou packages qui sont inclus sont installés. Quand des correctifs ou des packages sont inclus et qu’une classification est également sélectionnée, les éléments inclus ainsi que les éléments qui remplissent les critères de la classification sont installés.

Il est important de se souvenir que les exclusions sont prioritaires sur les inclusions. Par exemple, si vous définissez une règle d’exclusion de `*`, aucun correctif ou package n’est installé puisque cette règle les exclut tous. Les correctifs exclus sont toujours affichés comme étant manquants sur l’ordinateur. Sur les machines Linux, si un package est inclus, mais qu’il a un package dépendant exclu, le package n’est pas installé.

## <a name="patch-linux-machines"></a>Appliquer une mise à jour corrective aux ordinateurs Linux

Les sections suivantes décrivent les problèmes potentiels liés à la mise à jour corrective Linux.

### <a name="unexpected-os-level-upgrades"></a>Mises à niveau inattendues au niveau du système d’exploitation

Sur certaines variantes Linux, comme Red Hat Enterprise Linux, les mises à niveau du système d’exploitation peuvent se produire par le biais de packages. Celles-ci peuvent alors entraîner des exécutions Update Management au cours desquelles le numéro de version du système d’exploitation change. Étant donné que la solution Update Management utilise les mêmes méthodes pour mettre à jour des packages qu’un administrateur sur l’ordinateur Linux local, ce comportement est volontaire.

Pour éviter de mettre à jour la version du système d’exploitation par le biais des exécutions Update Management, vous utilisez la fonctionnalité **Exclusion**.

Dans Red Hat Enterprise Linux, le nom du package à exclure est : redhat-release-server.x86_64.

![Packages à exclure pour Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Les correctifs de sécurité/critiques ne sont pas appliqués

Lorsque vous déployez des mises à jour sur un ordinateur Linux, vous pouvez sélectionner des classifications. Vous pouvez ainsi filtrer les mises à jour afin d’appliquer à la machine uniquement celles qui remplissent les critères de classification spécifiés. Ce filtre est appliqué localement sur l’ordinateur lorsque la mise à jour est déployée.

Comme Update Management enrichit les mises à jour dans le cloud, certaines mises à jour peuvent être signalées dans Update Management comme ayant un impact sur la sécurité quand bien même l’ordinateur local n’a pas ces informations. Ainsi, si vous appliquez des mises à jour critiques à un ordinateur Linux, certaines mises à jour, non signalées comme ayant un impact sur la sécurité pour cet ordinateur, peuvent ne pas être appliquées.

Toutefois, Update Management peut quand même signaler que cet ordinateur n’est pas conforme car il contient des informations supplémentaires sur la mise à jour concernée.

Le déploiement de mises à jour par classification ne fonctionne pas directement sur CentOS. Pour déployer correctement les mises à jour pour CentOS, sélectionnez toutes les classifications pour garantir que les mises à jour sont appliquées. Pour SUSE, sélectionner *uniquement* « Autres mises à jour » en tant que classification peut entraîner l’installation de certaines mises à jour de sécurité si les mises à jour de sécurité associées à zypper (gestionnaire de package) ou ses dépendances sont requises en premier. Il s’agit d’une limitation de zypper. Dans certains cas, vous devrez peut-être réexécuter le déploiement des mises à jour. Pour savoir si cela est nécessaire, consultez le journal des mises à jour.

## <a name="remove-a-vm-for-update-management"></a>Supprimer une machine virtuelle pour Update Management

Pour supprimer une machine virtuelle de Update Management :

* Dans votre espace de travail Log Analytics, supprimez la machine virtuelle de la rechercher enregistrée pour la configuration d’étendue `MicrosoftDefaultScopeConfig-Updates`. Les recherches enregistrées se trouvent sous la section **Général** de votre espace de travail.
* Supprimez l’agent [Microsoft Monitoring agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou l’[agent Log Analytics pour Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).
  
## <a name="troubleshoot"></a>Résolution des problèmes

Pour savoir comment résoudre les problèmes de Update Management, consultez [Résolution des problèmes de Update Management](troubleshoot/update-management.md).

## <a name="next-steps"></a>Étapes suivantes

Poursuivez avec le didacticiel pour apprendre à gérer les mises à jour de vos machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure](automation-tutorial-update-management.md)

* Utilisez les recherches de journaux de [Log Analytics](../log-analytics/log-analytics-log-searches.md) pour afficher des données détaillées sur les mises à jour.
* [Créez des alertes](automation-tutorial-update-management.md#configure-alerts) pour connaître l’état de déploiement des mises à jour.

* Pour savoir comment utiliser Update Management avec l’API REST, consultez [Configurations des mises à jour logicielles](/rest/api/automation/softwareupdateconfigurations)

