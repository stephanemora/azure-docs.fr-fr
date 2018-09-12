---
title: Authentification multifacteur (MFA) et PIM - Azure | Microsoft Docs
description: Découvrez comment Azure AD Privileged Identity Management (PIM) valide l’authentification multifacteur (MFA).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: cfa092e8aebe92192ecca8aec2721282e603cc5b
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669272"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Authentification multifacteur (MFA) et PIM

Nous vous recommandons d’exiger une authentification multifacteur (MFA) pour tous vos administrateurs. Cela réduit le risque d'attaque en raison d'un mot de passe compromis.

Vous pouvez exiger que les utilisateurs se soumettent à une authentification MFA lors de leur connexion. Vous pouvez également exiger que les utilisateurs répondent à une demande d’authentification multifacteur (MFA) quand ils activent un rôle dans Azure AD Privileged Identity Management (PIM). De cette façon, si l'utilisateur n'a pas terminé une demande d'authentification MFA lors de sa connexion, il sera invité à le faire par PIM.

> [!IMPORTANT]
> L’authentification multifacteur Azure ne fonctionne actuellement qu’avec des comptes professionnels ou scolaires et pas avec des comptes Microsoft (généralement un compte personnel utilisé pour se connecter à des services Microsoft tels que Skype, Xbox, Outlook.com, etc.). C’est pourquoi une personne disposant d’un compte Microsoft ne peut pas être un administrateur éligible car elle ne peut pas utiliser l’authentification multifacteur pour activer ses rôles. Si ces utilisateurs doivent continuer à gérer les charges de travail à l’aide d’un compte Microsoft, convertissez-les en administrateurs permanents pour l’instant.

## <a name="how-pim-validates-mfa"></a>Comment PIM valide MFA

Il existe deux options pour valider l'authentification multifacteur lorsqu’un utilisateur active un rôle.

Le plus simple consiste à s’appuyer sur Azure MFA pour les utilisateurs qui activent un rôle privilégié. Pour ce faire, vérifiez tout d’abord que ces utilisateurs bénéficient d’une licence si nécessaire, et qu’il se sont enregistrés pour l’authentification Azure MFA. Pour plus d’informations sur la façon de déployer Azure MFA, voir [Déployer une authentification multifacteur Azure basée sur le cloud](../authentication/howto-mfa-getstarted.md). Il est recommandé, mais pas obligatoire, de configurer Azure AD pour appliquer l’authentification MFA à ces utilisateurs lorsqu’ils se connectent. En effet, les vérifications de l’authentification multifacteur sont effectuées par PIM.

Si les utilisateurs s’authentifient en local, vous pouvez également faire en sorte que votre fournisseur d’identité soit responsable de l’authentification MFA. Par exemple, si vous avez configuré des services ADFS pour exiger l’authentification par carte à puce avant d’accéder à Azure AD, la section [Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et ADFS](../authentication/howto-mfa-adfs.md) inclut des instructions pour configurer ADFS afin d’envoyer les revendications à Azure AD. Quand un utilisateur tente d’activer un rôle, PIM accepte l’authentification multifacteur déjà validée pour l’utilisateur dès réception des revendications appropriées.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’annuaire Azure AD dans PIM](pim-how-to-change-default-settings.md)
- [Configurer les paramètres des rôles de ressources Azure dans PIM](pim-resource-roles-configure-role-settings.md)
