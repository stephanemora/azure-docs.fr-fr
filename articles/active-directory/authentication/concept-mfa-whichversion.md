---
title: Serveur ou service Azure MFA, localement ou dans le cloud ?
description: En tant qu’administrateur Azure AD, ma question est de savoir quelle version de MFA je dois déployer.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 696c331111fef9d6fc238b733e15230ab3ee1e56
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378014"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Quelle version Azure MFA est adaptée à mon organisation ?

Avant de déterminer où et comment déployer l’authentification multifacteur (MFA), il est essentiel pour vous de déterminer quels sont :

* [les éléments à protéger](#what-am-i-trying-to-secure)
* [l’emplacement des utilisateurs](#where-are-the-users-located)
* [quelles sont les fonctionnalités nécessaires ?](#what-features-do-i-need)

Chacune des sections suivantes fournit des informations détaillées pour vous aider à répondre à ces questions.

## <a name="what-am-i-trying-to-secure"></a>Les éléments à protéger.

Afin de déterminer la solution de vérification en deux étapes appropriée, vous devez tout d’abord connaître les éléments à protéger avec un facteur supplémentaire d’authentification. S’agit-il d’une application dans Azure ? Ou d’un système d’accès à distance ? En déterminant les éléments à protéger, vous pouvez répondre à la question de savoir où l’authentification multifacteur doit être activée.

| Les éléments que vous souhaitez protéger | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Applications Microsoft internes |● |● |
| Applications SaaS dans la galerie d’applications |● |  |
| Applications Web publiées via le proxy d’application Azure AD |● |  |
| Applications IIS non publiées via le proxy d'application Azure AD | |● |
| Accès à distance comme VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>l’emplacement des utilisateurs

Ensuite, selon où se trouvent les utilisateurs de votre organisation, vous pouvez déterminer la solution adaptée à utiliser, que ce soit dans le cloud ou localement avec le serveur MFA.

| Emplacement de l'utilisateur | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD et AD local à l'aide de la fédération avec AD FS |● |● |
| Azure AD et AD local à l’aide d’Azure AD Connect - aucune synchronisation de hachage de mot de passe ni authentification directe |● |● |
| Azure AD et AD local à l’aide d’Azure AD Connect - avec synchronisation de hachage de mot de passe et authentification directe |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>Quelles sont les fonctionnalités nécessaires ?

Le tableau suivant compare les fonctionnalités disponibles de Multi-Factor Authentication dans le cloud et du serveur Multi-Factor Authentication.

| Fonctionnalité | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Notification d'application mobile comme second facteur | ● | ● |
| Code de vérification d'application mobile comme second facteur | ● | ● |
| Appel téléphonique comme second facteur | ● | ● |
| SMS unidirectionnel comme second facteur | ● | ● |
| Jetons matériels comme second facteur | ● (Préversion publique) | ● |
| Mots de passe d’application pour les clients Office 365 qui ne prennent pas en charge MFA | ● | |
| Contrôle d'administration sur les méthodes d'authentification | ● | ● |
| Mode du code PIN | | ● |
| Alerte de fraude | ● | ● |
| Rapports MFA | ● | ● |
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
