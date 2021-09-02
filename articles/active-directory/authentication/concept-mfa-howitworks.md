---
title: Présentation d'Azure AD Multi-Factor Authentication
description: Découvrez comment Azure AD Multi-Factor Authentication permet de sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur lors d'un processus de connexion simple.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 133ec8c46bb227f2f43cb9243ca5db4a4e9ac32b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562734"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Fonctionnement : Authentification multifacteur Azure AD

L’authentification multifacteur est un processus dans lequel l’utilisateur est invité pendant le processus de connexion à suivre une forme d’identification supplémentaire, consistant par exemple à entrer un code sur son téléphone portable ou à scanner son empreinte digitale.

L’utilisation d’un mot de passe uniquement ne protège pas complètement des attaques. Si le mot de passe est faible ou s’il a été exposé ailleurs, est-ce vraiment l’utilisateur qui se connecte avec le nom d’utilisateur et le mot de passe, ou s’agit-il d’un attaquant ? Quand vous exigez une deuxième forme d’authentification, la sécurité est accrue, car ce facteur supplémentaire n’est pas un élément facile à obtenir ou à dupliquer par un attaquant.

![Image conceptuelle des différentes formes d’authentification multifacteur](./media/concept-mfa-howitworks/methods.png)

L’authentification multifacteur Azure AD impose au minimum deux des méthodes d’authentification suivantes :

* Un élément que vous connaissez, généralement un mot de passe.
* Un élément que vous possédez, tel qu’un appareil de confiance qui n’est pas facilement dupliqué, comme un téléphone ou une clé matérielle.
* Un élément biométrique identifiant votre personne, tel qu’une empreinte digitale ou un scan du visage.

Azure Active Directory Multifactor Authentication peut également renforcer la sécurité de votre mot de passe. Lorsque les utilisateurs s’inscrivent pour Azure AD Multi-Factor Authentication, ils peuvent également s’inscrire à la réinitialisation du mot de passe en libre-service en une seule étape. Les administrateurs peuvent choisir des formes d’authentification secondaire et configurer des défis pour l’authentification multifacteur en fonction des décisions de configuration. 

Aucune modification n'est nécessaire au niveau de vos applications ou services pour utiliser Azure AD Multi-Factor Authentication. Les invites de vérification font partie de l’événement de connexion Azure AD, qui demande et traite automatiquement le défi MFA lorsque cela est nécessaire.

![Méthodes d’authentification en cours d’utilisation sur l’écran de connexion](media/concept-authentication-methods/overview-login.png)

## <a name="available-verification-methods"></a>Méthodes de vérification disponibles

Lorsqu’un utilisateur se connecte à une application ou à un service et reçoit une invite MFA, il peut choisir l’une des formes inscrites de vérification supplémentaire dont il dispose. Les utilisateurs peuvent accéder à [Mon profil](https://myprofile.microsoft.com) pour modifier ou jouter des méthodes de vérification.

Les autres formes de vérification suivantes peuvent être utilisées avec Azure AD Multi-Factor Authentication :

* Application Microsoft Authenticator
* Jeton matériel OATH (préversion)
* Jeton logiciel OATH
* sms
* Appel vocal

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Activer et utiliser Azure AD Multi-Factor Authentication

Tous les locataires Azure AD peuvent utiliser des [paramètres de sécurité par défaut](../fundamentals/concept-fundamentals-security-defaults.md) afin d’activer rapidement Microsoft Authenticator pour tous les utilisateurs. Azure AD Multi-Factor Authentication peut être activé pour inviter les utilisateurs et les groupes à se soumettre à une vérification supplémentaire dans le cadre de l'événement de connexion. 

Pour des contrôles plus précis, des stratégies d’[accès conditionnel](../conditional-access/overview.md) peuvent être utilisées pour définir des événements ou des applications qui demandent MFA. Ces stratégies peuvent autoriser des événements de connexion standard lorsque l’utilisateur se trouve sur le réseau d’entreprise ou sur un appareil inscrit, mais demander des facteurs de vérification supplémentaires lorsqu’il se connecte à distance ou sur un appareil personnel.

![Diagramme de vue d’ensemble du fonctionnement de l’accès conditionnel pour sécuriser le processus de connexion](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les licences, consultez [Fonctionnalités et licences Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

Pour en savoir plus sur les différentes méthodes d’authentification et de validation, consultez les [méthodes d’authentification dans Azure Active Directory](concept-authentication-methods.md).

Pour avoir un aperçu de l'authentification MFA, appliquez Azure AD Multi-Factor Authentication à un ensemble d'utilisateurs de test dans le tutoriel suivant :

> [!div class="nextstepaction"]
> [Activer l’authentification multifacteur Azure AD](./tutorial-enable-azure-mfa.md)