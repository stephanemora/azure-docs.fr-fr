---
title: Résoudre les problèmes de données manquantes dans les journaux d’activité Azure Active Directory | Microsoft Docs
description: Fournit une résolution au problème de données manquantes dans les journaux d’activité Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1ceb903d5586dbcc824e3ad9de56e609f162e888
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174480"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Résolution de problèmes : données manquantes dans les journaux d’activité Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Je ne trouve pas les journaux d’audit des actions récentes dans le portail Azure

### <a name="symptoms"></a>Symptômes

J’ai réalisé certaines actions dans le portail Azure et je pensais pouvoir consulter les journaux d’audit associés dans le panneau `Activity logs > Audit Logs`, mais je ne les trouve pas.

 ![Reporting](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Cause :

Les actions n’apparaissent pas immédiatement dans les journaux d’activité. Le tableau ci-dessous énumère nos valeurs de latence pour les journaux d’activité. 

| Rapport | &nbsp; | Latence (P95) | Latence (P99) |
|--------|--------|---------------|---------------|
| Audit de répertoire | &nbsp; | 2 minutes | 5 minutes |
| Activité de connexion | &nbsp; | 2 minutes | 5 minutes | 

### <a name="resolution"></a>Résolution :

Attendez entre 15 minutes et deux heures pour voir si les actions apparaissent dans le journal. Si vous ne voyez toujours pas les journaux après deux heures, [créez un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) et nous l’étudierons.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Je ne trouve pas les connexions utilisateur récentes dans le journal d’activité des connexions à Azure Active Directory

### <a name="symptoms"></a>Symptômes

Je me suis connecté récemment au portail Azure et je pensais pouvoir consulter les journaux de connexion associés dans le panneau `Activity logs > Sign-ins`, mais je ne les trouve pas.

 ![Reporting](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Cause :

Les actions n’apparaissent pas immédiatement dans les journaux d’activité. Le tableau ci-dessous énumère nos valeurs de latence pour les journaux d’activité. 

| Rapport | &nbsp; | Latence (P95) | Latence (P99) |
|--------|--------|---------------|---------------|
| Audit de répertoire | &nbsp; | 2 minutes | 5 minutes |
| Activité de connexion | &nbsp; | 2 minutes | 5 minutes | 

### <a name="resolution"></a>Résolution :

Attendez entre 15 minutes et deux heures pour voir si les actions apparaissent dans le journal. Si vous ne voyez toujours pas les journaux après deux heures, [créez un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) et nous l’étudierons.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Je n’arrive pas à afficher plus de 30 jours de données de rapport dans le portail Azure

### <a name="symptoms"></a>Symptômes

Je n’arrive pas à afficher plus de 30 jours de données de connexion et d’audit dans le portail Azure. Pourquoi ? 

 ![Reporting](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Cause :

Selon votre licence, les actions Azure Active Directory stockent les rapports d’activité pour les durées suivantes :

| Rapport           | &nbsp; |  Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Audit de répertoire  | &nbsp; |   7 jours     | 30 jours             | 30 jours             |
| Activité de connexion | &nbsp; | Non disponible Vous ne pouvez pas accéder à vos propres connexions pendant 7 jours depuis le panneau de profil utilisateur individuel | 30 jours | 30 jours             |

Pour plus d’informations, consultez [Stratégies de rétention des rapports Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Résolution :

Vous avez deux options pour conserver les données pendant plus de 30 jours. Vous pouvez utiliser les [API de création de rapports Azure AD](concept-reporting-api.md) pour récupérer les données par programmation et les stocker dans une base de données. Sinon, vous pouvez intégrer des journaux d’audit dans un système SIEM tiers tel que Splunk ou SumoLogic.

## <a name="next-steps"></a>Étapes suivantes

* [Rétention des rapports Azure AD](reference-reports-data-retention.md).
* [Latences de la création de rapports Azure Active Directory](reference-reports-latencies.md).
* [FAQ sur les rapports Azure Active Directory](reports-faq.md).

