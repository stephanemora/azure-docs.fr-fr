---
title: Présentation d’Azure Multi-Factor Authentication
description: Découvrez comment Azure Multi-Factor Authentication contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur d’un processus d’authentification simple.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10d49331f08a83eb48c232ced2c565935eb2abd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051205"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Fonctionnement : Azure Multi-Factor Authentication

L’authentification multifacteur est un processus dans lequel l’utilisateur est invité pendant le processus de connexion à suivre une forme d’identification supplémentaire, consistant par exemple à entrer un code sur son téléphone portable ou à scanner son empreinte digitale.

L’utilisation d’un mot de passe uniquement ne protège pas complètement des attaques. Si le mot de passe est faible ou s’il a été exposé ailleurs, est-ce vraiment l’utilisateur qui se connecte avec le nom d’utilisateur et le mot de passe, ou s’agit-il d’un attaquant ? Quand vous exigez une deuxième forme d’authentification, la sécurité est accrue, car ce facteur supplémentaire n’est pas un élément facile à obtenir ou à dupliquer par un attaquant.

![Image conceptuelle des différentes formes d’authentification multifacteur](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication impose au minimum deux des méthodes d’authentification suivantes :

* Un élément que vous connaissez, généralement un mot de passe.
* Un élément que vous possédez, tel qu’un appareil de confiance qui n’est pas facilement dupliqué, comme un téléphone ou une clé matérielle.
* Un élément biométrique identifiant votre personne, tel qu’une empreinte digitale ou un scan du visage.

Les utilisateurs peuvent s’inscrire à la réinitialisation de mot de passe en libre-service et à Azure Multi-Factor Authentication en une seule étape pour simplifier l’expérience d’intégration. Les administrateurs peuvent définir les formes d’authentification secondaire qui peuvent être utilisées. Azure Multi-Factor Authentication peut également être nécessaire quand les utilisateurs effectuent une réinitialisation de mot de passe en libre-service pour sécuriser davantage ce processus.

![Méthodes d’authentification en cours d’utilisation sur l’écran de connexion](media/concept-authentication-methods/overview-login.png)

Azure Multi-Factor Authentication participe à la sécurisation de l’accès aux données et aux applications tout en maintenant une simplicité de gestion pour les utilisateurs. MFA fournit une sécurité supplémentaire en exigeant une deuxième forme d’authentification, et procure une authentification renforcée par le biais d’un éventail de [méthodes d’authentification](concept-authentication-methods.md) faciles à utiliser. Les utilisateurs peuvent devoir s'authentifier via MFA selon les choix de configuration de l'administrateur.

Vos applications ou services n’ont pas besoin d’apporter de modifications pour utiliser Azure Multi-Factor Authentication. Les invites de vérification font partie de l’événement de connexion Azure AD, qui demande et traite automatiquement le défi MFA lorsque cela est nécessaire.

## <a name="available-verification-methods"></a>Méthodes de vérification disponibles

Lorsqu’un utilisateur se connecte à une application ou à un service et reçoit une invite MFA, il peut choisir l’une des formes inscrites de vérification supplémentaire dont il dispose. Un administrateur peut exiger l’inscription de ces méthodes de vérification Azure Multi-Factor Authentication, ou l’utilisateur peut accéder à sa propre page [Mon profil](https://myprofile.microsoft.com) pour modifier ou ajouter des méthodes de vérification.

Les autres formes de vérification suivantes peuvent être utilisées avec Azure Multi-Factor Authentication :

* Application Microsoft Authenticator
* Jetons matériels OATH
* sms
* Appel vocal

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Comment activer et utiliser Azure Multi-Factor Authentication

Les utilisateurs et les groupes peuvent être activés pour qu’Azure Multi-Factor Authentication demande une vérification supplémentaire dans le cadre de l’événement de connexion. Les [paramètres de sécurité par défaut](../fundamentals/concept-fundamentals-security-defaults.md) sont disponibles pour tous les locataires Azure AD afin d’activer rapidement l’utilisation de l’application Microsoft Authenticator pour tous les utilisateurs.

Pour des contrôles plus précis, des stratégies d’[accès conditionnel](../conditional-access/overview.md) peuvent être utilisées pour définir des événements ou des applications qui demandent MFA. Ces stratégies peuvent autoriser des événements de connexion standard lorsque l’utilisateur se trouve sur le réseau d’entreprise ou sur un appareil inscrit, mais demander des facteurs de vérification supplémentaires lorsqu’il se connecte à distance ou sur un appareil personnel.

![Diagramme de vue d’ensemble du fonctionnement de l’accès conditionnel pour sécuriser le processus de connexion](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre davantage sur les licences, consultez [Fonctionnalités et licences pour Azure Multi-Factor Authentication](concept-mfa-licensing.md).

Pour voir l’authentification MFA en action, activez Azure Multi-Factor Authentication pour un ensemble d’utilisateurs de test dans le tutoriel suivant :

> [!div class="nextstepaction"]
> [Activation d’Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
