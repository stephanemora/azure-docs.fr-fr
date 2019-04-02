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
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803289"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Les données du Service de contrôle d’intégrité ne sont pas mises à jour

## <a name="overview"></a>Présentation
Les agents des machines locales surveillées par Azure AD Connect Health transfèrent régulièrement des données vers le service Azure AD Connect Health. Si le service ne reçoit pas de données émanant d’un agent, les informations présentées dans le portail sont obsolètes. Pour mettre en évidence le problème, le service déclenche une alerte **Les données du service de contrôle d’intégrité ne sont pas à jour**. Il est généré lorsque le service n’a pas reçu les données terminées dans les deux dernières heures.  

* Le **avertissement** alerte d’état se déclenche si le Service de contrôle d’intégrité a reçu uniquement **partielle** les types de données envoyées à partir du serveur dans les deux dernières heures. Alerte d’état avertissement ne déclenche pas de notifications par courrier électronique aux destinataires configurés. 
* Le **erreur** alerte d’état se déclenche si le Service de contrôle d’intégrité n’a pas reçu des types de données à partir du serveur dans les deux dernières heures. Notifications par courrier électronique aux destinataires configurés de déclencheurs d’alerte état erreur.

Le Service Obtient les données à partir des Agents qui sont exécutent sur les ordinateurs sur site. Selon le Type de Service, le tableau suivant répertorie les agents qui s’exécutent sur l’ordinateur, ce qu’ils font, ainsi que les Types de données qui sont générés par le service. Dans certains cas, il existe plusieurs services impliqués dans le processus, par conséquent, un d’eux peut être la cause du problème. 

## <a name="understanding-the-alert"></a>Présentation de l’alerte
Le panneau de détails de l’alerte indique le moment où l’alerte est déclenchée et dernière détection. L’alerte est générée/ré-evaluated par un processus en arrière-plan qui s’exécute toutes les deux heures. Dans l’exemple ci-dessous, l’alerte initiale a été déclenché au 03/10 à 9 h 59. Il a continué d’exister même au 12/03 10 h 00 lorsque l’alerte a été évaluée à nouveau.
Le panneau détaille également le temps quand un Type particulier de données lors de sa réception par le Service de contrôle d’intégrité. 
 
 ![Détails d’alerte Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Voici le mappage de types de service et type de données requises correspondant.

| Type de service | Agent (nom du Service de Windows) | Objectif | Type de données généré  |
| --- | --- | --- | --- |  
| Azure AD Connect (synchronisation) | Azure AD Connect Health Sync Insights Service | Collecter des informations spécifiques AAD Connect (connecteurs, la synchronisation des règles, etc..) | - AadSyncService-SynchronizationRules <br />  AadSyncService-connecteurs <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | Collecter les compteurs de performances (AAD Connect spécifique), les Traces ETW, fichiers | Compteur de performances |
| AD DS | Azure AD Connect Health AD DS Insights Service | Effectuer des Tests synthétiques, collecter des informations sur la topologie, les métadonnées de réplication |  -Ajoute-TopologyInfo-Json <br /> -Common-TestData-Json (crée les résultats des tests)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Collecter des fichiers de compteurs, les Traces ETW, de performances (spécifiques à AD DS) | -Compteur de performances  <br /> -Common-TestData-Json (charge les résultats des tests)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Effectuer des tests synthétiques | TestResult (crée les résultats des tests) | 
| | Azure AD Connect Health AD FS Insights Service  | Collecter les métriques d’utilisation AD FS | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Collecter des fichiers de compteurs, les Traces ETW, de performances (spécifiques à AD FS) | TestResult (charge les résultats des tests) |

## <a name="troubleshooting-steps"></a>Étapes de dépannage 

Les étapes requises pour diagnostiquer le problème est donné ci-dessous. Le premier est un ensemble de vérifications de base qui sont communes à tous les Types de Service. Le tableau ci-dessous qui répertorie les étapes spécifiques pour chaque Type de Service et le Type de données. 

> [!IMPORTANT] 
> Cette alerte suit la [stratégie de conservation des données](reference-connect-health-user-privacy.md#data-retention-policy) de Connect Health.

* Vérifiez que les dernières versions des agents sont installées. Vue [historique des versions](reference-connect-health-version-history.md). 
* Assurez-vous que les Agents Azure AD Connect Health services sont **en cours d’exécution** sur l’ordinateur. Par exemple, Connect Health pour AD FS doit avoir trois services.
  ![Vérifier Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* N’oubliez pas de prendre connaissance et de respecter la [section Configuration requise](how-to-connect-health-agent-install.md#requirements).
* Utilisez l’[outil de test de connectivité](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) pour découvrir les problèmes de connectivité.
* Si vous disposez d'un proxy HTTP, suivez cette [procédure de configuration](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Étapes suivantes
Si une des étapes ci-dessus identifié un problème, corrigez-le et attendez que l’alerte à résoudre. Le processus en arrière-plan alerte s’exécute toutes les 2 heures, il prendra jusqu'à 2 heures pour résoudre l’alerte. 

* [Stratégie de rétention de données AD Connect Health Azure](reference-connect-health-user-privacy.md#data-retention-policy)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](reference-connect-health-faq.md)
