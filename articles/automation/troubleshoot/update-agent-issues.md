---
title: Comprendre les résultats de la vérification de l’agent Windows dans Azure Update Management
description: Découvrez comment résoudre les problèmes rencontrés avec l’agent Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 72fdfe912a5560ce0c0e3886dd3c56cf9534dc22
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480775"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Comprendre les résultats de la vérification de l’agent Windows dans Update Management

De nombreuses raisons peuvent expliquer pourquoi votre machine n’est pas à l’état **Prêt** dans Update Management. Dans la Update Management, vous pouvez vérifier l’intégrité d’un agent worker hybride pour déterminer le problème sous-jacent. Cet article explique comment exécuter l’utilitaire de résolution des problèmes pour les machines Azure du Portail Azure et les machines autres qu’Azure dans le [scénario hors connexion](#troubleshoot-offline).

Voici les trois états de préparation possibles d’une machine :

* **Prête** : l’agent de mise à jour est déployé et a été vu pour la dernière fois il y a moins d’une heure.
* **Déconnectée** : l’agent de mise à jour est déployé et a été vu pour la dernière fois il y a plus d’une heure.
* **Non configurée** : l’agent de mise à jour est introuvable ou n’a pas terminé son intégration.

> [!NOTE]
> L’état actuel de la machine peut s’afficher avec un certain retard sur le portail Azure.

## <a name="start-the-troubleshooter"></a>Démarrer l’utilitaire de résolution des problèmes

Pour les machines Azure, cliquez sur le lien **Résolution des problèmes** sous la colonne **Préparation de l’agent de mise à jour** dans le portail pour ouvrir la page **Résoudre les problèmes de l’Agent de mise à jour**. Pour les machines autres qu’Azure, le lien vous dirige vers cet article. Consultez les [instructions hors connexion](#troubleshoot-offline) pour résoudre les problèmes d’une machine autre qu’Azure.

![Mettre à jour la liste de gestion des machines virtuelles](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Pour vérifier l’intégrité d’un agent, la machine virtuelle doit être en cours d’exécution. Si la machine virtuelle n’est pas en cours d’exécution, un bouton **Démarrer la machine virtuelle** s’affiche.

Dans la page **Résoudre les problèmes de l’agent de mise à jour**, sélectionnez **Exécuter les vérifications** pour démarrer l’utilitaire de résolution des problèmes. L’utilitaire de résolution des problèmes utilise la commande [Run](../../virtual-machines/windows/run-command.md) pour exécuter un script sur la machine et vérifier les dépendances de l’agent. Quand l’utilitaire de résolution des problèmes a terminé, il retourne le résultat des vérifications.

![Page Résoudre les problèmes de l’agent de mise à jour](../media/update-agent-issues/troubleshoot-page.png)

Les résultats sont affichés dans la page quand ils sont prêts. Les sections des vérifications affichent ce qui est inclus dans chaque vérification.

![Résoudre les problèmes de l’agent de mise à jour - vérifications](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Vérifications de la configuration requise

### <a name="operating-system"></a>Système d’exploitation

La vérification du système d’exploitation vérifie si le Runbook Worker hybride exécute l’un de ces systèmes d’exploitation :

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Prend uniquement en charge les évaluations de mises à jour.         |
|Windows Server 2008 R2 SP1 et versions ultérieures |.NET Framework 4.6 ou version ultérieure est requis. ([Télécharger le .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 5.1 est requis.  ([Télécharger Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

La fonctionnalité de vérification de .NET Framework vérifie que [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) ou une version ultérieure est installé sur le système.

### <a name="wmf-51"></a>WMF 5.1

La fonctionnalité de vérification de WMF vérifie que le système dispose de la version requise de Windows Management Framework (WMF), [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Cette vérification détermine si vous utilisez TLS 1.2 pour chiffrer vos communications. TLS 1.0 n’est plus pris en charge par la plateforme. Nous recommandons que les clients utilisent TLS 1.2 pour communiquer avec Update Management.

## <a name="connectivity-checks"></a>Vérifications de la connectivité

### <a name="registration-endpoint"></a>Point de terminaison d’inscription

Cette vérification détermine si l’agent peut correctement communiquer avec le service d’agent.

Les configurations du pare-feu et du proxy doivent permettre à l’agent Runbook Worker hybride de communiquer avec le point de terminaison d’inscription. Pour obtenir la liste des adresses et des ports à ouvrir, consultez [Planification réseau de workers hybrides](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Point de terminaison des opérations

Cette vérification détermine si l’agent peut correctement communiquer avec Job Runtime Data Service.

Les configurations du pare-feu et du proxy doivent permettre à l’agent Runbook Worker hybride de communiquer avec Job Runtime Data Service. Pour obtenir la liste des adresses et des ports à ouvrir, consultez [Planification réseau de workers hybrides](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Vérification de l’intégrité du service de machine virtuelle

### <a name="monitoring-agent-service-status"></a>État du service de Monitoring Agent

Cette vérification détermine si `HealthService` (Microsoft Monitoring Agent) est en cours d’exécution sur l’ordinateur.

Pour en savoir plus sur la résolution des problèmes de ce service, consultez [Microsoft Monitoring Agent n’est pas en cours d’exécution](hybrid-runbook-worker.md#mma-not-running).

Pour réinstaller Microsoft Monitoring Agent, consultez [Installer et configurer Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Événements de service de Monitoring Agent

Cette vérification détermine si des événements `4502` sont apparus dans le journal Azure Operations Manager de la machine au cours des dernières 24 heures.

Pour en savoir plus sur cet événement, consultez le [guide de résolution des problèmes](hybrid-runbook-worker.md#event-4502) pour cet événement.

## <a name="access-permissions-checks"></a>Vérification des autorisations d’accès

### <a name="machinekeys-folder-access"></a>Accès au dossier MachineKeys

La vérification d’accès au dossier Crypto détermine si le compte système Local a accès à C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Résolution des problèmes hors connexion

Vous pouvez utiliser l’utilitaire de résolution des problèmes sur un Runbook Worker hybride hors connexion en exécutant le script localement. Vous pouvez obtenir le script [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) dans PowerShell Gallery. Vous devez avoir installé WMF 4.0 ou version ultérieure pour exécuter le script. Pour télécharger la dernière version de PowerShell, consultez [Installer plusieurs versions de PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

La sortie de ce script ressemble à l’exemple suivant :

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les autres problèmes de vos Runbooks Workers hybrides, consultez [Résolution des problèmes de Runbooks Workers hybrides](hybrid-runbook-worker.md).

