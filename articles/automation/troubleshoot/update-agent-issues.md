---
title: Comprendre les résultats de la vérification de l’agent dans Azure Update Management
description: Découvrez comment résoudre les problèmes rencontrés avec l’agent Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 20323afe79ad3de1e3dfccd4752c4f7e28d22266
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095369"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Comprendre les résultats de la vérification de l’agent dans Update Management

Il peut y avoir de nombreuses raisons pour lesquelles votre machine non-Azure ne s’affiche pas comme étant **prête** dans Update Management. Dans la Update Management, vous pouvez vérifier l’intégrité d’un agent worker hybride pour déterminer le problème sous-jacent. Cet article explique comment exécuter l’utilitaire de résolution des problèmes à partir du portail Azure et dans les scénarios hors connexion.

## <a name="start-the-troubleshooter"></a>Démarrer l’utilitaire de résolution des problèmes

Cliquez sur le lien **Résolution des problèmes** sous la colonne **Préparation de l’agent de mise à jour** dans le portail pour ouvrir la page **Agent de résolution des problèmes de mise à jour**. Cette page vous présente les problèmes de l’agent et contient un lien vers cet article pour vous aider à résoudre ces problèmes.

![vm list page](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Les vérifications nécessitent que la machine virtuelle soit en cours d’exécution. Si la machine virtuelle n’est pas en cours d’exécution, vous voyez un bouton pour **démarrer la machine virtuelle**.

Dans la page **Agent de résolution des problèmes de mise à jour**, cliquez sur **Exécuter les vérifications**, pour démarrer l’utilitaire de résolution des problèmes. L’utilitaire de résolution des problèmes utilise la résolution la commande [Run](../../virtual-machines/windows/run-command.md) pour exécuter un script sur la machine et vérifier les dépendances de l’agent. Lorsque l’utilitaire de résolution des problèmes a terminé, il retourne le résultat des vérifications.

![Page de résolution des problèmes](../media/update-agent-issues/troubleshoot-page.png)

Lorsque l’inspection est terminée, les résultats sont renvoyés. Les sections [vérifié](#pre-requisistes-checks) fournissent des informations sur ce que chaque vérification recherche.

![Page de vérifications de l’agent de mise à jour](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Vérifications de la configuration requise

### <a name="operating-system"></a>Système d’exploitation

La vérification du système d’exploitation vérifie si le Runbook Worker hybride exécute l’un des systèmes d’exploitation suivants :

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Prend uniquement en charge les évaluations de mises à jour.         |
|Windows Server 2008 R2 SP1 et versions ultérieures     |.NET Framework 4.5.1 ou version ultérieure est requis. ([Télécharger .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 ou une version ultérieure est nécessaire. ([Télécharger WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 est recommandé pour accroître la fiabilité.  ([Télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) et 7 (x64)      | Les agents Linux doivent avoir accès à un référentiel de mise à jour. La mise à jour corrective basée sur la classification nécessite que 'yum' renvoie les données de sécurité que CentOS n’a pas directement.         |
|Red Hat Enterprise 6 (x86/x64) et 7 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 LTS (x86/x64)      |Les agents Linux doivent avoir accès à un référentiel de mise à jour.         |

### <a name="net-451"></a>.NET 4.5.1

La vérification .NET Framework contrôle si le système dispose d’au moins [.NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653).

### <a name="wmf-51"></a>WMF 5.1

La vérification WMF vérifie si le système dispose de la version requise de Windows Management Framework. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) est la version la plus basse prise en charge. Il est recommandé d’installer [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) pour renforcer la fiabilité du Runbook Worker hybride.

### <a name="tls-12"></a>TLS 1.2

Cette vérification détermine si vous utilisez TLS 1.2 pour chiffrer vos communications. TLS 1.0 n’est plus pris en charge par la plateforme. Il est donc recommandé que les clients utilisent TLS 1.2 pour communiquer avec Update Management.

## <a name="connectivity-checks"></a>Vérifications de la connectivité

### <a name="registration-endpoint"></a>Point de terminaison d’inscription

Cette vérification détermine si l’agent peut correctement communiquer avec le service d’agent.

Les configurations du pare-feu et du proxy doivent permettre à l’agent Runbook Worker hybride de communiquer avec le point de terminaison d’inscription. Pour obtenir la liste des adresses et des ports à ouvrir, consultez [Planification réseau de workers hybrides](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Point de terminaison des opérations

Cette vérification détermine si l’agent peut correctement communiquer avec Job Runtime Data Service.

Les configurations du pare-feu et du proxy doivent permettre à l’agent Runbook Worker hybride de communiquer avec Job Runtime Data Service. Pour obtenir la liste des adresses et des ports à ouvrir, consultez [Planification réseau de workers hybrides](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Vérification de l’intégrité du service de machine virtuelle

### <a name="monitoring-agent-service-status"></a>État du service de Monitoring Agent

Cette vérification détermine si Microsoft Monitoring Agent `HealthService` est en cours d’exécution sur l’ordinateur.

Pour en savoir plus sur la résolution des problèmes de ce service, consultez [Microsoft Monitoring Agent n’est pas en cours d’exécution](hybrid-runbook-worker.md#mma-not-running).

Pour réinstaller l’agent Microsoft Monitoring Agent, consultez [Installer et configurer Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>Événements de service de Monitoring Agent

Cette vérification détermine s’il y a eu des événements `4502` dans le journal Operations Manager de la machine l’ordinateur au cours des dernières 24 heures.

Pour en savoir plus sur cet événement, consultez le [guide de résolution des problèmes](hybrid-runbook-worker.md#event-4502) pour cet événement.

## <a name="access-permissions-checks"></a>Vérification des autorisations d’accès

### <a name="machinekeys-folder-access"></a>Accès au dossier MachineKeys

La vérification de l’accès au dossier Crypto détermine si le compte système local a accès à `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>Résolution des problèmes hors connexion

Vous pouvez utiliser l’utilitaire de résolution des problèmes en mode hors connexion sur un Runbook Worker hybride en exécutant le script localement. Ce script, [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), se trouve dans PowerShell Gallery. L’exemple suivant illustre la sortie de ce script :

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
RuleName                    : .Net Framework 4.5+
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

Pour résoudre les autres problèmes de vos Runbooks Workers hybrides, consultez [Résolution des problèmes relatifs aux Runbooks Workers hybrides](hybrid-runbook-worker.md).
