---
title: Choisir entre un cloud ou un serveur Azure MFA | Microsoft Docs
description: Choisissez la solution de sécurité d’authentification multifacteur la plus appropriée pour vous en définissant les éléments à protéger et l’emplacement des utilisateurs.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 8314d72aa2cc6787d3f65dd48cd693a0ac332c0a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866350"
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Choisissez la solution Azure Multi-Factor Authentication qui vous convient
Étant donné qu’il existe plusieurs types d’Azure Multi-Factor Authentication (MFA), nous devons répondre à quelques questions pour connaître la version utiliser.  Ces questions sont les suivantes :

* [les éléments à protéger](#what-am-i-trying-to-secure)
* [l’emplacement des utilisateurs](#where-are-the-users-located)
* [quelles sont les fonctionnalités nécessaires ?](#what-features-do-i-need)

Les sections suivantes fournissent des conseils sur la détermination de ces réponses.

## <a name="what-am-i-trying-to-secure"></a>Les éléments à protéger.
Afin de déterminer la solution de vérification en deux étapes appropriée, nous devons tout d’abord connaître les éléments à protéger avec une seconde méthode d’authentification.  S’agit-il d’une application dans Azure ?  Ou d’un système d’accès à distance ?  En déterminant les éléments à protéger, nous pouvons répondre à la question de savoir où l’authentification multifacteur doit être activée.  

| Les éléments que vous souhaitez protéger | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Applications Microsoft internes |● |● |
| Applications SaaS dans la galerie d’applications |● |  |
| Applications Web publiées via le proxy d’application Azure AD |● |  |
| Applications IIS non publiées via le proxy d'application Azure AD | |● |
| Accès à distance comme VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>l’emplacement des utilisateurs
Ensuite, selon l’emplacement de nos utilisateurs, nous pouvons déterminer la solution appropriée à utiliser, que ce soit dans le cloud ou en local avec le serveur MFA.

| Emplacement de l'utilisateur | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD et AD local à l'aide de la fédération avec AD FS |● |● |
| Azure AD et AD local avec DirSync, Azure AD Sync, Azure AD Connect - aucune synchronisation de hachage de mot de passe ni authentification directe |● |● |
| Azure AD et AD local avec DirSync, Azure AD Sync, Azure AD Connect - avec synchronisation de hachage de mot de passe et authentification directe |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>Quelles sont les fonctionnalités nécessaires ?
Le tableau suivant compare les fonctionnalités disponibles de Multi-Factor Authentication dans le cloud et du serveur Multi-Factor Authentication.

| Fonctionnalité | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Notification d'application mobile comme second facteur | ● | ● |
| Code de vérification d'application mobile comme second facteur | ● | ● |
| Appel téléphonique comme second facteur | ● | ● |
| SMS unidirectionnel comme second facteur | ● | ● |
| SMS bidirectionnel comme second facteur | | ● (Déprécié)| 
| Jetons matériels comme second facteur | | ● |
| Mots de passe d’application pour les clients Office 365 qui ne prennent pas en charge MFA | ● | |
| Contrôle d'administration sur les méthodes d'authentification | ● | ● |
| Mode du code PIN | | ● |
| Alerte de fraude |● | ● |
| Rapports MFA |● | ● |
| Contournement à usage unique | | ● |
| Messages de bienvenue personnalisés pour les appels téléphoniques | ● | ● |
| ID d'appelant personnalisable pour les appels téléphoniques | ● | ● |
| Adresses IP approuvées | ● | ● |
| Mémoriser MFA pour les appareils fiables | ● | |
| Accès conditionnel | ● | ● |
| Cache |  | ● |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez la différence entre Azure Multi-Factor Authentication dans le cloud et le serveur local Multi-Factor Authentication, nous allons configurer et utiliser Azure Multi-Factor Authentication. **Sélectionnez l’icône qui représente votre scénario**

<center>

[![MFA dans le cloud](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![Serveur MFA](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
