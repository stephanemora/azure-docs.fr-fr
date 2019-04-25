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
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468202"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Options de connexion pour les comptes Microsoft dans Azure Active Directory

La page de connexion Microsoft 365 pour Azure Active Directory (Azure AD) prend en charge les comptes scolaires et professionnels ou comptes Microsoft, mais en fonction de la situation de l’utilisateur, il peut être une ou l’autre ou les deux. Par exemple, la page de connexion Azure AD prend en charge :

* Applications qui acceptent les connexions depuis les deux types de compte
* Organisations qui acceptent des invités

## <a name="identification"></a>Identification
Vous pouvez indiquer si la page de connexion que votre organisation utilise prend en charge les comptes Microsoft en examinant le texte d’information dans le champ nom d’utilisateur. Si le texte d’indication est « Courrier électronique, téléphone ou Skype », la page de connexion prend en charge les comptes Microsoft.

![Différence entre les pages de connexion compte](./media/signin-account-support/ui-prompt.png)

[Options de connexion supplémentaires fonctionnent uniquement pour les comptes Microsoft personnels](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) mais ne peut pas être utilisée pour la connexion à des professionnels ou scolaires de ressources de compte.

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser votre connexion de personnalisation](../fundamentals/add-custom-domain.md)