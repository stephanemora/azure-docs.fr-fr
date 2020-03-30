---
title: Versions de flux utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez-en plus sur les différentes versions de flux utilisateur disponibles dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185618"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versions de flux utilisateur dans Azure Active Directory B2C

Les flux utilisateur dans Azure Active Directory B2C (Azure AD B2C) vous aident à configurer des [stratégies](user-flow-overview.md) courantes qui décrivent entièrement les expériences liées à l’identité des clients. Ces expériences incluent l’inscription, la connexion, la réinitialisation du mot de passe et la modification du profil. Dans Azure AD B2C, vous pouvez sélectionner des flux utilisateur recommandés et en préversion dans une collection.

Les nouveaux flux utilisateur sont ajoutés sous forme de nouvelles versions. L’utilisation d’un flux utilisateur est recommandée quand le flux se stabilise. Un flux utilisateur **recommandé** signifie qu’il a été rigoureusement testé. Les flux utilisateur sont considérés comme étant en préversion tant qu’ils ne sont pas recommandés. Vous devez utiliser un flux utilisateur recommandé pour toute application de production, mais vous pouvez choisir d’autres versions pour tester les nouvelles fonctionnalités mises à disposition. Il est déconseillé d’utiliser les versions antérieures des flux utilisateur recommandés.

>[!IMPORTANT]
> Si un flux d’utilisateur n’est pas identifié comme **recommandé**, il est considéré comme étant en *préversion*. Veillez à n’utiliser que des flux d’utilisateur recommandés pour vos applications de production.

## <a name="v1"></a>V1

| Flux utilisateur | Recommandé | Description |
| --------- | ----------- | ----------- |
| Réinitialisation du mot de passe | Oui | Permet à un utilisateur de choisir un nouveau mot de passe après la vérification de son adresse e-mail. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>Paramètres de conformité de jeton</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul> |
| Modification de profil | Oui | Permet à un utilisateur de configurer ses attributs utilisateur. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li></ul> |
| Se connecter à l’aide de ROPC | Non | Permet à un utilisateur avec un compte local de se connecter directement à des applications natives (aucun navigateur n’est requis). À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li></ul> |
| Se connecter | Non | Permet à un utilisateur de se connecter à son compte. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li><li>Bloquer la connexion</li><li>Forcer la réinitialisation du mot de passe</li><li>Maintenir la connexion</ul><br>Vous ne pouvez pas personnaliser l’interface utilisateur avec ce flux utilisateur. |
| Inscription | Non | Permet à un utilisateur de créer un compte. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul> |
| Inscription et connexion | Oui | Permet à un utilisateur de créer un compte ou de se connecter à son compte. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Flux utilisateur | Recommandé | Description |
| --------- | ----------- | ----------- |
| Réinitialisation de mot de passe v1.1 | Non | Permet à un utilisateur de choisir un nouveau mot de passe après la vérification de son adresse e-mail (nouvelle mise en page disponible). À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>Paramètres de conformité de jeton</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Flux utilisateur | Recommandé | Description |
| --------- | ----------- | ----------- |
| Réinitialisation de mot de passe v2 | Non | Permet à un utilisateur de choisir un nouveau mot de passe après la vérification de son adresse e-mail. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>Paramètres de conformité de jeton</li><li>[Vérification de l’âge](basic-age-gating.md)</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul> |
| Modification du profil v2 | Oui | Permet à un utilisateur de configurer ses attributs utilisateur. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li></ul> |
| Connexion v2 | Non | Permet à un utilisateur de se connecter à son compte. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li><li>[Vérification de l’âge](basic-age-gating.md)</li><li>Personnalisation de la page de connexion</li></ul> |
| Inscription v2 | Non | Permet à un utilisateur de créer un compte. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>[Durée de vie du jeton](tokens-overview.md)</li><li>Paramètres de conformité de jeton</li><li>Comportement de la session</li><li>[Vérification de l’âge](basic-age-gating.md)</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul> |
| Inscription et connexion v2 | Non | Permet à un utilisateur de créer un compte ou de se connecter à son compte. À l’aide de ce flux utilisateur, vous pouvez configurer les éléments suivants : <ul><li>[Authentification multifacteur](custom-policy-multi-factor-authentication.md)</li><li>[Vérification de l’âge](basic-age-gating.md)</li><li>[Exigences en termes de « complexité » du mot de passe](user-flow-password-complexity.md)</li></ul> |
