---
title: Résolution des problèmes de l’agent de mise à jour Linux dans Azure Automation Update Management
description: Découvrez comment détecter et résoudre les problèmes liés à l’agent de mise à jour Linux Windows à l’aide de la solution Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679074"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Résoudre les problèmes de l’agent de mise à jour Linux

De nombreuses raisons peuvent expliquer pourquoi votre machine n’apparaît pas comme étant prête (saine) dans Update Management. Dans Update Management, vous pouvez vérifier l’intégrité d’un agent Runbook Worker hybride pour déterminer le problème sous-jacent. Cet article explique comment exécuter l’utilitaire de résolution des problèmes pour les machines Azure à partir du portail Azure et les machines non-Azure dans le [scénario hors connexion](#troubleshoot-offline). 

Voici les trois états de préparation possibles d’une machine :

* Prêt : le Runbook Worker hybride est déployé et a été vu pour la dernière fois il y a moins d’une heure.
* Déconnecté : le Runbook Worker hybride est déployé et a été vu pour la dernière fois il y a plus d’une heure.
* Non configuré : le Runbook Worker hybride est introuvable ou n’a pas terminé son intégration.

> [!NOTE]
> L’état actuel d’une machine peut s’afficher avec un léger retard sur le Portail Azure.

## <a name="start-the-troubleshooter"></a>Démarrer l’utilitaire de résolution des problèmes

Pour les machines Azure, cliquez sur le lien **Résolution des problèmes** sous la colonne **Préparation de l’agent de mise à jour** dans le portail pour ouvrir la page Résoudre les problèmes de l’agent de mise à jour. Pour les machines non-Azure, le lien vous dirige vers cet article. Consultez les instructions hors connexion pour résoudre les problèmes d’une machine non-Azure.

![vm list page](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Les vérifications nécessitent que la machine virtuelle soit en cours d’exécution. Si la machine virtuelle n’est pas en cours d’exécution, vous voyez un bouton **Démarrer la machine virtuelle**.

Dans la page Résoudre les problèmes de l’agent de mise à jour, cliquez sur **Exécuter les vérifications** pour démarrer l’utilitaire de résolution des problèmes. L’utilitaire de résolution des problèmes utilise la [commande Run](../../virtual-machines/linux/run-command.md) pour exécuter un script sur la machine afin de vérifier les dépendances. Lorsque l’utilitaire de résolution des problèmes a terminé, il retourne le résultat des vérifications.

![Page de résolution des problèmes](../media/update-agent-issues-linux/troubleshoot-page.png)

Lorsque l’inspection est terminée, les résultats sont renvoyés. Les sections de vérification fournissent des informations sur ce que chaque vérification recherche.

![Page de vérifications de l’agent de mise à jour](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Vérifications de la configuration requise

### <a name="operating-system"></a>Système d’exploitation

Le contrôle du système d’exploitation permet de vérifier si le Runbook Worker hybride exécute l’un des systèmes d’exploitation suivants :

|Système d’exploitation  |Notes  |
|---------|---------|
|CentOS 6 (x86/x64) et 7 (x64)      | Les agents Linux doivent avoir accès à un référentiel de mise à jour. La mise à jour corrective basée sur la classification nécessite que 'yum' retourne les données de sécurité que CentOS n’a pas directement.         |
|Red Hat Enterprise 6 (x86/x64) et 7 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 LTS (x86/x64)      |Les agents Linux doivent avoir accès à un référentiel de mise à jour.         |

## <a name="monitoring-agent-service-health-checks"></a>Vérifications d’intégrité du service Monitoring Agent

### <a name="log-analytics-agent"></a>Agent Log Analytics

Cette vérification garantit que l’agent Log Analytics pour Linux est installé. Pour obtenir des instructions sur son installation, consultez [Installer l’agent pour Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>État de l’agent Log Analytics

Cette vérification garantit que l’agent Log Analytics pour Linux est en cours d’exécution. Si l’agent n’est pas en cours d’exécution, vous pouvez exécuter la commande suivante pour tenter de le redémarrer. Pour plus d’informations sur la résolution des problèmes de l’agent, consultez [Résolution des problèmes de Runbook Worker hybride Linux](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multirésidence

Cette vérification détermine si l’agent envoie des rapports à plusieurs espaces de travail. La multirésidence n’est pas prise en charge par Update Management.

### <a name="hybrid-runbook-worker"></a>Runbook Worker hybride

Ce contrôle permet de vérifier que l’agent Log Analytics pour Linux a le package Runbook Worker hybride. Ce package est nécessaire pour le fonctionnement de Update Management.

### <a name="hybrid-runbook-worker-status"></a>État du Runbook Worker hybride

Cette vérification garantit que le Runbook Worker hybride est en cours d’exécution sur l’ordinateur. Les processus suivants doivent être présents si le Runbook Worker hybride s’exécute correctement. Pour plus d’informations, consultez [Résolution des problèmes de l’Agent Log Analytics pour Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Vérifications de la connectivité

### <a name="general-internet-connectivity"></a>Connectivité Internet générale

Cette vérification garantit que l’ordinateur a accès à internet.

### <a name="registration-endpoint"></a>Point de terminaison d’inscription

Cette vérification détermine si le Runbook Worker hybride peut communiquer correctement avec Azure Automation et l’espace de travail Log Analytics.

Les configurations du pare-feu et du proxy doivent permettre à l’agent Runbook Worker hybride de communiquer avec le point de terminaison d’inscription. Pour obtenir la liste des adresses et des ports à ouvrir, consultez [Planification réseau de workers hybrides](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Point de terminaison des opérations

Cette vérification détermine si l’agent peut correctement communiquer avec Job Runtime Data Service.

Les configurations du pare-feu et du proxy doivent permettre à l’agent Runbook Worker hybride de communiquer avec Job Runtime Data Service. Pour obtenir la liste des adresses et des ports à ouvrir, consultez [Planification réseau de workers hybrides](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Point de terminaison Log Analytics 1

Cette vérification contrôle que votre machine a accès aux points de terminaison nécessaires pour l’agent Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Point de terminaison Log Analytics 2

Cette vérification contrôle que votre machine a accès aux points de terminaison nécessaires pour l’agent Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Point de terminaison Log Analytics 3

Cette vérification contrôle que votre machine a accès aux points de terminaison nécessaires pour l’agent Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Résolution des problèmes hors connexion

Vous pouvez utiliser l’utilitaire de résolution des problèmes en mode hors connexion sur un Runbook Worker hybride en exécutant le script localement. Le script python [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) se trouve dans le centre de scripts. L’exemple suivant illustre la sortie de ce script :

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les autres problèmes de vos Runbooks Workers hybrides, consultez [Résolution des problèmes relatifs aux Runbooks Workers hybrides](hybrid-runbook-worker.md).
