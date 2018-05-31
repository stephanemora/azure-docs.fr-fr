---
title: Stratégies de rétention des rapports Azure Active Directory | Microsoft Docs
description: Stratégies de rétention des données de rapport dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9101b3877f8a011878baeed0d5c23d29fddaeaad
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055169"
---
# <a name="azure-active-directory-report-retention-policies"></a>Stratégies de rétention des rapports Azure Active Directory


Cette rubrique fournit des réponses aux questions plus courantes en rapport avec la conservation des données pour les différents rapports d’activité dans Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Q : Comment obtenir la collecte des données des activités démarrée ?

**R :**

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Lorsque vous vous inscrivez pour un abonnement |
| Azure AD Gratuit | La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Q: Quand vos données d’activité sont-elles disponibles dans le portail Azure ?

**R :**

- **Immédiatement** : si vous avez déjà travaillé avec des rapports dans le portail Azure
- **Dans les 2 heures** : si vous n’avez pas encore activé la création de rapports dans le portail Azure

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Q : Comment obtenir la collecte des signaux de sécurité démarrée ?  

**R :** Pour les signaux de sécurité, le processus de collecte démarre quand vous choisissez d’utiliser Identity Protection Center. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Q : Pendant combien de temps les données collectées sont-elles stockées ?

**R :**

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
