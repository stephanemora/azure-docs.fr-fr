---
title: Stratégies de rétention des rapports Azure Active Directory | Microsoft Docs
description: Stratégies de rétention des données de rapport dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 601169cc62a99438f661adc06ab166b545606edb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624588"
---
# <a name="azure-active-directory-report-retention-policies"></a>Stratégies de rétention des rapports Azure Active Directory

Dans cet article, vous allez en savoir plus sur les stratégies de rétention des données pour les différents rapports d’activité dans Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quand Azure AD commence-t-il à collecter des données ?

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Lorsque vous souscrivez un abonnement |
| Azure AD Gratuit | La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quand les données d’activité sont-elles disponibles dans le portail Azure ?

- **Immédiatement** si vous avez déjà travaillé avec des rapports dans le portail Azure.
- **Dans les 2 heures** si vous n’avez pas encore activé la création de rapports dans le portail Azure.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quand Azure AD commence-t-il à collecter des données de signaux de sécurité ?  

Pour les signaux de sécurité, le processus de collection démarre lorsque vous choisissez d’utiliser **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Pendant combien de temps les données sont-elles conservées par Azure AD ?

**Rapports d’activité**    

| Rapport                 | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Audit de répertoire        | 7 jours        | 30 jours             | 30 jours             |
| Activité de connexion       | N/A           | 30 jours             | 30 jours             |
| Utilisation d’Azure MFA        | 30 jours       | 30 jours             | 30 jours             |

**Signaux de sécurité**

| Rapport         | Azure AD Gratuit | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Les utilisateurs à risque  | 7 jours        | 30 jours             | 90 jours             |
| Connexions risquées | 7 jours        | 30 jours             | 90 jours             |

---
