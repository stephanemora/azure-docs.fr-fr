---
title: Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Découvrez comment déployer et configurer Azure Monitor pour des machines virtuelles. Découvrez la configuration système requise.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: ce95223e9501ad7bec2bd260a8fe6f1537db5593
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400615"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Activer Azure Monitor pour machines virtuelles (préversion)

Cet article fournit une vue d’ensemble des options disponibles pour configurer Azure Monitor pour machines virtuelles. Utilisez Azure Monitor pour machines virtuelles pour surveiller l’intégrité et les performances. Découvrez les dépendances d’application qui s’exécutent sur des machines virtuelles et des groupes de machines virtuelles identiques Azure, des machines virtuelles locales ou des machines virtuelles hébergées dans un autre environnement cloud.  

Pour configurer Azure Monitor pour des machines virtuelles :

* Activez une machine virtuelle ou un groupe de machines virtuelles identiques Azure en sélectionnant **Insights (préversion)** directement à partir de la machine virtuelle ou du groupe de machines virtuelles identiques.
* Activez au moins deux machines virtuelles et groupes de machines virtuelles identiques Azure à l’aide d’Azure Policy. Cette méthode vérifie que, sur des machines virtuelles et des groupes identiques Azure, les dépendances requises sont installées et correctement configurées. Les machines virtuelles et groupes identiques non conformes sont signalés afin que vous puissiez décider si vous voulez les activer et les corriger.
* Activez plusieurs machines virtuelles Azure ou groupes de machines virtuelles identiques sur un abonnement ou un groupe de ressources spécifié à l’aide de PowerShell.
* Activez Azure Monitor pour machines virtuelles afin de surveiller les machines virtuelles ou ordinateurs physiques hébergés dans votre réseau d’entreprise ou un autre environnement cloud.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer, prenez connaissance de ce qui suit. 

>[!NOTE]
>Les informations suivantes décrites dans cette section s’appliquent également à la solution [Service Map](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pour machines virtuelles prend en charge un espace de travail Log Analytics dans les régions suivantes :

- Centre-USA Ouest
- USA Ouest
- USA Ouest 2
- États-Unis - partie centrale méridionale
- USA Est
- USA Est 2
- USA Centre
- Centre-Nord des États-Unis
- Centre du Canada
- Sud du Royaume-Uni
- Europe Nord
- Europe Ouest
- Asie Est
- Asie Sud-Est
- Inde centrale
- Japon Est
- Australie Est
- Sud-Australie Est

>[!NOTE]
>Vous pouvez déployer des machines virtuelles Azure à partir de toute région. Ces machines virtuelles ne sont pas limitées aux régions prises en charge par l’espace de travail Log Analytics.
>

Si vous n’avez pas d’espace de travail, vous pouvez en créer un en utilisant l’une des ressources suivantes :
* [L’interface de ligne de commande Microsoft Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Le portail Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Vous pouvez également créer un espace de travail pendant que vous activez la surveillance pour une machine virtuelle ou un groupe de machines virtuelles identiques Azure dans le portail Azure.

Pour configurer un scénario à grande échelle qui utilise des modèles Azure Policy, Azure PowerShell ou Azure Resource Manager, dans votre espace de travail Log Analytics :

* Installez les solutions ServiceMap et InfrastructureInsights. Vous pouvez effectuer cette installation à l’aide d’un modèle Azure Resource Manager fourni. Ou bien, sous l’onglet **Prise en main**, vous pouvez sélectionner **Configurer l’espace de travail**.
* Configurez l’espace de travail Log Analytics pour collecter les compteurs de performances.

Pour configurer votre espace de travail pour le scénario à grande échelle, utilisez l’une des méthodes suivantes :

* Utilisez [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Sur la page [**Couverture de stratégie**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) d’Azure Monitor pour machines virtuelles, sélectionnez **Configurer l’espace de travail**. 

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau ci-après répertorie les systèmes d’exploitation Windows et Linux qu’Azure Monitor pour machines virtuelles prend en charge. Plus loin dans cette section, figure une liste complète détaillant les versions principales et mineures de système d’exploitation et de noyau Linux prises en charge.

|Version du SE |Performances |Cartes |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> La fonctionnalité Performances d’Azure Monitor pour machines virtuelles est disponible uniquement à partir d’Azure Monitor. Elle n’est pas directement accessible à partir du volet gauche de la machine virtuelle Azure.

>[!NOTE]
>Dans le système d’exploitation Linux :
> - Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
> - Les versions non standard du noyau, par exemple Physical Address Extension (PAE) et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version *2.6.16.21-0.8-xen* n’est pas pris en charge.
> - Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
> - Le noyau CentOSPlus est pris en charge.
> - Le noyau Linux doit être corrigé pour la vulnérabilité Spectre. Pour plus d’informations, consultez le fournisseur de votre distribution Linux.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Version du SE | Version du noyau |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Version du SE | Version du noyau |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Version du SE | Version du noyau |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Serveur Ubuntu

| Version du SE | Version du noyau |
|:--|:--|
| 18,04 | 5.0 (inclut un noyau optimisé pour Azure)<br>4.18 *<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Version du SE | Version du noyau |
|:--|:--|
|12 SP4 | 4.12.* (inclut un noyau optimisé pour Azure) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Version du SE | Version du noyau |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

La fonctionnalité Map dans Azure Monitor pour machines virtuelles obtient ses données à partir de Microsoft Dependency Agent. Celui-ci dépend de l’agent Log Analytics pour ses connexions à Log Analytics. Par conséquent, Log Analytics Agent doit être installé et configuré avec l’agent Dependency sur le système.

Que vous activiez Azure Monitor pour machines virtuelles pour une seule machine virtuelle Azure ou que vous utilisiez les méthodes de déploiement à grande échelle, utilisez l’extension de l’agent Azure VM Dependency pour [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) ou [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) pour installer l’agent dans le cadre de cette expérience.

>[!NOTE]
>Les informations suivantes décrites dans cette section s’appliquent également à la solution [Service Map](service-map.md).  

Dans un environnement hybride, vous pouvez télécharger et installer l’agent Dependency manuellement ou à l’aide d’une méthode automatisée.

Le tableau suivant décrit les sources connectées prises en charge par la fonctionnalité Map dans un environnement hybride.

| Source connectée | Prise en charge | Description |
|:--|:--|:--|
| Agents Windows | Oui | Outre l’agent [Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md), les agents Windows ont besoin du Dependency Agent. Pour plus d’informations, voir [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Agents Linux | Oui | Outre l’agent [Log Analytics pour Linux](../../azure-monitor/platform/log-analytics-agent.md), les agents Linux ont besoin du Dependency Agent. Pour plus d’informations, voir [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | Non | |

Vous pouvez télécharger le Dependency Agent à partir des emplacements suivants :

| Fichier | Système d''exploitation | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Pour activer les fonctionnalités et y accéder dans Azure Monitor pour les machines virtuelles, vous devez avoir le rôle *Contributeur Log Analytics*. Pour afficher les données de performances, d’intégrité et de mappage, vous devez avoir le rôle *Lecteur d’analyse* pour la machine virtuelle Azure. L’espace de travail Log Analytics doit être configuré pour Azure Monitor pour machines virtuelles.

Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Comment activer Azure Monitor pour machines virtuelles (préversion)

Pour activer Azure Monitor pour les machines virtuelles, utilisez l’une des méthodes décrites dans ce tableau :

| État du déploiement | Méthode | Description |
|------------------|--------|-------------|
| Machine virtuelle ou groupe de machines virtuelles identiques uniques Azure | [Activer à partir de la machine virtuelle](vminsights-enable-single-vm.md) | Vous pouvez activer une machine virtuelle Azure en sélectionnant **Insights (préversion)** directement à partir de la machine virtuelle ou du groupe de machines virtuelles identiques. |
| Machines virtuelles ou groupes de machines virtuelles identiques multiples Azure | [Activer via Azure Policy](vminsights-enable-at-scale-policy.md) | Vous pouvez activer plusieurs machines virtuelles Azure à l’aide d’Azure Policy et des définitions de stratégie disponibles. |
| Machines virtuelles ou groupes de machines virtuelles identiques multiples Azure | [Activer à l’aide de modèles Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Vous pouvez activer plusieurs machines virtuelles ou groupes de machines virtuelles identiques Azure sur un abonnement ou un groupe de ressources spécifié en utilisant des modèles Azure PowerShell ou Azure Resource Manager. |
| Cloud hybride | [Activer pour l’environnement hybride](vminsights-enable-hybrid-cloud.md) | Vous pouvez déployer sur des machines virtuelles ou des ordinateurs physiques hébergés dans votre centre de données ou d’autres environnements cloud. |

## <a name="performance-counters-enabled"></a>Compteurs de performances activés 

Azure Monitor pour machines virtuelles configure un espace de travail Log Analytics pour collecter des compteurs de performances qu’il utilise. Les tableaux suivants répertorient les objets et compteurs collectés toutes les 60 secondes.

>[!NOTE]
>La liste suivante de compteurs de performances activée par Azure Monitor pour machines virtuelles ne vous limite pas à l’activation de compteurs supplémentaires que vous devez collecter à partir de machines virtuelles envoyant des rapports à l’espace de travail. En outre, si vous désactivez ces compteurs, cela empêchera l’ensemble des graphiques de performances inclus avec la fonctionnalité de performances d’afficher l’utilisation des ressources de vos machines virtuelles.

### <a name="windows-performance-counters"></a>Compteurs de performances Windows

|Nom d’objet |Nom du compteur |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg. Disk sec/Read |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |Avg. Disk sec/Write |
|LogicalDisk |Disk Bytes/sec |
|LogicalDisk |Nb d’octets de lecture de disque/s |
|LogicalDisk |Nb d’opérations de lectures de disque/s |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk |Nb d’octets d’écriture de disque/s |
|LogicalDisk |Nb d’opération d’écriture de disque/s |
|LogicalDisk |Free Megabytes |
|Mémoire |Nombre d’octets disponibles |
|Carte réseau |Octets reçus/s |
|Carte réseau |Octets envoyés/s |
|Processeur |% temps processeur |

### <a name="linux-performance-counters"></a>Compteurs de performances Linux

|Nom d’objet |Nom du compteur |
|------------|-------------|
|Logical Disk |% Used Space |
|Logical Disk |Nb d’octets de lecture de disque/s |
|Logical Disk |Nb d’opérations de lectures de disque/s |
|Logical Disk |Disk Transfers/sec |
|Logical Disk |Nb d’octets d’écriture de disque/s |
|Logical Disk |Nb d’opération d’écriture de disque/s |
|Logical Disk |Free Megabytes |
|Logical Disk |Logical Disk Bytes/sec |
|Mémoire |Available MBytes Memory |
|Réseau |Total Bytes Received |
|Réseau |Total Bytes Transmitted |
|Processeur |% temps processeur |

## <a name="management-packs"></a>Packs d’administration

Lorsque Azure Monitor pour machines virtuelles est activé et configuré avec un espace de travail Log Analytics, un pack d’administration est transféré à tous les ordinateurs Windows qui sont en rapport avec cet espace de travail. Si vous avez [intégré votre groupe d’administration System Center Operations Manager](../../azure-monitor/platform/om-agents.md) à l’espace de travail Log Analytics, le pack d’administration Service Map est déployé à partir du groupe d’administration sur les ordinateurs Windows connectés au groupe d’administration.  

Le pack d’administration se nomme *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Il est écrit dans le dossier `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`. Le pack d’administration utilise la source de données `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation

Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Azure Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service. 

Pour fournir des capacités de dépannage précises et efficaces, la fonctionnalité Map inclut des données sur la configuration de votre logiciel. Les données fournissent des informations telles que le système d’exploitation et sa version, l’adresse IP, le nom DNS et nom de la station de travail. Microsoft ne collecte pas de nom, d’adresse ou d’autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, voir la [Déclaration de confidentialité des Services en ligne de Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

À présent que vous avez activé la surveillance de votre machine virtuelle, des informations de surveillance sont disponibles pour analyse dans Azure Monitor pour machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser la fonctionnalité d’analyse des performances, consultez [Afficher les performances d’Azure Monitor pour machines virtuelles](vminsights-performance.md). Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md).
