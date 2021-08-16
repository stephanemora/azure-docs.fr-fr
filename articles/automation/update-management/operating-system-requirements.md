---
title: Clients Azure Automation Update Management pris en charge
description: Cet article décrit les systèmes d’exploitation Windows et Linux pris en charge avec Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 06/07/2021
ms.topic: conceptual
ms.openlocfilehash: b25cc49846f7cd7cba1bb121fcbb8ba93f3a9b02
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111895871"
---
# <a name="operating-systems-supported-by-update-management"></a>Systèmes d’exploitation pris en charge par Update Management

Cet article détaille les systèmes d’exploitation Windows et Linux pris en charge et la configuration requise pour les ordinateurs et les serveurs gérés par Update Management.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau suivant liste les systèmes d’exploitation pris en charge pour les évaluations des mises à jour et les mises à jour correctives. Une mise à jour corrective nécessite un Runbook Worker hybride système, qui est automatiquement installé quand vous activez la machine virtuelle ou le serveur à des fins de gestion par Update Management. Pour plus d’informations sur la configuration requise du Runbook Worker hybride, consultez [Déploiement d’un Runbook Worker hybride Windows](../automation-windows-hrw-install.md#prerequisites) et [Déploiement d’un Runbook Worker hybride Linux](../automation-linux-hrw-install.md#prerequisites).

> [!NOTE]
> L’évaluation des mises à jour des machines Linux est prise en charge uniquement dans certaines régions, comme listé dans la [table de mappages](../how-to/region-mappings.md#supported-mappings) du compte Automation et de l’espace de travail Log Analytics.

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2019 (Datacenter/Standard incluant Server Core)<br><br>Windows Server 2016 (Datacenter/Standard incluant Server Core)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM et SP1 Standard)| Update Management prend uniquement en charge les évaluations et les mises à jour correctives pour ce système d’exploitation. La fonctionnalité [Runbook Worker hybride](../automation-windows-hrw-install.md) est prise en charge pour Windows Server 2008 R2. |
|CentOS 6, 7 et 8 (x64)      | Les agents Linux nécessitent un accès à un référentiel de mise à jour. La mise à jour corrective basée sur la classification nécessite que `yum` retourne les données de sécurité que CentOS n’a pas dans ses versions RTM. Pour plus d’informations sur la mise à jour corrective basée sur des classifications sur CentOS, consultez [Mettre à jour des classifications sur Linux](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6, 7 et 8 (x64)     | Les agents Linux nécessitent un accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 12, 15 et 15.1 (x64)     | Les agents Linux nécessitent un accès à un référentiel de mise à jour.     |
|Ubuntu 14.04 LTS, 16.04 LTS et 18.04 LTS (x64)      |Les agents Linux nécessitent un accès à un référentiel de mise à jour.         |

> [!NOTE]
> Update Management ne permet pas d’automatiser de manière sécurisée la gestion des mises à jour sur toutes les instances d’un groupe de machines virtuelles identiques Azure. Les [mises à niveau automatiques de l’image de système d’exploitation](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) constituent la méthode recommandée pour gérer les mises à niveau des images de système d’exploitation sur le groupe identique.

## <a name="unsupported-operating-systems"></a>Systèmes d’exploitation non pris en charge

Le tableau suivant répertorie les systèmes d’exploitation qui ne sont pas pris en charge par Update Management :

|Système d’exploitation  |Notes  |
|---------|---------|
|Client Windows     | Les systèmes d’exploitation client (par exemple, Windows 7 et Windows 10) ne sont pas pris en charge.<br> Pour Azure Windows Virtual Desktop (WVD), la méthode recommandée<br> pour la gestion des mises à jour est [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) pour la gestion des correctifs pour les machines clientes Windows 10. |
|Windows Server 2016 Nano Server     | Non pris en charge.       |
|Nœuds Azure Kubernetes Service (AKS) | Non pris en charge. Utilisez le processus de correction décrit dans [Appliquer des mises à jour de sécurité et du noyau à des nœuds Linux dans Azure Kubernetes Service (AKS)](../../aks/node-updates-kured.md)|

## <a name="system-requirements"></a>Configuration requise

Les informations suivantes décrivent la configuration requise propre au système d’exploitation. Pour obtenir des conseils supplémentaires, consultez [Planification réseau](plan-deployment.md#ports). Pour comprendre la configuration requise pour le protocole TLS 1.2, consultez [Application de TLS 1.2 pour Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="windows"></a>Windows

Configuration logicielle requise :

- .NET Framework 4.6 ou version ultérieure est requis. ([Télécharger le .NET Framework](/dotnet/framework/install/guide-for-developers).)
- Windows PowerShell 5.1 est requis ([Télécharger Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)
- La fonctionnalité Update Management dépend du rôle de système Runbook Worker hybride. Vérifiez la [configuration requise](../automation-windows-hrw-install.md#prerequisites).

Les agents Windows Update doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services). Sinon, ils doivent pouvoir accéder à Microsoft Update. Pour les machines hybrides, nous vous recommandons d’installer l’agent Log Analytics pour Windows en connectant d’abord votre machine à [Azure Arc enabled servers](../../azure-arc/servers/overview.md), puis d’utiliser Azure Policy pour affecter la stratégie intégrée [Déployer l’agent Log Analytics sur des machines Azure Arc Windows](../../governance/policy/samples/built-in-policies.md#monitoring). Si vous prévoyez de superviser les machines avec VM Insights, utilisez plutôt l’initiative [Activation de VM Insights](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Vous pouvez utiliser Update Management avec Microsoft Endpoint Configuration Manager. Pour en savoir plus sur les scénarios d’intégration, consultez [Intégrer Update Management à Windows Endpoint Configuration Manager](mecmintegration.md). L’[agent Log Analytics pour Windows](../../azure-monitor/agents/agent-windows.md) est nécessaire aux serveurs Windows gérés par les sites dans votre environnement Configuration Manager.

Par défaut, les machines virtuelles Windows déployées à partir de la Place de marché Azure sont configurées pour recevoir des mises à jour automatiques du service Windows Update. Ce comportement ne change pas lorsque vous ajoutez des machines virtuelles Windows à votre espace de travail. Si vous ne gérez pas activement les mises à jour avec Update Management, le comportement par défaut (pour effectuer automatiquement les mises à jour) s’applique.

> [!NOTE]
> Vous pouvez modifier la stratégie de groupe afin que les redémarrages de la machine ne puissent être effectués que par l’utilisateur et non par le système. Les machines managées peuvent rester bloquées si Update Management ne dispose pas des droits nécessaires pour les redémarrer sans intervention manuelle de l'utilisateur. Pour plus d'informations, consultez [Configurer les paramètres de stratégie de groupe pour les mises à jour automatiques](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

### <a name="linux"></a>Linux

Configuration logicielle requise :

- La machine nécessite un accès à un référentiel de mise à jour, privé ou public.
- TLS 1.1 ou TLS 1.2 est exigé pour interagir avec Update Management.
- La fonctionnalité Update Management dépend du rôle de système Runbook Worker hybride. Vérifiez la [configuration requise](../automation-linux-hrw-install.md#prerequisites). Étant donné qu’Update Management utilise des runbooks Automation pour déclencher l’évaluation et la mise à jour de vos machines, vérifiez la [version de Python requise](../automation-linux-hrw-install.md#supported-runbook-types) pour votre distribution Linux prise en charge.

> [!NOTE]
> L’évaluation des mises à jour des machines Linux est uniquement prise en charge dans certaines régions. Consultez la [table des mappages](../how-to/region-mappings.md#supported-mappings) du compte Automation et de l’espace de travail Log Analytics.

Pour les machines hybrides, nous vous recommandons d’installer l’agent Log Analytics pour Linux en connectant d’abord votre machine à [Azure Arc enabled servers](../../azure-arc/servers/overview.md), puis d’utiliser Azure Policy pour affecter la stratégie intégrée [Déployer l’agent Log Analytics sur des machines Azure Arc Linux](../../governance/policy/samples/built-in-policies.md#monitoring). Autrement, si vous envisagez de superviser les machines avec Azure Monitor pour machines virtuelles, utilisez plutôt l’initiative [Activer Azure Monitor pour machines virtuelles](../../governance/policy/samples/built-in-initiatives.md#monitoring).

## <a name="next-steps"></a>Étapes suivantes

Avant d’activer et d’utiliser Update Management, consultez [Planification d’un déploiement Update Management](plan-deployment.md).