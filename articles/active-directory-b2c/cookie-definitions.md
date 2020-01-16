---
title: Définitions de cookies - Azure Active Directory B2C | Microsoft Docs
description: Cet article fournit les définitions de certains cookies utilisés dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930891"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Définitions de cookies pour Azure Active Directory B2C

Le tableau suivant répertorie les cookies utilisés dans Azure Active Directory B2C.

| Name | Domain | Expiration | Objectif |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Fin de la [session de navigateur](session-behavior.md) | Contient les données d’appartenance de l’utilisateur sur tous les locataires. Les locataires dont un utilisateur est membre et son niveau d’appartenance (Admin ou Utilisateur). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Utilisé pour acheminer les requêtes vers l’instance de production appropriée. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Utilisé pour le suivi des transactions (nombre de requêtes d’authentification Azure AD B2C) et la transaction en cours. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Permet de gérer la session d’authentification unique. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md), authentification réussie | Permet de gérer l’état de la requête. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Jeton de falsification de requête intersites utilisé pour la protection CRSF. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Utilisé pour le routage du réseau Azure AD B2C. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Context |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Utilisé pour le stockage des données d’appartenance pour le locataire du fournisseur de ressources. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, domaine de marque | Fin de la [session de navigateur](session-behavior.md) | Utilisé pour le stockage du cookie de relais. |
