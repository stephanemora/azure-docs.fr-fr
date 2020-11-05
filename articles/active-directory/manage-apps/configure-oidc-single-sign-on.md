---
title: Comprendre l’authentification unique (SSO) basée sur OIDC pour les applications dans Azure Active Directory
description: Comprendre l’authentification unique (SSO) basée sur OIDC pour les applications dans Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 5fc0156b9b832f44fd38292ef2b9a25e39401610
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348659"
---
# <a name="understand-oidc-based-single-sign-on"></a>Comprendre l’authentification unique basée sur OIDC
Avec la [série de guides de démarrage rapide](view-applications-portal.md) sur la gestion des applications, vous avez appris à utiliser Azure AD comme fournisseur d’identité (IdP) pour une application. Cet article décrit plus en détails les applications qui utilisent la norme OpenID Connect pour implémenter l’authentification unique. 

## <a name="before-you-begin"></a>Avant de commencer
Le processus d’ajout d’une application à votre locataire Azure Active Directory dépend du type d’authentification unique que l’application a implémentée. Pour en savoir plus sur les options d’authentification unique disponibles pour les applications qui peuvent utiliser Azure AD pour la gestion des identités, consultez [les options d’authentification unique](sso-options.md). Cet article traite des applications basées sur OIDC.


## <a name="basic-oidc-configuration"></a>Configuration de base OIDC
La [série de guides de démarrage rapide](add-application-portal-setup-oidc-sso.md) inclut un article sur la configuration de l’authentification unique. Dans ce guide, vous allez apprendre à ajouter une application basée sur OIDC à votre locataire Azure.

La bonne chose avec l’ajout d’une application qui utilise la norme OIDC pour l’authentification unique est que la configuration est minime. Voici une brève vidéo illustrant comment ajouter une application basée sur OIDC à votre locataire.

Ajout d’une application basée sur OIDC dans Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="next-steps"></a>Étapes suivantes

- [Série de guides de démarrage rapide sur la gestion des applications](add-application-portal-setup-oidc-sso.md)
- [Options d’authentification unique](sso-options.md)
- [Procédure : connecter un utilisateur Azure Active Directory à l’aide du modèle d’application multilocataire](../develop/howto-convert-app-to-be-multi-tenant.md)
