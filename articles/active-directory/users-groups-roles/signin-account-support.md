---
title: Comment savoir si une page de connexion Azure AD accepte les comptes Microsoft | Microsoft Docs
description: Comment l’écran messagerie reflète le nom d’utilisateur recherche pendant la connexion
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62c4d56fbfca34ff6291863149b078f7ddc6680
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288590"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Options de connexion pour les comptes Microsoft dans Azure Active Directory

La page de connexion Microsoft 365 pour Azure Active Directory (Azure AD) prend en charge une ou les deux comptes scolaires et professionnels ou comptes Microsoft, selon la situation, pour prendre en charge :

* Applications qui acceptent les connexions depuis les deux types de compte
* Organisations qui acceptent des invités

## <a name="identification"></a>Identification
Vous pouvez indiquer si la page de connexion que votre organisation utilise prend en charge les comptes Microsoft en examinant le texte d’information dans le champ nom d’utilisateur. Si le texte d’indication est « Courrier électronique, téléphone ou Skype », la page de connexion prend en charge les comptes Microsoft.

![Différence entre les pages de connexion compte](./media/signin-account-support/ui-prompt.png)

[Options de connexion supplémentaires fonctionnent uniquement pour les comptes Microsoft personnels](index.yml) mais ne peut pas être utilisée pour la connexion à des professionnels ou scolaires de ressources de compte.

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser votre connexion de personnalisation](../fundamentals/add-custom-domain.md)