---
title: Collecter des données de journal avec l'agent Azure Log Analytics | Microsoft Docs
description: Cette rubrique aide à comprendre comment collecter des données et effectuer le monitoring d’ordinateurs hébergés dans un environnement Azure, local ou tout autre environnement cloud avec Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 57e560c52c9a8f10586c31231bcc9d6acc667558
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019534"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Collecter des données de journal avec l’agent Log Analytics

L'agent Azure Log Analytics, précédemment appelé Microsoft Monitoring Agent (MMA) ou agent OMS Linux, a été développé fournir une gestion complète sur plusieurs machines locales, ordinateurs surveillés par [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) et machines virtuelles situées dans un cloud. Les agents Windows et Linux accèdent à Azure Monitor et stockent des données de journaux collectées à partir de différentes sources dans votre espace de travail Log Analytics, ainsi que des journaux ou métriques uniques tels que définis dans une solution de surveillance. 

Cet article propose une présentation détaillée des exigences en matière d'agent, de système et de réseau, ainsi que des différentes méthodes de déploiement.

## <a name="overview"></a>Vue d’ensemble

![Schéma de communication de l’agent Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Avant d’analyser et d’exploiter les données collectées, vous devez commencer par installer et connecter les agents de toutes les machines qui devront envoyer des données au service Azure Monitor. Vous pouvez installer des agents sur vos machines virtuelles Azure à l’aide de l’extension de machine virtuelle Log Analytics pour Windows, et pour les machines d'un environnement hybride à l’aide du programme d’installation, de la ligne de commande ou avec DSC (Desired State Configuration) dans Azure Automation. 

L’agent pour Linux et Windows communique en sortie avec le service Azure Monitor via le port TCP 443. Si la machine se connecte via un pare-feu ou un serveur proxy pour communiquer sur Internet, consultez les exigences ci-dessous pour connaître la configuration réseau nécessaire. Si vos stratégies de sécurité informatique n’autorisent pas les ordinateurs du réseau à se connecter à Internet, vous pouvez configurer une [passerelle Log Analytics](gateway.md), puis configurer l’agent pour qu’il se connecte aux journaux Azure Monitor via la passerelle. L’agent peut ensuite recevoir des informations de configuration et envoyer les données collectées en fonction des règles de collecte de données et des solutions de surveillance que vous avez activées dans votre espace de travail. 

Quand vous utilisez les agents Log Analytics pour collecter des données, vous devez comprendre ce qui suit pour planifier le déploiement de votre agent :

* Pour collecter des données à partir d’agents Windows, vous pouvez [configurer chaque agent pour qu’il rend compte à un ou plusieurs espaces de travail](agent-windows.md), même s’il rend compte à un groupe d’administration System Center Operations Manager. L’agent Windows peut rendre compte à quatre espaces de travail au maximum.
* L’agent Linux ne prend pas en charge l’hébergement multiple et peut uniquement rendre compte à un seul espace de travail.
* L’agent Windows prend en charge la [norme FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), ce qui n’est pas le cas de l’agent Linux.  

Si vous utilisez System Center Operations Manager 2012 R2 ou une version ultérieure :

* Chaque groupe d’administration Operations Manager peut être [connecté à un seul espace de travail](om-agents.md).
* Les ordinateurs Linux rendant compte à un groupe d’administration doivent être configurés pour rendre compte directement à un espace de travail Log Analytics. Si vos ordinateurs Linux rendent déjà compte directement à un espace de travail et que vous souhaitez les superviser avec Operations Manager, effectuez les étapes suivantes pour [qu’ils rendent compte à un groupe d’administration Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Vous pouvez installer l’agent Windows Log Analytics sur l’ordinateur Windows et faire en sorte qu’il rende compte à la fois à Operations Manager intégré à un espace de travail et à un autre espace de travail.

L’agent pour Linux et Windows ne sert pas seulement à la connexion à Azure Monitor. Il prend également en charge la connexion à Azure Automation pour héberger le rôle de travail Runbook Worker hybride, ainsi que d’autres services tels que [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md) et [Azure Security Center](../../security-center/security-center-intro.md). Pour plus d’informations sur le rôle Runbook Worker hybride, consultez la page [Runbook Worker hybride d’Azure Automation](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Systèmes d’exploitation Windows pris en charge

Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour l’agent Windows :

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, version 1709 et 1803
* Windows 7 SP1, Windows 8 Enterprise et Pro, et Windows 10 Enterprise et Pro

>[!NOTE]
>Tandis que l’agent Log Analytics pour Windows a été conçu pour prendre en charge les scénarios d’analyse de serveur, nous savons que vous pouvez exécuter le client Windows pour prendre en charge les charges de travail configurées et optimisées pour le système d’exploitation serveur. L’agent ne prend pas en charge le client Windows, mais nos solutions de surveillance ne concernent pas les scénarios d’analyse du client, sauf mention explicite.

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

Cette section fournit des détails sur les distributions Linux prises en charge.

À partir des versions publiées après août 2018, nous apportons les changements suivants à notre modèle de prise en charge :  

* Seules les versions serveur sont prises en charge, pas les versions client.  
* Les nouvelles versions des [distributions approuvées de Linux Azure](../../virtual-machines/linux/endorsed-distros.md) sont toujours prises en charge.  
* Toutes les versions mineures de chaque version majeure listée sont prises en charge.
* Les versions qui ont dépassé la date de fin de support de leur fabricant ne sont pas prises en charge.  
* Les nouvelles versions d’AMI ne sont pas prises en charge.  
* Seules les versions qui exécutent SSL 1.x par défaut sont prises en charge.

>[!NOTE]
>Si vous utilisez une distribution ou une version qui n’est pas prise en charge actuellement et qui ne s’aligne pas sur notre modèle de prise en charge, nous vous recommandons de dupliquer ce dépôt, en sachant que le support de Microsoft ne fournira aucune assistance sur les versions d’agent dupliqué.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) et 7 (x64)  
* Oracle Linux 6 et 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) et 7 (x64)
* Debian GNU/Linux 8 et 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) et 18.04 LTS (x64)
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
|Python-ctypes | | 
|PAM | Modules d’authentification enfichable | | 

>[!NOTE]
>rsyslog ou syslog-ng est requis pour collecter les messages syslog. Le démon syslog par défaut sur la version 5 de Red Hat Enterprise Linux, CentOS et Oracle Linux (sysklog) ne prend pas en charge la collecte des événements syslog. Pour collecter les données syslog avec cette version de ces distributions, le démon rsyslog doit être installé et configuré à la place de sysklog.

## <a name="tls-12-protocol"></a>Protocole TLS 1.2

Pour garantir la sécurité des données en transit vers les journaux Azure Monitor, nous vous encourageons vivement à configurer l’agent de façon à utiliser au moins Transport Layer Security (TLS) 1.2. Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**.  Pour plus d’informations, passez en revue [Envoi sécurisé de données via TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Configuration requise du pare-feu réseau

Voici la liste des informations de configuration du proxy et du pare-feu requises pour permettre à l’agent Linux et Windows de communiquer avec les journaux Azure Monitor.  

|Ressource de l'agent|Ports |Sens |Ignorer l’inspection HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Règle de trafic sortant|Oui |  
|*.oms.opinsights.azure.com |Port 443 |Règle de trafic sortant|Oui |  
|*.blob.core.windows.net |Port 443 |Règle de trafic sortant|Oui |  

Pour obtenir les informations relatives au pare-feu nécessaires pour Azure Government, consultez [Azure Government Monitoring + Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Si vous envisagez d’utiliser le Runbook Worker hybride Azure Automation pour vous connecter et vous inscrire auprès du service Automation afin d’utiliser des runbooks et des solutions de gestion dans votre environnement, il doit avoir accès au numéro de port et aux URL décrites dans la section [Configurer votre réseau pour le Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

L’agent Windows et Linux prend en charge la communication par l’intermédiaire d’un serveur proxy ou de la passerelle Log Analytics vers Azure Monitor, au moyen du protocole HTTPS.  L’authentification anonyme et l’authentification de base (nom d’utilisateur/mot de passe) sont prises en charge.  Pour l’agent Windows connecté directement au service, la configuration du proxy est indiquée pendant l’installation ou [après le déploiement](agent-manage.md#update-proxy-settings), dans le panneau de configuration ou avec PowerShell.  

Pour l’agent Linux, le serveur proxy est indiqué pendant ou [après l’installation](agent-manage.md#update-proxy-settings) en modifiant le fichier de configuration du fichier proxy.conf.  La valeur de configuration du proxy de l’agent Linux adopte la syntaxe suivante :

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Si votre serveur proxy ne requiert pas d’authentification, l’agent Linux oblige en revanche à saisir un pseudo utilisateur/mot de passe. Il peut s’agir de n’importe quel nom d’utilisateur ou mot de passe.

|Propriété| Description |
|--------|-------------|
|Protocol | https |
|utilisateur | Nom d’utilisateur facultatif pour l’authentification du proxy |
|password | Mot de passe facultatif pour l’authentification du proxy |
|proxyhost | Adresse ou nom de domaine complet du serveur proxy/de la passerelle Log Analytics |
|port | Numéro de port facultatif pour le serveur proxy/la passerelle Log Analytics |

Par exemple : `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Si des caractères spéciaux, par exemple « \@ », sont utilisés dans le mot de passe, il se produit une erreur de connexion au proxy, car la valeur est mal analysée.  Pour contourner ce problème, encodez le mot de passe dans l’URL à l’aide d’un outil comme [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installer et configurer l’agent

Selon vos besoins, plusieurs méthodes vous permettent de connecter des machines de votre abonnement Azure ou d’un environnement hybride directement à des journaux Azure Monitor. Le tableau suivant décrit chacune d’entre elle, pour vous permettre d’identifier la plus adaptée à votre organisation.

|Source | Méthode | Description|
|-------|-------------|-------------|
|Azure VM| - Extension de machine virtuelle Log Analytics pour [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) à l’aide de l'interface de ligne de commande Azure ou d'un modèle Azure Resource Manager<br>- [Manuellement à partir du portail Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Provisionnement automatique Azure Security Center](../../security-center/security-center-enable-data-collection.md)| - L’extension installe l’agent Log Analytics sur les machines virtuelles Azure et les inscrit dans un espace de travail Azure Monitor existant.<br>- Azure Security Center peut provisionner l’agent Log Analytics sur toutes les machines virtuelles Azure prises en charge et sur toutes les nouvelles créées si vous l’autorisez à surveiller les menaces et les failles de sécurité. Si cette option est activée, les machines virtuelles nouvelles ou existantes sans agent installé seront provisionnées.|
| Ordinateur Windows hybride|- [Installation manuelle](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modèle Resource Manager avec Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installez Microsoft Monitoring Agent à partir de la ligne de commande ou à l’aide d’une méthode automatisée, comme Azure Automation DSC, [Configuration Manager](https://docs.microsoft.com/configmgr/apps/deploy-use/deploy-applications), ou avec un modèle Azure Resource Manager si vous avez déployé Microsoft Azure Stack dans votre centre de données.| 
| Ordinateur Linux hybride| [Installation manuelle](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installez l’agent pour Linux, qui appelle un script wrapper hébergé sur GitHub. | 
| System Center Operations Manager|[Intégration d’Operations Manager à Log Analytics](om-agents.md) | Configurez l’intégration entre Operations Manager et les journaux Azure Monitor pour transférer les données collectées à partir d’ordinateurs Windows associés à un groupe d’administration.|  

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [sources de données](agent-data-sources.md) pour connaître les sources de données disponibles pour collecter des données sur votre système Windows ou Linux. 

* Découvrez les [requêtes dans les journaux](../log-query/log-query-overview.md) pour analyser les données collectées à partir de sources de données et de solutions. 

* Découvrez les [solutions de surveillance](../insights/solutions.md) qui ajoutent des fonctionnalités à Azure Monitor et collectent également des données dans l’espace de travail Log Analytics.
