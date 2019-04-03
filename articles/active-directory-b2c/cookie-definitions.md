---
title: Définitions de cookie - Azure Active Directory B2C | Microsoft Docs
description: Fournit des définitions pour les cookies utilisés dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ac422a00a919903063c96ac096882036b99a63e3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887225"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Définitions de cookies pour Azure Active Directory B2C

Le tableau suivant répertorie les cookies utilisés dans Azure Active Directory B2C.

| Nom | Domaine | Expiration | Objectif |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.Azure.com | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Contient les données d’appartenance utilisateur entre les locataires. Les locataires, un utilisateur est membre d’et de niveau d’adhésion (administrateur ou utilisateur). |
| x-ms-cpim-slice | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Utilisé pour acheminer les demandes vers l’instance de production appropriés. |
| x-ms-cpim-trans | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Utilisé pour le suivi des transactions (nombre de demandes d’authentification Azure AD B2C) et la transaction en cours. |
| x-ms-cpim-sso:{Id} | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Permet de gérer la session d’authentification unique. |
| x-ms-cpim-cache:{id}_n | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md), une authentification réussie | Permet de gérer l’état de la demande. |
| x-ms-cpim-csrf | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Jeton de Cross-Site Request Forgery utilisé pour la protection de CRSF. |
| x-ms-cpim-dc | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Utilisé pour le routage de réseau Azure AD B2C. |
| x-ms-cpim-ctx | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Context |
| x-ms-cpim-rp | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Utilisé pour stocker les données d’appartenance pour le locataire de fournisseur de ressources. |
| x-ms-cpim-rc | Login.microsoftonline.com, b2clogin.com, domaine personnalisée | Fin de [session de navigateur](active-directory-b2c-token-session-sso.md) | Utilisé pour stocker le cookie de relais. |

