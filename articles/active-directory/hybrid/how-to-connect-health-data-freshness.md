---
title: Azure AD Connect Health - Alerte indiquant que les données du Service de contrôle d’intégrité ne sont pas mises à jour | Microsoft Docs
description: Ce document décrit la cause de l’alerte indiquant que les données du Service de contrôle d’intégrité ne sont pas mises à jour et explique comment y remédier.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429009"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Les données du Service de contrôle d’intégrité ne sont pas mises à jour

## <a name="overview"></a>Vue d’ensemble
Les agents des machines locales surveillées par Azure AD Connect Health transfèrent régulièrement des données vers le service Azure AD Connect Health. Si le service ne reçoit pas de données émanant d’un agent, les informations présentées dans le portail sont obsolètes. Pour mettre en évidence le problème, le service déclenche une alerte **Les données du service de contrôle d’intégrité ne sont pas à jour**. Cela se produit lorsque le service n'a pas reçu de données au cours des deux dernières heures.  

* L’alerte d’état **Avertissement** se déclenche si Connect Health ne reçoit pas d’éléments de données partiels de la part du serveur pendant deux heures. L’alerte d’état Avertissement ne déclenche pas de notifications par e-mail à l’administrateur des clients.
* L’alerte d’état **Erreur** se déclenche si Connect Health ne reçoit pas du tout d’éléments de données de la part du serveur pendant deux heures. L’alerte d’état Erreur déclenche des notifications par e-mail à l’administrateur des clients.


## <a name="troubleshooting-steps"></a>Étapes de dépannage 

> [!IMPORTANT] 
> Cette alerte suit la [stratégie de conservation des données](reference-connect-health-user-privacy.md#data-retention-policy) de Connect Health.

* Assurez-vous que les services des agents Azure AD Connect Health sont en cours d’exécution sur la machine. Par exemple, Connect Health pour AD FS doit avoir trois services.  
  ![Vérifier Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* N’oubliez pas de prendre connaissance et de respecter la [section Configuration requise](how-to-connect-health-agent-install.md#requirements).
* Utilisez l’[outil de test de connectivité](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) pour découvrir les problèmes de connectivité.
* Si vous disposez d'un proxy HTTP, suivez cette [procédure de configuration](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

Le panneau de détails de l’alerte répertorie les éléments de données manquants en provenance d’un serveur. Le tableau suivant vous aidera à mieux identifier le problème. 
### <a name="connect-health-for-sync"></a>Connect Health pour la synchronisation

| Éléments de données | Étapes de dépannage |
| --- | --- | 
| PerfCounter | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L’inspection SSL pour le trafic sortant est filtrée ou désactivée](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health pour ADFS

Étapes supplémentaires pour valider pour AD FS et suivre le workflow dans l’[aide AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Éléments de données | Étapes de dépannage |
| --- | --- | 
| PerfCounter, TestResult | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [L’inspection SSL pour le trafic sortant est filtrée ou désactivée](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | Connectivité sortante basée sur des adresses IP, reportez-vous à [Plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>Connect Health pour ADDS

| Éléments de données | Étapes de dépannage |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Connectivité sortante vers les points de terminaison de service Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Ports du pare-feu sur le serveur qui exécute l’agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>Étapes suivantes
* [Forum Aux Questions (FAQ) Azure AD Connect Health](reference-connect-health-faq.md)
