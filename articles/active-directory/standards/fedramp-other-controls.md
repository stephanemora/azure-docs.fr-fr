---
title: Configurer des contrôles supplémentaires pour atteindre un niveau d’impact élevé FedRAMP
description: Instructions détaillées pour la configuration des contrôles supplémentaires afin d’atteindre un niveau d’impact élevé FedRAMP.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: celested
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e6f0930ebfaac554ddc15b0b038b74944ba663d
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517961"
---
# <a name="configure-additional-controls-to-achieve-fedramp-high-impact-level"></a>Configurer des contrôles supplémentaires pour atteindre un niveau d’impact élevé FedRAMP

La liste suivante de contrôles (et d’améliorations de contrôle) des familles ci-dessous peut nécessiter une configuration dans votre locataire Azure AD.

Chaque ligne des tableaux ci-dessous fournit des instructions normatives pour vous aider à développer la réponse de votre organisation pour toute responsabilité partagée du contrôle et/ou de l’amélioration du contrôle.

## <a name="audit--accountability"></a>Audit et responsabilité

* AU-02 Événements d’audit

* AU-03 Contenu d’audit
* AU-06 Révision, analyse et rapports d’audit


| ID de contrôle et sous-partie| Responsabilités du client et instructions |
| - | - |
| AU-02 <br>AU-03 <br>AU-03(1)<br>AU-03(2)| **S’assurer que le système est capable d’auditer les événements définis dans AU-02 Partie a, et de se coordonner avec d’autres entités au sein du sous-ensemble d’événements pouvant être audités de l’organisation pour prendre en charge les investigations ultérieures. Implémenter la gestion centralisée des enregistrements d’audit**.<p>Toutes les opérations de cycle de vie des comptes (opérations de création, de modification, d’activation, de désactivation et de suppression des comptes) sont auditées dans les journaux d’audit Azure AD. Tous les événements d’authentification et d’autorisation sont audités dans les journaux de connexion Azure AD, et tous les risques détectés sont audités dans les journaux de protection des identités. Ces différents journaux peuvent être transmis directement dans une solution SIEM (Security Information and Event Management) comme Azure Sentinel. Vous pouvez également utiliser Azure Event Hub pour intégrer les journaux à des solutions SIEM tierces.<p>Événements d’audit<li> [Rapports d’activité d’audit dans le portail Azure Active Directory](../reports-monitoring/concept-audit-logs.md)<li> [Rapports d’activité de connexion dans le portail Azure Active Directory](../reports-monitoring/concept-sign-ins.md)<li>[Guide pratique pour examiner les risques](../identity-protection/howto-identity-protection-investigate-risk.md)<p>Intégrations SIEM<li> [Azure Sentinel : connecter des données à partir d’Azure Active Directory (Azure AD)](../../sentinel/connect-azure-active-directory.md)<li>[Transmettre vers Azure Event Hub et d’autres SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| AU-06<br>AU-06(1)<br>AU-06(3)<br>AU-06(4)<br>AU-06(5)<br>AU-06(6)<br>AU-06(7)<br>AU-06(10)<br>| **Examiner et analyser au moins une fois par semaine les enregistrements d’audit pour identifier les activités inappropriées ou inhabituelles et signaler ce qui en découle au personnel compétent**. <p>Les instructions fournies ci-dessus pour AU-02 et AU-03 permettent d’examiner chaque semaine les enregistrements d’audit et d’en référer au personnel compétent. Vous ne pouvez pas répondre à ces exigences en utilisant uniquement Azure AD. Vous devez également utiliser une solution SIEM comme Azure Sentinel.<p>[Qu’est-ce qu’Azure Sentinel ?](../../sentinel/overview.md) |

## <a name="incident-response"></a>Réponse aux incidents

* IR-04 Gestion des incidents

* IR-05 Surveillance des incidents

| ID de contrôle et sous-partie| Responsabilités du client et instructions |
| - | - |
| IR-04<br>IR-04(1)<br>IR-04(2)<br>IR-04(3)<br>IR-04(4)<br>IR-04(6)<br>IR-04(8)<br>IR-05<br>IR-05(1)| **Implémenter des fonctionnalités de gestion et de surveillance des incidents, notamment : gestion des incidents automatisés, reconfiguration dynamique, continuité des opérations, corrélation des informations, menaces internes, corrélation avec les organisations externes, surveillance des incidents et suivi automatique**. <p>Toutes les modifications de configuration sont consignées dans les journaux d’audit. Les événements d’authentification et d’autorisation sont audités dans les journaux de connexion, et tous les risques détectés sont audités dans les journaux de protection des identités. Ces différents journaux peuvent être transmis directement dans une solution SIEM (Security Information and Event Management) comme Azure Sentinel. Vous pouvez également utiliser Azure Event Hub pour intégrer les journaux à des solutions SIEM tierces. Automatiser la reconfiguration dynamique basée sur des événements au sein de la solution SIEM à l’aide de MSGraph et/ou d’Azure AD PowerShell.<p>Événements d’audit<br><li>[Rapports d’activité d’audit dans le portail Azure Active Directory](../reports-monitoring/concept-audit-logs.md)<li>[Rapports d’activité de connexion dans le portail Azure Active Directory](../reports-monitoring/concept-sign-ins.md)<li>[Guide pratique pour examiner les risques](../identity-protection/howto-identity-protection-investigate-risk.md)<p>Intégrations SIEM<li>[Azure Sentinel : connecter des données à partir d’Azure Active Directory (Azure AD)](../../sentinel/connect-azure-active-directory.md)<li>[Transmettre vers Azure Event Hub et d’autres SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)<p>Reconfiguration dynamique<li>[Module AzureAD](/powershell/module/azuread/)<li>[Overview of Microsoft Graph (Vue d’ensemble de Microsoft Graph)](/graph/overview?view=graph-rest-1.0&preserve-view=true) |

## <a name="personnel-security"></a>Sécurité du personnel

* PS-04 Départ du personnel

| ID de contrôle et sous-partie| Responsabilités du client et instructions |
| - | - |
| PS-04<br>PS-04(2)| **Notifier automatiquement le personnel responsable de la désactivation de l’accès au système.** <p>Désactiver les comptes et révoquer tous les authentificateurs et informations d’identification connexes dans les 8 heures. <p>Configurer l’approvisionnement (y compris la désactivation au moment du départ) des comptes dans Azure AD à partir de systèmes RH externes, de l’instance Active Directory locale ou directement dans le cloud. Mettre fin à tous les accès système en révoquant les sessions existantes. <p>Approvisionnement des comptes<li> Consultez les instructions détaillées dans AC-02. <p>Révoquer tous les authentificateurs connexes. <li> [Révoquer les accès utilisateur lors d’une urgence dans Azure Active Directory](../enterprise-users/users-revoke-access.md) |


## <a name="system--information-integrity"></a>Intégrité du système et des informations

* SI-04 Supervision du système d’information

 ID de contrôle et sous-partie| Responsabilités du client et instructions |
| - | - |
| SI-04<br>SI-04(1)| **Implémenter la surveillance du système d’information et du système de détection des intrusions à l’échelle du réseau**<p>Inclurr tous les journaux Azure AD (audit, connexion, protection des identités) au sein de la solution de surveillance du système d’information. <p>Transmettre les journaux Azure AD vers une solution SIEM (voir IA-04). |

## <a name="next-steps"></a>Étapes suivantes

[Configurer des contrôles d’accès](fedramp-access-controls.md)

[Configurer les contrôles d’identification et d’authentification](fedramp-identification-and-authentication-controls.md)

[Configurer d’autres contrôles](fedramp-other-controls.md)
 