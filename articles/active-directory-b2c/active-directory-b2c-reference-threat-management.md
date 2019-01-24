---
title: Gérer les menaces aux ressources et aux données dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez les techniques de détection et de prévention d’attaques par déni de service et d’attaques de mot de passe dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 66932aa4ea070c5f8ca83524a424e3b73b724c73
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845720"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gérer les menaces aux ressources et aux données dans Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C dispose de fonctionnalités intégrées qui peuvent vous aider à protéger vos ressources et vos données contre les menaces. Ces menaces incluent des attaques par déni de service et des attaques de mot de passe. Les attaques par déni de service peuvent rendre les ressources inaccessibles aux utilisateurs prévus. Une attaque de mot de passe peut mener à un accès non autorisé aux ressources. 

## <a name="denial-of-service-attacks"></a>Attaques par déni de service

Azure AD B2C protège contre les attaques de flux SYN à l’aide d’un cookie SYN. Azure AD B2C protège également contre les attaques par déni de service en limitant les tarifs et les connexions.

## <a name="password-attacks"></a>Attaques de mot de passe

Les mots de passe définis par les utilisateurs doivent être d’une complexité raisonnable. Azure AD B2C dispose de techniques d’atténuation pour contrer les attaques de mot de passe. L’atténuation couvre à la fois les attaques de mot de passe par force brute et celles basées sur un dictionnaire. À l’aide de différents signaux, Azure AD B2C analyse l’intégrité des demandes. Azure AD B2C est conçu pour différencier intelligemment les utilisateurs prévus des pirates et botnets. 

Azure AD B2C applique une stratégie sophistiquée pour verrouiller les comptes. Le verrouillage des comptes est basé sur l’adresse IP de la requête et sur les mots de passe entrés. La durée du verrouillage augmente également en fonction de la probabilité de l’existence d’une attaque. Après 10 tentatives d’entrée d’un mot de passe sans succès, le compte est verrouillé pendant une minute. La prochaine fois qu'une connexion échoue après le déverrouillage du compte, un autre verrouillage d'une minute se produit et se poursuit pour chaque connexion non réussie. L’entrée du même mot de passe à plusieurs reprises n’est pas considérée comme plusieurs connexions ayant échoué. 

Les 10 premiers verrouillages durent une minute. Les 10 verrouillages suivants durent un peu plus longtemps, et leur durée augmente tous les 10 verrouillages. Le compteur de verrouillage se remet à zéro après une connexion réussie lorsque le compte n’est pas verrouillé. Les périodes de verrouillage peuvent durer jusqu'à cinq heures. 

Actuellement, vous ne pouvez pas :

- déclencher un verrouillage avec moins de 10 échecs de connexion ;
- récupérer une liste des comptes verrouillés ;
- configurer la stratégie de verrouillage.

Pour plus d’informations, consultez le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/trustcenter/default.aspx).
