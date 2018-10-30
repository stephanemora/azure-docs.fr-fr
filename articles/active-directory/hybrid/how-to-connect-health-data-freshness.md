---
title: Azure AD Connect Health - Alerte indiquant que les données du Service de contrôle d’intégrité ne sont pas mises à jour | Microsoft Docs
description: Ce document décrit la cause de l’alerte indiquant que les données du Service de contrôle d’intégrité ne sont pas mises à jour et explique comment y remédier.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: e470a44732b881311eacecfdf2bd2211598d880a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984858"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Les données du Service de contrôle d’intégrité ne sont pas mises à jour

## <a name="overview"></a>Vue d’ensemble
<li>Le service Azure AD Connect Health génère une alerte d’actualisation des données lorsqu’il ne reçoit pas tous les points de données de la part du serveur pendant deux heures. Le titre de l’alerte est **Les données du Service de contrôle d’intégrité ne sont pas mises à jour**. </li>
<li>L’alerte d’état **Avertissement** se déclenche si Connect Health ne reçoit pas d’éléments de données partiels de la part du serveur pendant deux heures. L’alerte d’état Avertissement ne déclenche pas de notifications par e-mail à l’administrateur des clients. </li>
<li>L’alerte d’état **Erreur** se déclenche si Connect Health ne reçoit pas du tout d’éléments de données de la part du serveur pendant deux heures. L’alerte d’état Erreur déclenche des notifications par e-mail à l’administrateur des clients. </li>

>[!IMPORTANT] 
> Cette alerte suit la [stratégie de conservation des données](reference-connect-health-user-privacy.md#data-retention-policy) de Connect Health.

## <a name="troubleshooting-steps"></a>Étapes de dépannage 
* N’oubliez pas de prendre connaissance et de respecter la [section Configuration requise](how-to-connect-health-agent-install.md#requirements).
* Utilisez l’[outil de test de connectivité](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) pour découvrir les problèmes de connectivité.
* Si vous avez un serveur proxy HTTP, suivez les [étapes de configuration ici](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-adfs"></a>Connect Health pour ADFS
Étapes supplémentaires pour valider pour AD FS et suivre le workflow dans l’[aide AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

### <a name="data-collection-map-required-steps"></a>Étapes requises pour le mappage de collecte de données
| Nom du service | Éléments de données | Étapes de dépannage |
| --- | --- | --- | 
| Connect Health pour AD FS | PerfCounter, TestResult | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L’inspection SSL pour le trafic sortant est filtrée ou désactivée](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Autoriser les sites web désignés en cas d’activation de la sécurité renforcée d’IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | Adfs-UsageMetrics | Connectivité sortante basée sur des adresses IP, reportez-vous à [Plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=41653) | 
| Connect Health pour la synchronisation | PerfCounter | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L’inspection SSL pour le trafic sortant est filtrée ou désactivée](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Autoriser les sites web désignés en cas d’activation de la sécurité renforcée d’IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - Connectivité sortante basée sur des adresses IP, reportez-vous à [Plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 
| Connect Health pour ADDS  | PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [L’inspection SSL pour le trafic sortant est filtrée ou désactivée](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Autoriser les sites web désignés en cas d’activation de la sécurité renforcée d’IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) <br />  - Connectivité sortante basée sur des adresses IP, reportez-vous à [Plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=41653)  |




## <a name="next-steps"></a>Étapes suivantes
* [Forum Aux Questions (FAQ) Azure AD Connect Health](reference-connect-health-faq.md)
