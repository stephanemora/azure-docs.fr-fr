---
title: MFA ou 2FA et Privileged Identity Management - Azure AD | Microsoft Docs
description: Découvrez comment Azure AD Privileged Identity Management (PIM) valide l’authentification multifacteur (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e263de51c234c0572584911b3bd8f7d4eb7c487c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667209"
---
# <a name="multifactor-authentication-and-privileged-identity-management"></a>Authentification multifacteur et Privileged Identity Management

Nous vous recommandons d’exiger l’authentification multifacteur (MFA ou 2FA) pour tous vos administrateurs. L’authentification multifacteur réduit le risque d’attaque avec un mot de passe compromis.

Vous pouvez exiger que les utilisateurs répondent à une demande d’authentification multifacteur quand ils se connectent. Vous pouvez également exiger que les utilisateurs répondent à une demande d’authentification multifacteur quand ils activent un rôle dans Azure Active Directory (Azure AD) Privileged Identity Management (PIM). De cette façon, même si l’utilisateur n’a pas effectué l’authentification multifacteur quand il s’est connecté, il est invité à le faire par Privileged Identity Management.

> [!IMPORTANT]
> Azure AD Multi-Factor Authentication ne fonctionne actuellement qu’avec des comptes professionnels ou scolaires, pas avec des comptes Microsoft (généralement un compte personnel utilisé pour se connecter à des services Microsoft tels que Skype, Xbox ou Outlook.com). C’est pourquoi une personne avec un compte personnel ne peut pas être administrateur, car elle ne peut pas utiliser l’authentification multifacteur pour activer ses rôles. Si ces utilisateurs doivent continuer à gérer les charges de travail à l’aide d’un compte Microsoft, convertissez-les en administrateurs permanents pour l’instant.

## <a name="how-pim-validates-mfa"></a>Comment PIM valide MFA

Il existe deux options pour valider l’authentification multifacteur quand un utilisateur active un rôle.

Le plus simple consiste à s’appuyer sur Azure AD Multi-Factor Authentication pour les utilisateurs qui activent un rôle privilégié. Pour ce faire, vérifiez tout d’abord que ces utilisateurs bénéficient d’une licence si nécessaire, et qu’ils se sont inscrits à Azure AD Multi-Factor Authentication. Pour plus d’informations sur la façon de déployer Azure AD Multi-Factor Authentication, consultez [Déployer Azure AD Multi-Factor Authentication basé sur le cloud](../authentication/howto-mfa-getstarted.md). Nous vous recommandons, mais ce n’est pas obligatoire, de configurer Azure AD afin d’appliquer l’authentification multifacteur pour ces utilisateurs quand ils se connectent. C’est parce que les vérifications de l’authentification multifacteur sont effectuées par Privileged Identity Management lui-même.

Si les utilisateurs s’authentifient localement, vous pouvez également définir votre fournisseur d’identité comme responsable de l’authentification multifacteur. Par exemple, si vous avez configuré des services ADFS pour exiger l’authentification par carte à puce avant d’accéder à Azure AD, la section [Sécurisation des ressources de cloud avec Azure AD Multi-Factor Authentication et ADFS](../authentication/howto-mfa-adfs.md) inclut des instructions pour configurer ADFS afin d’envoyer les revendications à Azure AD. Quand un utilisateur tente d’activer un rôle, Privileged Identity Management reconnaît que l’authentification multifacteur a déjà été validée pour l’utilisateur quand il a reçu les revendications appropriées.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles Azure AD dans Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Configurer les paramètres des rôles de ressources Azure dans Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
