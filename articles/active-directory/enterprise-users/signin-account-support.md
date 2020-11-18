---
title: Ma page de connexion Azure AD accepte-t-elle les comptes Microsoft | Microsoft Docs
description: Comment les messages à l’écran reflètent la recherche de nom d’utilisateur en cours de connexion
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2a3c2257311f208ae704a54f1369e10b2ff160b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646846"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Options de connexion pour les comptes Microsoft dans Azure Active Directory

La page de connexion Microsoft 365 pour Azure Active Directory (Azure AD) prend en charge les comptes scolaires et professionnels, ainsi que les comptes Microsoft, mais, selon la situation de l’utilisateur, il peut s’agit d’un type de copte, de l’autre ou des deux. Par exemple, la page de connexion Azure AD prend en charge :

* les applications qui acceptent les connexions à partir des deux types de comptes ;
* les organisations qui acceptent des invités.

## <a name="identification"></a>Identification
Vous pouvez déterminer si la page de connexion que votre organisation utilise prend en charge les comptes Microsoft en examinant le texte d’information dans le champ du nom d’utilisateur. Si le texte d’information est « Courrier, téléphone ou Skype », la page de connexion prend en charge les comptes Microsoft.

![Différence entre pages de connexion de compte](./media/signin-account-support/ui-prompt.png)

[Les options de connexion supplémentaires fonctionnent uniquement pour les comptes Microsoft personnels.](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) Elles ne peuvent pas être utilisées pour accéder à des ressources de compte professionnel ou scolaire.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter votre nom de domaine personnalisé](../fundamentals/add-custom-domain.md)