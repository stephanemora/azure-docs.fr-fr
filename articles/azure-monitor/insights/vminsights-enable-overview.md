---
title: Activer Azure Monitor pour une vue d’ensemble de machines virtuelles (version préliminaire) | Microsoft Docs
description: Cet article décrit comment déployer et configurer Azure Monitor pour les machines virtuelles et de la configuration système requise.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 4eb07b29f68c5f495bdbe5e23f5b226480b66661
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524069"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Activer Azure Monitor pour une vue d’ensemble de machines virtuelles (version préliminaire)

Cet article fournit une vue d’ensemble des options disponibles pour le programme d’installation d’Azure Monitor pour surveiller l’intégrité, performances et découvrir les dépendances d’application en cours d’exécution sur les machines virtuelles et des machines virtuelles identiques de machines virtuelles, de machines virtuelles sur site ou de machines virtuelles hébergées dans un autre environnement cloud.  

Pour activer Azure Monitor pour machines virtuelles, utilisez l’une des méthodes suivantes :

* Activer une seule machine virtuelle Azure ou les machines virtuelles identiques en sélectionnant **Insights (version préliminaire)** directement à partir de l’échelle de machine virtuelle ou une machine virtuelle définie.
* Activer deux ou plus les machines virtuelles et les machines virtuelles Azure identiques à l’aide d’Azure Policy. Via cette méthode, les dépendances requises de machines virtuelles existantes et nouvelles et identiques sont installés et configurés correctement. Machines virtuelles non conforme et identiques sont signalés, pour que vous puissiez décider s’il faut les activer et comment vous voulez les corriger.
* Activez plusieurs machines virtuelles Azure ou groupes de machines virtuelles identiques sur un abonnement ou un groupe de ressources spécifié à l’aide de PowerShell.
* Permettent de surveiller les ordinateurs virtuels ou physiques hébergé dans votre réseau d’entreprise ou un autre environnement cloud.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, prenez connaissance de ce qui suit.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pour les machines virtuelles prend en charge un espace de travail Analytique de journal dans les régions suivantes :

- USA Centre-Ouest
- USA Est
- Canada Central<sup>1</sup>
- Royaume-Uni Sud<sup>1</sup>
- Europe Ouest
- Asie Sud-Est<sup>1</sup>

<sup>1</sup> Cette région ne prend pas encore en charge la fonctionnalité d’intégrité d’Azure Monitor pour machines virtuelles.

>[!NOTE]
>Il est possible de déployer des machines virtuelles Azure à partir de n’importe quelle région, et pas seulement des régions qui prennent en charge l’espace de travail Log Analytics.
>

Si vous n’avez pas d’espace de travail, vous pouvez en créer un comme suit :
* [L’interface de ligne de commande Microsoft Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Le portail Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Si vous activez l’analyse pour une seule machine virtuelle Azure ou les machines virtuelles identiques dans le portail Azure, vous pouvez créer un espace de travail pendant ce processus.

Pour le scénario à grande échelle à l’aide de la stratégie Azure, Azure PowerShell ou modèles Azure Resource Manager, votre espace de travail Analytique de journal doit d’abord configuré ce qui suit :

* Installez les solutions ServiceMap et InfrastructureInsights. Vous pouvez exécuter cette installation en utilisant un modèle Azure Resource Manager qui est fourni, ou à l’aide de la **configurer un espace de travail** option se trouve sur le **prise en main** onglet.
* Configurez l’espace de travail Log Analytics pour collecter les compteurs de performances.

Pour configurer votre espace de travail pour le scénario à grande échelle, vous pouvez le configurer en utilisant l’une des méthodes suivantes :

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* À partir de la **configurer un espace de travail** option sur l’analyse Azure pour les machines virtuelles [couverture de la stratégie](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) page

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau ci-après répertorie les systèmes d’exploitation Windows et Linux qui sont pris en charge avec Azure Monitor pour machines virtuelles. Une liste complète détaillant les versions principales et mineures de système d’exploitation et de noyau Linux prises en charge est fournie plus loin dans cette section.

|Version du SE |Performances |Cartes |Intégrité |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> La fonctionnalité Performances d’Azure Monitor pour machines virtuelles est disponible uniquement à partir d’Azure Monitor. Elle n’est pas disponible lorsque vous accédez directement au volet gauche de la machine virtuelle Azure.

>[!NOTE]
>Les informations ci-après concernent la prise en charge du système d’exploitation Linux :
> - Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
> - Les versions non standard du noyau, par exemple Physical Address Extension (PAE) et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version *2.6.16.21-0.8-xen* n’est pas pris en charge.
> - Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
> - Le noyau CentOSPlus est pris en charge.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Version du SE | Version du noyau |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Version du SE | Version du noyau |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Version du SE | Version du noyau |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Serveur Ubuntu

| Version du SE | Version du noyau |
|:--|:--|
| Ubuntu 18.04 | noyau 4.15.* |
| Ubuntu 16.04.3 | noyau 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Version du SE | Version du noyau
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Version du SE | Version du noyau
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

La fonctionnalité Azure Monitor pour machines virtuelles Map obtient ses données à partir de l’agent Microsoft Dependency. Celui-ci dépend de l’agent Log Analytics pour ses connexions à Log Analytics. Par conséquent, Log Analytics Agent doit être installé et configuré avec l’agent Dependency sur le système.

Que vous activiez Azure Monitor pour machines virtuelles pour une seule machine virtuelle Azure ou que vous utilisiez les méthodes de déploiement à l’échelle, vous devez utiliser l’extension de l’agent Azure VM Dependency pour installer l’agent dans le cadre de cette expérience.

Dans un environnement hybride, vous pouvez télécharger et installer l’agent de dépendances de deux façons : manuellement ou à l’aide d’une méthode de déploiement automatisé pour les machines virtuelles qui sont hébergées en dehors d’Azure.

Le tableau suivant décrit les sources connectées prises en charge par la fonctionnalité Map dans un environnement hybride.

| Source connectée | Prise en charge | Description |
|:--|:--|:--|
| Agents Windows | Oui | Outre [l’agent Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md), les agents Windows nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez les [systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Agents Linux | Oui | Outre [l’agent Log Analytics pour Linux](../../azure-monitor/platform/log-analytics-agent.md), les agents Linux nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, reportez-vous aux [systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | Non  | |

L’agent Dependency peut être téléchargé à partir des emplacements suivants :

| Fichier | SE | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour activer les fonctionnalités dans Azure Monitor pour machines virtuelles et y accéder, vous devez avoir le rôle d’accès suivant :

- Pour l’activer, vous devez avoir le *collaborateur d’Analytique de journal* rôle.

- Pour afficher les données de performances, d’intégrité et de mappage, vous devez avoir le rôle *Lecteur d’analyse* pour la machine virtuelle Azure. L’espace de travail Log Analytics doit être configuré pour Azure Monitor pour machines virtuelles.

Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Comment activer Azure Monitor pour les machines virtuelles (version préliminaire)

Vous activez Azure Monitor pour les machines virtuelles en utilisant l’une des méthodes suivantes décrites dans le tableau suivant.

| État du déploiement | Méthode | Description  |
|------------------|--------|-------------|
| Ensemble d’échelle de machine virtuelle Azure ou une machine virtuelle unique | [Directement à partir de la machine virtuelle](vminsights-enable-single-vm.md) | Vous pouvez activer une seule machine virtuelle Azure en sélectionnant **Insights (version préliminaire)** directement à partir de l’échelle de machine virtuelle ou une machine virtuelle définie. |
| Plusieurs machines virtuelles Azure ou machines virtuelles identiques | [Azure Policy](vminsights-enable-at-scale-policy.md) | Vous pouvez activer plusieurs machines virtuelles de Azure à l’aide de la stratégie de Azure et les définitions de stratégie disponibles. |
| Plusieurs machines virtuelles Azure ou machines virtuelles identiques | [Modèles Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Vous pouvez activer plusieurs jeux de mise à l’échelle de machines virtuelles Azure ou une machine virtuelle sur un groupe de ressources ou abonnement spécifié à l’aide de modèles Azure PowerShell ou Azure Resource Manager. |
| Cloud hybride | [Activer pour un environnement hybride](vminsights-enable-hybrid-cloud.md) | Vous pouvez déployer sur les ordinateurs virtuels ou physiques qui sont hébergés dans votre centre de données ou d’autres environnements de cloud. |

## <a name="performance-counters-enabled"></a>Compteurs de performances activés

Azure Monitor pour les machines virtuelles configure un espace de travail Analytique des journaux pour collecter les compteurs de performances qu’il utilise. Le tableau suivant répertorie les objets et compteurs collectés toutes les 60 secondes.

### <a name="windows-performance-counters"></a>Compteurs de performances Windows

|Nom d’objet |Nom du compteur |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg. Disk sec/Read |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |Avg. Disk sec/Write |
|LogicalDisk |Disk Bytes/sec |
|LogicalDisk |Nb d’octets de lecture de disque/s  |
|LogicalDisk |Nb d’opérations de lectures de disque/s  |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk | Nb d’octets d’écriture de disque/s |
|LogicalDisk | Nb d’opération d’écriture de disque/s |
|LogicalDisk |Free Megabytes |
|Mémoire |Nombre d’octets disponibles |
|Carte réseau |Octets reçus/s |
|Carte réseau |Octets envoyés/s |
|Processeur |% temps processeur |

### <a name="linux-performance-counters"></a>Compteurs de performances Linux

|Nom d’objet |Nom du compteur |
|------------|-------------|
|Logical Disk |% Used Space |
|Logical Disk |Nb d’octets de lecture de disque/s  |
|Logical Disk |Nb d’opérations de lectures de disque/s  |
|Logical Disk |Disk Transfers/sec |
|Logical Disk | Nb d’octets d’écriture de disque/s |
|Logical Disk | Nb d’opération d’écriture de disque/s |
|Logical Disk |Free Megabytes |
|Logical Disk |Logical Disk Bytes/sec |
|Mémoire |Available MBytes Memory |
|Réseau |Total Bytes Received |
|Réseau |Total Bytes Transmitted |
|Processeur |% temps processeur |

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation

Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Azure Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service. Afin d’offrir des fonctionnalités de dépannage précises et efficaces, les données de la fonctionnalité Map incluent des informations sur la configuration de votre logiciel, telles que le système d’exploitation et la version, l’adresse IP, le nom DNS et le nom de la station de travail. Microsoft ne collecte pas de nom, d’adresse ou d’autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, voir la [Déclaration de confidentialité des Services en ligne de Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois la supervision activée pour votre machine virtuelle, ces informations peuvent être analysées par Azure Monitor pour machines virtuelles. Pour savoir comment utiliser la fonctionnalité de contrôle de l’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles](vminsights-health.md). Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md).
