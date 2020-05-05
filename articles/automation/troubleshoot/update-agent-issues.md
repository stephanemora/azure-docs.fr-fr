---
title: Résolution des problèmes de l’agent de mise à jour Windows dans Azure Automation Update Management
description: Découvrez comment détecter et résoudre les problèmes liés à l’agent de mise à jour Windows à l’aide de la solution Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678970"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Résoudre les problèmes de l’agent de mise à jour Windows

De nombreuses raisons peuvent expliquer pourquoi votre machine n’apparaît pas comme étant prête (saine) dans Update Management. Dans Update Management, vous pouvez vérifier l’intégrité d’un agent Runbook Worker hybride pour déterminer le problème sous-jacent. Cet article explique comment exécuter l’utilitaire de résolution des problèmes pour les machines Azure à partir du portail Azure et les machines non-Azure dans le [scénario hors connexion](#troubleshoot-offline).

Voici les trois états de préparation possibles d’une machine :

* Prêt : le Runbook Worker hybride est déployé et a été vu pour la dernière fois il y a moins de 1 heure.
* Déconnecté : le Runbook Worker hybride est déployé et a été vu pour la dernière fois il y a plus de 1 heure.
* Non configuré : le Runbook Worker hybride est introuvable ou n’a pas terminé son intégration.

> [!NOTE]
> L’état actuel d’une machine peut s’afficher avec un léger retard sur le Portail Azure.

## <a name="start-the-troubleshooter"></a>Démarrer l’utilitaire de résolution des problèmes

Pour les machines Azure, cliquez sur le lien **Résolution des problèmes** sous la colonne **Préparation de l’agent de mise à jour** dans le portail pour ouvrir la page Résoudre les problèmes de l’agent de mise à jour. Pour les machines non-Azure, le lien vous dirige vers cet article. Consultez les [instructions hors connexion](#troubleshoot-offline) pour résoudre les problèmes d’une machine non-Azure.

![Mettre à jour la liste de gestion des machines virtuelles](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Pour vérifier l’intégrité du Runbook Worker hybride, la machine virtuelle doit être en cours d’exécution. Si la machine virtuelle n’est pas en cours d’exécution, un bouton **Démarrer la machine virtuelle** s’affiche.

Dans la page Résoudre les problèmes de l’agent de mise à jour, sélectionnez **Exécuter les vérifications** pour démarrer l’utilitaire de résolution des problèmes. L’utilitaire de résolution des problèmes utilise la [commande Run](../../virtual-machines/windows/run-command.md) pour exécuter un script sur la machine et vérifier les dépendances. Quand l’utilitaire de résolution des problèmes a terminé, il retourne le résultat des vérifications.

![Page Résoudre les problèmes de l’agent de mise à jour](../media/update-agent-issues/troubleshoot-page.png)

Les résultats sont affichés dans la page quand ils sont prêts. Les sections des vérifications affichent ce qui est inclus dans chaque vérification.

![Résoudre les problèmes de l’agent de mise à jour - vérifications](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Vérifications de la configuration requise

### <a name="operating-system"></a>Système d’exploitation

Le contrôle du système d’exploitation permet de vérifier si le Runbook Worker hybride exécute l’un des systèmes d’exploitation suivants :

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2012 et ultérieur |.NET Framework 4.6 ou version ultérieure est requis. ([Télécharger le .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 5.1 est requis.  ([Télécharger Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

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

Cette vérification détermine si l’agent Log Analytics pour Windows (`healthservice`) est en cours d’exécution sur l’ordinateur. Pour en savoir plus sur la résolution des problèmes de ce service, consultez [l’agent Log Analytics pour Windows ne s’exécute pas](hybrid-runbook-worker.md#mma-not-running).

Pour réinstaller l’agent Log Analytics pour Windows, consultez [installer et configurer l’agent Log Analytics pour Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Événements de service de Monitoring Agent

Cette vérification détermine si des événements 4502 apparaissent dans le journal Azure Operations Manager de la machine au cours des dernières 24 heures.

Pour en savoir plus sur cet événement, consultez le [guide de résolution des problèmes](hybrid-runbook-worker.md#event-4502) pour cet événement.

## <a name="access-permissions-checks"></a>Vérification des autorisations d’accès

### <a name="machinekeys-folder-access"></a>Accès au dossier MachineKeys

La vérification d’accès au dossier Crypto détermine si le compte système Local a accès à C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Résolution des problèmes hors connexion

Vous pouvez utiliser l’utilitaire de résolution des problèmes sur un Runbook Worker hybride hors connexion en exécutant le script localement. Vous pouvez obtenir le script [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) dans PowerShell Gallery. Vous devez avoir installé WMF 4.0 ou version ultérieure pour exécuter le script. Pour télécharger la dernière version de PowerShell, consultez [Installer plusieurs versions de PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

La sortie de ce script ressemble à l’exemple suivant :

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
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
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

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
