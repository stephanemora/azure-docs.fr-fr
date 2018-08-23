---
title: Gestion des menaces dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez les techniques de détection et de prévention d’attaques par déni de service et d’attaques de mot de passe dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1801fe9695aa15850d600300b957df2c7d7cd9ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145959"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C : Gestion des menaces

La gestion des menaces inclut la planification de la protection contre les attaques des systèmes et des réseaux. Les attaques par déni de service peuvent rendre les ressources inaccessibles aux utilisateurs prévus. Une attaque de mot de passe peut mener à un accès non autorisé aux ressources. Azure Active Directory B2C (Azure AD B2C) dispose de fonctionnalités intégrées qui peuvent vous aider à protéger vos données contre ces menaces de plusieurs façons.

## <a name="denial-of-service-attacks"></a>Attaques par déni de service

Azure AD B2C utilise des techniques de détection et d’atténuation telles que les cookies SYN ainsi que les limites de débit et de connexion pour protéger les ressources sous-jacentes contre les attaques par déni de service.

## <a name="password-attacks"></a>Attaques de mot de passe

Azure AD B2C dispose également de techniques d’atténuation pour contrer les attaques de mot de passe. L’atténuation couvre à la fois les attaques de mot de passe par force brute et celles basées sur un dictionnaire. Les mots de passe définis par les utilisateurs doivent être d’une complexité raisonnable. À l’aide de différents signaux, Azure AD B2C analyse l’intégrité des demandes. Azure AD B2C est conçu pour différencier intelligemment les utilisateurs prévus des pirates et botnets. Azure AD B2C fournit une stratégie sophistiquée pour verrouiller les comptes en fonction des mots de passe entrés, dans l’éventualité d’une attaque.

Pour plus d’informations, consultez le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/trustcenter/default.aspx).
