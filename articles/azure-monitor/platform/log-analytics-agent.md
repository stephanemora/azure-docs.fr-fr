---
title: Présentation de l’agent Log Analytics
description: Cette rubrique aide à comprendre comment collecter des données et effectuer le monitoring d’ordinateurs hébergés dans un environnement Azure, local ou tout autre environnement cloud avec Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 36b94f53d3a9113c3980c94c3b8eff0713f11814
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446542"
---
# <a name="log-analytics-agent-overview"></a>Présentation de l’agent Log Analytics
L’agent Azure Log Analytics a été développé pour une gestion complète des machines virtuelles dans tous les clouds, des machines locales et de celles surveillées par [Operations Manager](/system-center/scom/). Les agents Windows et Linux envoient des données collectées à partir de différentes sources à votre espace de travail Log Analytics dans Azure Monitor, ainsi que des journaux d’activité ou mesures uniques tels que définis dans une solution de supervision. L’agent Log Analytics prend également en charge des analyses et d’autres services dans Azure Monitor comme [Azure Monitor pour machines virtuelles](../insights/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml) et [Azure Automation](../../automation/automation-intro.md).

Cet article propose une présentation détaillée des exigences en matière d'agent, de système et de réseau, ainsi que des différentes méthodes de déploiement.

> [!NOTE]
> Vous pouvez également voir l’agent Log Analytics appelé Microsoft Monitoring Agent (MMA) ou agent Linux OMS.

> [!NOTE]
> L’extension Diagnostics Azure est l’un des agents disponibles pour collecter des données de supervision à partir du système d’exploitation invité des ressources de calcul. Pour obtenir une description des différents agents et des conseils sur la sélection des agents appropriés en fonction de vos besoins, consultez [Vue d’ensemble des agents Azure Monitor](agents-overview.md).

## <a name="comparison-to-azure-diagnostics-extension"></a>Comparaison avec l’extension Diagnostics Azure
L’[extension Diagnostics Azure](diagnostics-extension-overview.md) dans Azure Monitor peut également être utilisée pour collecter des données de supervision à partir du système d’exploitation invité des machines virtuelles Azure. Vous pouvez choisir d’utiliser l’un ou l’autre, ou les deux en fonction de vos besoins. Pour une comparaison détaillée des agents Azure Monitor, consultez [Vue d’ensemble des agents Azure Monitor](agents-overview.md). 

Les principales différences à prendre en compte sont les suivantes :

- L’extension Diagnostics Azure peut être utilisée uniquement avec des machines virtuelles Azure. L’agent Log Analytics peut être utilisé avec des machines virtuelles dans Azure, dans d’autres clouds et en local.
- L’extension Diagnostics Azure envoie des données à Stockage Azure, à [Azure Monitor Metrics](data-platform-metrics.md) (Windows uniquement) et à Event Hubs. L’agent Log Analytics collecte des données dans des [journaux d’activité Azure Monitor](data-platform-logs.md).
- L’agent Log Analytics est requis pour les [solutions](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor pour machines virtuelles](../insights/vminsights-overview.md) et d’autres services tels qu’[Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Coûts
Il n’y a aucun coût lié à l’agent Log Analytics, mais vous pouvez être facturé pour les données ingérées. Consultez [Gérer l’utilisation et les coûts avec les journaux d’activité Azure Monitor](manage-cost-storage.md) pour obtenir des informations détaillées sur la tarification des données collectées dans un espace de travail Log Analytics.

## <a name="data-collected"></a>Données collectées
Vous pouvez configurer un espace de travail Log Analytics pour qu’il collecte les types de données répertoriés dans le tableau suivant à partir de tous les agents connectés. Consultez [Quels sont les éléments supervisés par Azure Monitor ?](../monitor-reference.md) pour obtenir la liste des analyses, des solutions et d’autres solutions qui utilisent l’agent Log Analytics pour collecter d’autres types de données.

| source de données | Description |
| --- | --- |
| [Journaux d’événements Windows](data-sources-windows-events.md) | Informations envoyées au système de journalisation des événements Windows. |
| [Syslog](data-sources-syslog.md)                     | Informations envoyées au système de journalisation des événements Linux. |
| [Performances](data-sources-performance-counters.md)  | Valeurs numériques mesurant les performances de différents aspects du système d’exploitation et des charges de travail. |
| [Journaux d’activité IIS](data-sources-iis-logs.md)                 | Informations sur l’utilisation des sites web IIS s’exécutant sur le système d’exploitation invité. |
| [Journaux d’activité personnalisés](data-sources-custom-logs.md)           | Événements issus de fichiers texte sur des ordinateurs Windows et Linux. |

## <a name="data-destinations"></a>Destination des données
L’agent Log Analytics envoie des données à un espace de travail Log Analytics dans Azure Monitor. L’agent Windows peut être multirésident afin d’envoyer des données à plusieurs espaces de travail et groupes d’administration Operations Manager. L’agent Linux ne peut envoyer qu’à une seule destination.

## <a name="other-services"></a>Autres services
L’agent pour Linux et Windows ne sert pas seulement à la connexion à Azure Monitor. Il prend également en charge la connexion à Azure Automation pour héberger le rôle de travail Runbook Worker hybride, ainsi que d’autres services tels que [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/update-management/update-mgmt-overview.md) et [Azure Security Center](../../security-center/security-center-intro.md). Pour plus d’informations sur le rôle Runbook Worker hybride, consultez la page [Runbook Worker hybride d’Azure Automation](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Installation et configuration

Quand vous utilisez les agents Log Analytics pour collecter des données, vous devez comprendre ce qui suit pour planifier le déploiement de votre agent :

* Pour collecter des données à partir d’agents Windows, vous pouvez [configurer chaque agent pour qu’il rend compte à un ou plusieurs espaces de travail](agent-windows.md), même s’il rend compte à un groupe d’administration System Center Operations Manager. L’agent Windows peut rendre compte à quatre espaces de travail au maximum.
* L’agent Linux ne prend pas en charge l’hébergement multiple et peut uniquement rendre compte à un seul espace de travail.
* L’agent Windows prend en charge la [norme FIPS 140](/windows/security/threat-protection/fips-140-validation), ce qui n’est pas le cas de l’agent Linux.  

Si vous utilisez System Center Operations Manager 2012 R2 ou une version ultérieure :

* Chaque groupe d’administration Operations Manager peut être [connecté à un seul espace de travail](om-agents.md).
* Les ordinateurs Linux rendant compte à un groupe d’administration doivent être configurés pour rendre compte directement à un espace de travail Log Analytics. Si vos ordinateurs Linux rendent déjà compte directement à un espace de travail et que vous souhaitez les superviser avec Operations Manager, effectuez les étapes suivantes pour [qu’ils rendent compte à un groupe d’administration Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Vous pouvez installer l’agent Windows Log Analytics sur l’ordinateur Windows et faire en sorte qu’il rende compte à la fois à Operations Manager intégré à un espace de travail et à un autre espace de travail.


En fonction de vos besoins, il existe plusieurs méthodes pour installer l’agent Log Analytics et connecter votre ordinateur à Azure Monitor. Le tableau suivant décrit chacune d’entre elle, pour vous permettre d’identifier la plus adaptée à votre organisation.

|Source | Méthode | Description|
|-------|-------------|-------------|
|Azure VM| [Manuellement à partir du Portail Azure](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json) | Spécifiez les machines virtuelles à déployer à partir de l’espace de travail Log Analytics. |
| | Extension de machine virtuelle Log Analytics pour [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) à l’aide de l’interface de ligne de commande Azure ou d’un modèle Azure Resource Manager | L’extension installe l’agent Log Analytics sur les machines virtuelles Azure et les inscrit dans un espace de travail Azure Monitor existant. |
| | [Azure Monitor pour machines virtuelles](../insights/vminsights-enable-overview.md) | Lorsque vous activez la supervision avec Azure Monitor pour machines virtuelles, la solution installe l’extension et l’agent Log Analytics. |
| | [Approvisionnement automatique Azure Security Center](../../security-center/security-center-enable-data-collection.md) | Azure Security Center peut approvisionner l’agent Log Analytics sur toutes les machines virtuelles Azure prises en charge et sur toutes les nouvelles créées si vous l’autorisez à surveiller les menaces et les failles de sécurité. Si cette option est activée, les machines virtuelles nouvelles ou existantes sans agent installé seront provisionnées. |
| Ordinateur Windows hybride| [Installation manuelle](agent-windows.md) | Installez Microsoft Monitoring Agent à partir de la ligne de commande. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatisez l’installation à l’aide d’Azure Automation DSC. |
| | [Modèle Resource Manager avec Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Utilisez un modèle Azure Resource Manager si vous avez déployé Microsoft Azure Stack dans votre centre de données.| 
| Ordinateur Linux hybride| [Installation manuelle](../learn/quick-collect-linux-computer.md)|Installez l’agent pour Linux, qui appelle un script wrapper hébergé sur GitHub. | 
| System Center Operations Manager|[Intégration d’Operations Manager à Log Analytics](./om-agents.md) | Configurez l’intégration entre Operations Manager et les journaux Azure Monitor pour transférer les données collectées à partir d’ordinateurs Windows associés à un groupe d’administration.|  


## <a name="supported-windows-operating-systems"></a>Systèmes d’exploitation Windows pris en charge

Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour l’agent Windows :

* Windows Server 2019
* Windows Server 2016, versions 1709 et 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 entreprise (y compris multi-sessions) et Pro
* Windows 8 Entreprise et Professionnel 
* Windows 7 SP1

>[!NOTE]
>Tandis que l’agent Log Analytics pour Windows a été conçu pour prendre en charge les scénarios d’analyse de serveur, nous savons que vous pouvez exécuter le client Windows pour prendre en charge les charges de travail configurées et optimisées pour le système d’exploitation serveur. L’agent ne prend pas en charge le client Windows, mais nos solutions de surveillance ne concernent pas les scénarios d’analyse du client, sauf mention explicite.

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

Cette section fournit des détails sur les distributions Linux prises en charge.

À partir des versions publiées après août 2018, nous apportons les changements suivants à notre modèle de prise en charge :  

* Seules les versions serveur sont prises en charge, pas les versions client.  
* Nous concentrons la prise en charge sur les [distributions approuvées Azure Linux](../../virtual-machines/linux/endorsed-distros.md). Notez qu’il peut y avoir un certain délai entre la sortie d’une nouvelle distribution ou version approuvée par Azure Linux, et sa prise en charge pour l’agent Log Analytics Linux.
* Toutes les versions mineures de chaque version majeure listée sont prises en charge.
* Les versions qui ont dépassé la date de fin de support de leur fabricant ne sont pas prises en charge.  
* Les nouvelles versions d’AMI ne sont pas prises en charge.  
* Seules les versions qui exécutent SSL 1.x par défaut sont prises en charge.

>[!NOTE]
>Si vous utilisez une distribution ou une version qui n’est pas prise en charge actuellement et qui ne s’aligne pas sur notre modèle de prise en charge, nous vous recommandons de dupliquer ce dépôt, en sachant que le support de Microsoft ne fournira aucune assistance sur les versions d’agent dupliqué.


### <a name="python-2-requirement"></a>Exigence relative à Python 2
 L’agent Log Analytics nécessite Python 2. Si votre machine virtuelle utilise une distribution qui n’inclut pas Python 2 par défaut, vous devez l’installer. Les exemples de commandes suivants installent Python 2 sur différentes distributions.

 - Red Hat, CentOS, Oracle : `yum install -y python2`
 - Ubuntu, Debian : `apt-get install -y python2`
 - SUSE : `zypper install -y python2`

L’exécutable python2 doit être affecté de l’alias « python » à l’aide de la commande suivante :

```
alternatives --set python `which python2`
```

### <a name="supported-distros"></a>Distributions prises en charge

Les versions suivantes du système d’exploitation Linux sont officiellement prises en charge pour l’agent Linux :

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x64) et 7 (x64)  
* Oracle Linux 6 et 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64/x64), 7 et 8 (x64)
* Debian GNU/Linux 8 et 9 (x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x64) et 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) et 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 est uniquement pris en charge sur les plateformes x86_x64 (64 bits), tandis qu’OpenSSL antérieur à 1.x n’est pris en charge sur aucune plateforme.
>

### <a name="agent-prerequisites"></a>Conditions préalables associées à l’agent

Le tableau suivant répertorie les packages requis pour les distributions Linux prises en charge sur lesquelles l’agent est installé.

|Package requis |Description |Version minimale |
|-----------------|------------|----------------|
|Glibc |    Bibliothèque C de GNU | 2.5-12 
|Openssl    | Bibliothèques OpenSSL | 1.0.x ou 1.1.x |
|Curl | Client web cURL | 7.15.5 |
|Python | | 2.6+ ou 3.3+
|Python-ctypes | | 
|PAM | Modules d’authentification enfichable | | 

>[!NOTE]
>rsyslog ou syslog-ng est requis pour collecter les messages syslog. Le démon syslog par défaut sur la version 5 de Red Hat Enterprise Linux, CentOS et Oracle Linux (sysklog) ne prend pas en charge la collecte des événements syslog. Pour collecter les données syslog avec cette version de ces distributions, le démon rsyslog doit être installé et configuré à la place de sysklog.

## <a name="tls-12-protocol"></a>Protocole TLS 1.2

Pour garantir la sécurité des données en transit vers les journaux d’activité Azure Monitor, nous vous encourageons vivement à configurer l’agent de façon à utiliser au moins le protocole TLS (Transport Layer Security) 1.2. Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**.  Pour plus d’informations, passez en revue [Envoi sécurisé de données via TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Conditions de prise en charge de la signature de code SHA-2 pour Windows
À compter du 17 août 2020, l’agent Windows utilisera exclusivement la signature SHA-2. Ce changement aura un impact sur les clients qui utilisent l’agent Log Analytics sur un système d’exploitation hérité dans le cadre d’un service Azure (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). Le client n’aura rien à faire à moins que l’agent s’exécute sur une version héritée du système d’exploitation (Windows 7, Windows Server 2008 R2 et Windows Server 2008). Les clients utilisant une version héritée du système d’exploitation sont tenus d’effectuer les actions suivantes sur leurs ordinateurs avant le 17 août 2020. À défaut, leurs agents cesseront d’envoyer les données à leurs espaces de travail Log Analytics :

1. Installer le dernier Service Pack du système d’exploitation. Les versions de Service Pack nécessaires sont les suivantes :
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installer les mises à jour Windows de signature SHA-2 pour le système d’exploitation en question, comme décrit dans [Obligation de prise en charge de la signature du code SHA-2 2019 pour Windows et WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
3. Effectuer une mise à jour vers la dernière version de l’agent Windows (version 10.20.18029).
4. Configurer l’agent de sorte qu’il [utilise TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12) (recommandé). 


## <a name="network-requirements"></a>Configuration requise pour le réseau
L’agent pour Linux et Windows communique en sortie avec le service Azure Monitor via le port TCP 443. Si la machine se connecte via un pare-feu ou un serveur proxy pour communiquer sur Internet, consultez les exigences ci-dessous pour connaître la configuration réseau nécessaire. Si vos stratégies de sécurité informatique n’autorisent pas les ordinateurs du réseau à se connecter à Internet, vous pouvez configurer une [passerelle Log Analytics](gateway.md), puis configurer l’agent pour qu’il se connecte aux journaux Azure Monitor via la passerelle. L’agent peut ensuite recevoir des informations de configuration et envoyer les données collectées en fonction des règles de collecte de données et des solutions de surveillance que vous avez activées dans votre espace de travail.

![Schéma de communication de l’agent Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Le tableau suivant répertorie les informations de configuration du proxy et du pare-feu requises pour permettre aux agents Linux et Windows de communiquer avec les journaux d’activité Azure Monitor.

### <a name="firewall-requirements"></a>Configuration requise du pare-feu

|Ressource de l'agent|Ports |Sens |Ignorer l’inspection HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Règle de trafic sortant|Oui |  
|*.oms.opinsights.azure.com |Port 443 |Règle de trafic sortant|Oui |  
|*.blob.core.windows.net |Port 443 |Règle de trafic sortant|Oui |
|\* .azure-automation.net |Port 443 |Règle de trafic sortant|Oui |

Pour obtenir les informations relatives au pare-feu nécessaires pour Azure Government, consultez [Azure Government Monitoring + Management](../../azure-government/compare-azure-government-global-azure.md#azure-monitor-logs). 

Si vous envisagez d’utiliser le Runbook Worker hybride Azure Automation pour vous connecter et vous inscrire auprès du service Automation afin d’utiliser des runbooks et des solutions de gestion dans votre environnement, il doit avoir accès au numéro de port et aux URL décrites dans la section [Configurer votre réseau pour le Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Configuration du proxy

L’agent Windows et Linux prend en charge la communication par l’intermédiaire d’un serveur proxy ou de la passerelle Log Analytics vers Azure Monitor, au moyen du protocole HTTPS.  L’authentification anonyme et l’authentification de base (nom d’utilisateur/mot de passe) sont prises en charge.  Pour l’agent Windows connecté directement au service, la configuration du proxy est indiquée pendant l’installation ou [après le déploiement](agent-manage.md#update-proxy-settings), dans le panneau de configuration ou avec PowerShell.  

Pour l’agent Linux, le serveur proxy est indiqué pendant ou [après l’installation](agent-manage.md#update-proxy-settings) en modifiant le fichier de configuration du fichier proxy.conf.  La valeur de configuration du proxy de l’agent Linux adopte la syntaxe suivante :

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Si votre serveur proxy ne requiert pas d’authentification, l’agent Linux oblige en revanche à saisir un pseudo utilisateur/mot de passe. Il peut s’agir de n’importe quel nom d’utilisateur ou mot de passe.

|Propriété| Description |
|--------|-------------|
|Protocol | https |
|utilisateur | Nom d’utilisateur facultatif pour l’authentification du proxy |
|mot de passe | Mot de passe facultatif pour l’authentification du proxy |
|proxyhost | Adresse ou nom de domaine complet du serveur proxy/de la passerelle Log Analytics |
|port | Numéro de port facultatif pour le serveur proxy/la passerelle Log Analytics |

Par exemple : `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Si vous utilisez des caractères spéciaux comme « \@ » dans votre mot de passe, vous obtenez une erreur de connexion au proxy, car la valeur est mal analysée.  Pour contourner ce problème, encodez le mot de passe dans l’URL à l’aide d’un outil comme [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Étapes suivantes

* Consultez les [sources de données](agent-data-sources.md) pour connaître les sources de données disponibles pour collecter des données sur votre système Windows ou Linux. 
* Découvrez les [requêtes dans les journaux](../log-query/log-query-overview.md) pour analyser les données collectées à partir de sources de données et de solutions. 
* Découvrez les [solutions de surveillance](../insights/solutions.md) qui ajoutent des fonctionnalités à Azure Monitor et collectent également des données dans l’espace de travail Log Analytics.

