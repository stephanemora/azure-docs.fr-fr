---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Forum Aux Questions relatif à Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e30f8264870272fc12dcb7e15b37e5fd30688bcb
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126521"
---
# <a name="azure-active-directory-identity-protection-faq"></a>FAQ Azure Active Directory Identity Protection

Cet article répond aux questions fréquemment posées sur Azure Active Directory (Azure AD) Identity Protection. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](../active-directory-identityprotection.md). 

## <a name="why-do-some-risk-detections-have-closed-system-status"></a>Pourquoi certaines détections de risque ont-elles le statut « Clôturé (système) » ?

**R :** Ces détections de risques sont détectées par Identity Protection et clôturées ultérieurement, car les événements n’ont plus été considérés comme dangereux. Ces événements ne sont pas comptabilisés dans le niveau de risque de l’utilisateur. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Dois-je être administrateur général pour utiliser la protection d’identité dans le portail Azure ?
**R :**  Non. Vous pouvez être un lecteur de sécurité, un administrateur de sécurité ou un administrateur général pour utiliser la protection d’identité.

---

## <a name="how-do-i-get-identity-protection"></a>Comment obtenir Identity Protection ?

**R :** Consultez [Prise en main d’Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour connaître la réponse à cette question.

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>Comment faire pour trier les utilisateurs répertoriés dans « Utilisateurs avec indicateur de risque » ?

**R :** Téléchargez le rapport des utilisateurs avec indicateur de risque en cliquant sur **Télécharger** en haut de la page **Utilisateurs avec indicateur de risque**. Vous pouvez ensuite trier les données téléchargées en fonction des champs disponibles, notamment Dernière mise à jour (UTC).

---
