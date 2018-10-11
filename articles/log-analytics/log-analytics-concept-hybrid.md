---
title: Collecter des données dans un environnement hybride avec l’agent Azure Log Analytics | Microsoft Docs
description: Cette rubrique aide à comprendre comment collecter des données et effectuer le monitoring d’ordinateurs hébergés dans un environnement local ou tout autre environnement cloud avec Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 43f077ef07597604eaf42cb4af47cbc2f0e6c524
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042001"
---
# <a name="collect-data-in-a-hybrid-environment-with-log-analytics-agent"></a>Collecter des données dans un environnement hybride avec l’agent Log Analytics

Azure Log Analytics peut collecter et exploiter des données provenant d’ordinateurs Windows ou Linux et se trouvant sur :

* des [machines virtuelles](log-analytics-quick-collect-azurevm.md) avec l’extension Log Analytics pour machines virtuelles ; 
* votre centre de données en tant que serveurs physiques ou que machines virtuelles ;
* des machines virtuelles dans un service hébergé dans le cloud, comme Amazon Web Services (AWS).

Les ordinateurs hébergés dans l’environnement peuvent être connectés directement à Log Analytics. Si vous effectuez déjà la surveillance de ces ordinateurs avec System Center Operations Manager 2012 R2 ou ultérieur, vous pouvez également intégrer votre groupe d’administration Operations Manager à Log Analytics et continuer à gérer vos processus d’opérations de service informatique.  

## <a name="overview"></a>Vue d’ensemble

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Avant d’analyser et d’exploiter les données collectées, vous devez commencer par installer et connecter les agents de tous les ordinateurs qui devront envoyer des données au service Log Analytics. Pour installer des agents sur vos ordinateurs locaux, vous pouvez utiliser le programme d’installation, la ligne de commande ou la fonction Desired State Configuration (DSC) d’Azure Automation. 

L’agent pour Linux et Windows communique en sortie avec le service Log Analytics via le port TCP 443. Si l’ordinateur se connecte à un pare-feu ou à un serveur proxy pour communiquer sur Internet, consultez les exigences ci-dessous pour connaître la configuration réseau nécessaire.  Si vos stratégies de sécurité informatique n’autorisent pas les ordinateurs du réseau à se connecter à Internet, vous pouvez configurer une [passerelle OMS](log-analytics-oms-gateway.md), puis configurer l’agent pour qu’il se connecte à Log Analytics via la passerelle. L’agent peut ensuite recevoir des informations de configuration et envoyer les données collectées en fonction des règles de collecte de données et des solutions que vous avez activées. 

Si vous effectuez le monitoring de l’ordinateur avec System Center Operations Manager 2012 R2 ou ultérieur, il peut être multihébergé avec le service Log Analytics pour collecter des données et les transférer au service, tandis [qu’Operations Manager](log-analytics-om-agents.md) poursuit le monitoring. Les ordinateurs Linux analysés par un groupe d’administration Operations Manager intégré à Log Analytics ne reçoivent pas de configuration des sources de données et transmettent les données collectées par l’intermédiaire du groupe d’administration. L’agent Windows peut envoyer des rapports à quatre espaces de travail, contre un seul pour l’agent Linux.  

L’agent pour Linux et Windows ne sert pas seulement à la connexion à Log Analytics. Il prend également en charge la connexion à Azure Automation pour héberger le rôle Runbook Worker hybride, ainsi que des solutions de gestion telles que Change Tracking et Update Management.  Pour plus d’informations sur le rôle Runbook Worker hybride, consultez la page [Runbook Worker hybride d’Azure Automation](../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Systèmes d’exploitation Windows pris en charge
Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour l’agent Windows :

* Windows Server 2008 Service Pack 1 (SP1) (ou version ultérieure) ;
* Windows 7 SP1 (ou version ultérieure).

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge
Les distributions Linux suivantes sont officiellement prises en charge.  Toutefois, l’agent Linux peut également s’exécuter sur d’autres distributions, qui ne se trouvent pas dans la liste.  Sauf indication contraire, toutes les versions mineures sont prises en charge pour chaque version majeure répertoriée.  

* Amazon Linux 2012.09 à 2015.09 (x86/x64)
* CentOS Linux 5, 6 et 7 (x86/x64)  
* Oracle Linux 5, 6 et 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 et 7 (x86/x64)
* Debian GNU/Linux 6, 7 et 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 et 12 (x86/x64)

## <a name="tls-12-protocol"></a>Protocole TLS 1.2
Pour garantir la sécurité des données en transit vers Log Analytics, nous vous encourageons vivement à configurer l’agent de façon à utiliser au moins Transport Layer Security (TLS) 1.2. Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**.  Pour plus d’informations, passez en revue [Envoi sécurisé de données via TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Configuration requise du pare-feu réseau
Voici la liste des informations de configuration du proxy et du pare-feu requises pour permettre à l’agent Linux et Windows de communiquer avec Log Analytics.  

|Ressource de l'agent|Ports |Direction |Ignorer l’inspection HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Trafic entrant et sortant|Oui |  
|*.oms.opinsights.azure.com |Port 443 |Trafic entrant et sortant|Oui |  
|*.blob.core.windows.net |Port 443 |Trafic entrant et sortant|Oui |  
|* .azure-automation.net |Port 443 |Trafic entrant et sortant|Oui |  


Si vous envisagez d’utiliser le Runbook Worker hybride Azure Automation pour vous connecter et vous inscrire auprès du service Automation afin d’utiliser les runbooks dans votre environnement, il doit avoir accès au numéro de port et aux URL décrites dans la section [Configurer votre réseau pour le Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md#network-planning). 

L’agent Windows et Linux prend en charge la communication par l’intermédiaire d’un serveur proxy ou de la passerelle OMS avec le service Log Analytics, au moyen du protocole HTTPS.  L’authentification anonyme et l’authentification de base (nom d’utilisateur/mot de passe) sont prises en charge.  Pour l’agent Windows connecté directement au service, la configuration du proxy est indiquée pendant l’installation ou [après le déploiement](log-analytics-agent-manage.md#update-proxy-settings), dans le panneau de configuration ou avec PowerShell.  

Pour l’agent Linux, le serveur proxy est indiqué pendant ou [après l’installation](log-analytics-agent-manage.md#update-proxy-settings) en modifiant le fichier de configuration du fichier proxy.conf.  La valeur de configuration du proxy de l’agent Linux adopte la syntaxe suivante :

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Si votre serveur proxy ne requiert pas d’authentification, l’agent Linux oblige en revanche à saisir un pseudo utilisateur/mot de passe. Il peut s’agir de n’importe quel nom d’utilisateur ou mot de passe.

|Propriété| Description |
|--------|-------------|
|Protocole | https |
|user | Nom d’utilisateur facultatif pour l’authentification du proxy |
|password | Mot de passe facultatif pour l’authentification du proxy |
|proxyhost | Adresse ou nom de domaine complet du serveur proxy/de la passerelle OMS |
|port | Numéro de port facultatif pour le serveur proxy/la passerelle OMS |

Par exemple : `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Si des caractères spéciaux, par exemple « \@ », sont utilisés dans le mot de passe, il se produit une erreur de connexion au proxy, car la valeur est mal analysée.  Pour contourner ce problème, encodez le mot de passe dans l’URL à l’aide d’un outil comme [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installer et configurer l’agent 
Pour connecter directement des ordinateurs locaux avec Log Analytics, différentes méthodes existent en fonction des besoins. Le tableau suivant décrit chacune d’entre elle, pour vous permettre d’identifier la plus adaptée à votre organisation.

|Source | Méthode | Description|
|-------|-------------|-------------|
| Ordinateur Windows|- [Installation manuelle](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modèle Resource Manager avec Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installez Microsoft Monitoring Agent en ligne de commande ou suivant une méthode automatisée, par exemple, Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), ou avec un modèle Azure Resource Manager si vous avez déployé Microsoft Azure Stack dans votre centre de données.| 
|Ordinateur Linux| [Installation manuelle](log-analytics-quick-collect-linux-computer.md)|Installez l’agent pour Linux, qui appelle un script wrapper hébergé sur GitHub. | 
| System Center Operations Manager|[Intégration d’Operations Manager à Log Analytics](log-analytics-om-agents.md) | Configurez l’intégration entre Operations Manager et Log Analytics pour transférer les données collectées à partir d’ordinateurs Linux et Windows associés à un groupe d’administration.|  

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [sources de données](log-analytics-data-sources.md) pour connaître les sources de données disponibles pour collecter des données sur votre système Windows ou Linux. 

* Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions. 

* Découvrez les [solutions](log-analytics-add-solutions.md) qui ajoutent des fonctionnalités à Log Analytics et collectent également des données dans le référentiel OMS.
