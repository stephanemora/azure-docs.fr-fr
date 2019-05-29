---
title: Connecter des sources de données à la préversion d’Azure Sentinel| Microsoft Docs
description: Découvrez comment connecter des sources de données à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5d8d97aefa2efadae70c976b8a1876b2c7d8f1c1
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921925"
---
# <a name="connect-data-sources"></a>Connecter des sources de données

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Pour intégrer Azure Sentinel, vous devez d’abord vous connecter à vos sources de données. Azure Sentinel est fourni avec plusieurs connecteurs pour les solutions Microsoft, disponibles par défaut et offrant une intégration en temps réel, y compris les solutions Microsoft Threat Protection, et les sources Microsoft 365, dont Office 365, Azure AD, Azure ATP et Microsoft Cloud App Security, et bien plus encore. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format d’événement commun, Syslog ou l’API REST pour connecter vos sources de données à Azure Sentinel.  

![Collecteurs de données](./media/collect-data/collect-data-page.png)

## <a name="data-connection-methods"></a>Méthodes de connexion de données

Les méthodes de connexion de données suivantes sont prises en charge par Azure Sentinel :

- **Services Microsoft** :<br> Les services Microsoft sont connectés en mode natif. En tirant parti de la base Azure une intégration prête à l’emploi, les solutions suivantes peuvent être connectées en quelques clics :
    - [Office 365](connect-office-365.md)
    - [Journaux d’audit et infos d’identification Azure AD](connect-azure-active-directory.md)
    - [Activité Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Centre de sécurité Azure](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Événements de sécurité Windows](connect-windows-security-events.md) 
    - [Pare-feu Windows](connect-windows-firewall.md)

- **Solutions externes via API** : Certaines sources de données sont connectées à l’aide d’API fournies par la source de données connectée. En général, la plupart des technologies de sécurité fournissent un ensemble d’API par le biais duquel les journaux des événements peuvent être récupérés. Les API se connectent à Azure Sentinel, collectent des types de données spécifiques et les envoient à Azure Log Analytics. Les appliances connectées via une API sont les suivantes :
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Solutions externes via un agent** : Sentinel Azure peut être connecté à toute source de données capable d’effectuer une diffusion en continu de journal en temps réel à l’aide du protocole Syslog, via un agent. <br>La plupart des appliances utilisent le protocole Syslog pour envoyer des messages d’événement incluant le journal proprement dit et des données sur le journal. Le format des journaux varie, mais la plupart des appliances prennent en charge la norme CEF (Common Event Format). <br>L’agent Sentinel Azure, qui est basé sur Microsoft Monitoring Agent, convertit les journaux de format CEF dans un format que Log Analytics peut ingérer. Selon le type d’appliance, l’agent est installé directement sur l’appliance ou sur un serveur Linux dédié. L’agent pour Linux reçoit des événements du démon Syslog en utilisant le protocole UDP mais, dans les cas où une machine Linux est supposée collecter un volume important d’événements Syslog, ceux-ci sont envoyés, en utilisant le protocole TCP, du démon Syslog à l’agent, puis de celui-ci à Log Analytics.
    - Pare-feu, proxys et points de terminaison :
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Autres appliances CEF](connect-common-event-format.md)
        - [Autres appliances Syslog](connect-syslog.md)
    - Solutions DLP
    - [Fournisseurs de renseignement sur les menaces](connect-threat-intelligence.md)
    - [Machines DNS](connect-dns.md) : agent installé directement sur la machine DNS
    - Serveurs Linux
    - Autres clouds
    
## Options de connexion de l’agent<a name="agent-options"></a>

Pour connecter votre appliance externe à Azure Sentinel, l’agent doit être déployé sur une machine dédiée (machine virtuelle ou ordinateur local) pour prendre en charge la communication entre l’appliance et Azure Sentinel. Vous pouvez déployer l’agent manuellement ou automatiquement. Le déploiement automatique n’est disponible que si votre machine dédiée est une nouvelle machine virtuelle que vous créez dans Azure. 


![CEF dans Azure](./media/connect-cef/cef-syslog-azure.png)

Vous pouvez également déployer l’agent manuellement sur une machine virtuelle Azure existante, sur une machine virtuelle dans un autre cloud, ou sur un ordinateur local.

![CEF local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser Azure Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Azure Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces potentielles](quickstart-get-visibility.md).
