---
title: Stratégie de référence utilisateur final protection (version préliminaire) - Azure Active Directory
description: Stratégie d’accès conditionnel pour exiger une authentification multifacteur pour les utilisateurs
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35cd52fb82e5e4cce759be5dfdd8872f64802459
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003160"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Stratégie de référence : Protection de l’utilisateur final (version préliminaire)

Nous avons tendance à considérer que les comptes d’administrateur sont les seuls comptes qui requièrent une protection avec l’authentification multifacteur (MFA). Les administrateurs ont un accès étendu à des informations sensibles et apporter des modifications aux paramètres de l’ensemble de l’abonnement. Toutefois, les mauvais acteurs ont tendance pour cibler des utilisateurs finaux. Après avoir obtenu l’accès, ces mauvais acteurs peut demander l’accès à des informations confidentielles au nom du titulaire du compte d’origine ou télécharger l’ensemble du répertoire pour effectuer une attaque par phishing sur toute l’organisation. Une méthode courante pour améliorer la protection pour tous les utilisateurs consiste à exiger une forme plus puissante de vérification du compte, telles que l’authentification multifacteur (MFA).

Pour atteindre un équilibre raisonnable de sécurité et de facilité d’utilisation, les utilisateurs ne doit pas être invités à chaque fois qu’ils connectez-vous. Demandes d’authentification reflétant le comportement d’utilisateur normal, telles que la connexion à partir de la même appareil à partir de l’emplacement, ont un faible risque de compromission. Uniquement connexions qui sont jugées à risque et affichent les caractéristiques d’un acteur doivent être présentées aux défis MFA.

![Exiger l’authentification Multifacteur pour les utilisateurs](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

Protection de l’utilisateur final est une authentification Multifacteur en fonction des risques [stratégie de référence](concept-baseline-protection.md) qui protège tous les utilisateurs dans un répertoire, y compris tous les rôles d’administrateur. L’activation de cette stratégie nécessite tous les utilisateurs à s’inscrire à MFA à l’aide de l’application d’authentification. Les utilisateurs peuvent ignorer l’invite d’inscription MFA pendant 14 jours, après laquelle ils seront bloqués de se connecter jusqu'à ce qu’ils s’inscrivent pour l’authentification Multifacteur. Une fois inscrit pour l’authentification Multifacteur, les utilisateurs seront invités pour l’authentification Multifacteur uniquement pendant les tentatives de connexion à risque. Comptes d’utilisateur compromis sont bloquées jusqu'à ce que son mot de passe est réinitialisé et événements à risque ont été ignorées.

> [!NOTE]
> Cette stratégie s’applique à tous les utilisateurs, y compris les comptes d’invité et doit être évaluée lors de la connexion à toutes les applications.

## <a name="recovering-compromised-accounts"></a>La récupération de compte compromis

Pour protéger nos clients, le service informations d’identification de Microsoft recherche des paires nom d’utilisateur/mot de passe disponible publiquement. Si elles correspondent à une de nos utilisateurs, nous aider à sécuriser ce compte immédiatement. Utilisateurs identifiés comme ayant des informations d’identification volées sont confirmées compromis. Ces utilisateurs ne pourra se connecter jusqu'à ce que son mot de passe est réinitialisé.

Les utilisateurs reçoivent une licence Azure AD Premium peuvent restaurer l’accès via la réinitialisation de mot de passe libre-service (SSPR) si la fonctionnalité est activée dans son annuaire. Les utilisateurs sans licence premium qui sont bloquent doivent contacter un administrateur pour effectuer une réinitialisation de mot de passe manuelle et d’ignorer l’événement à risque utilisateur avec indicateur.

### <a name="steps-to-unblock-a-user"></a>Étapes pour débloquer un utilisateur

Vérifiez que l’utilisateur a été bloqué par la stratégie en examinant les journaux de connexion de l’utilisateur.

1. Un administrateur doit se connecter à la **Azure portal** et accédez à **Azure Active Directory** > **utilisateurs** > cliquez sur le nom de l’utilisateur et accédez à des connexions.
1. Pour lancer le mot de passe réinitialisé sur un utilisateur bloqué, un administrateur doit accéder à **Azure Active Directory** > **utilisateurs avec indicateur de risque**
1. Cliquez sur l’utilisateur dont le compte est bloqué pour afficher des informations sur l’activité de connexion récente de l’utilisateur.
1. Cliquez sur Réinitialiser le mot de passe pour affecter un mot de passe temporaire qui doit être modifié lors de la prochaine connexion.
1. Cliquez sur Ignorer tous les événements pour réinitialiser le score de risque de l’utilisateur.

L’utilisateur peut désormais se connecter, de réinitialiser leur mot de passe et d’accéder à l’application.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Étant donné que le **protection de l’utilisateur final** stratégie s’applique à tous les utilisateurs dans votre répertoire, plusieurs considérations doivent être apportées pour garantir un déploiement sans heurts. Ces considérations incluent l’identification des utilisateurs et les principes de service dans Azure AD qui ne peuvent pas ou ne devez pas effectuer d’authentification Multifacteur, ainsi que les applications et les clients utilisés par votre organisation qui ne prennent pas en charge l’authentification moderne.

### <a name="legacy-protocols"></a>Protocoles hérités

Protocoles d’authentification hérités (IMAP, SMTP, POP3, etc.) sont utilisés par les clients de messagerie pour effectuer des demandes d’authentification. Ces protocoles ne prennent pas en charge MFA.  La plupart de la compromission de compte indiquée par Microsoft est causée par des mauvais acteurs, effectuer des attaques contre des protocoles hérités de contourner l’authentification Multifacteur. Pour garantir l’authentification Multifacteur est requise lors de la connexion à un compte et mauvais acteurs ne sont pas capables de contourner l’authentification Multifacteur, cette stratégie bloque toutes les demandes d’authentification effectuées aux comptes d’administrateur à partir de protocoles hérités.

> [!WARNING]
> Avant d’activer cette stratégie, assurez-vous que vos utilisateurs ne sont pas à l’aide de protocoles d’authentification hérités. Consultez l’article [Comment : L’authentification héritée de bloc à Azure AD avec l’accès conditionnel](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) pour plus d’informations.

### <a name="user-exclusions"></a>Exclusions de l’utilisateur

Cette stratégie de référence vous offre la possibilité d’exclure les utilisateurs. Avant d’activer la stratégie pour votre client, nous recommandons à l’exclusion des comptes suivants :

* **Accès d’urgence** ou **secours** comptes afin d’éviter le verrouillage de compte de l’échelle du client. Dans le scénario improbable où tous les administrateurs sont verrouillés en dehors de votre locataire, votre compte d’administration d’accès d’urgence peut être utilisé pour vous connecter à la procédure client de récupérer l’accès.
   * Vous trouverez plus d’informations dans l’article [gérer les comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Comptes de service** et **principes de service**, telles que le compte de synchronisation de connexion AD Azure. Comptes de service sont des comptes non interactifs qui ne sont pas liés à un utilisateur spécifique. Ils sont normalement utilisés par les services back-end et autorisent l’accès par programmation aux applications. Comptes de service doivent être exclus, car l’authentification Multifacteur ne peut pas être effectuée par programme.
   * Si votre organisation dispose de ces comptes en cours d’utilisation dans des scripts ou du code, envisagez d’en les remplaçant par [gérés identités](../managed-identities-azure-resources/overview.md). En tant que solution de contournement temporaire, vous pouvez exclure ces comptes spécifiques à partir de la stratégie de référence.
* Utilisateurs qui n’ont pas ou ne sera pas en mesure d’utiliser un Smartphone.
   * Cette stratégie nécessite que les utilisateurs de s’inscrire à MFA à l’aide de l’application Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de référence

La stratégie **stratégie de référence : Protection de l’utilisateur final (version préliminaire)** est préconfigurée et s’affichera en haut lorsque vous accédez au panneau d’accès conditionnel dans le portail Azure.

Pour activer cette stratégie et protéger vos administrateurs :

1. Se connecter à la **Azure portal** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **stratégie de référence : Protection de l’utilisateur final (version préliminaire)**.
1. Définissez **activer la stratégie** à **utiliser immédiatement la stratégie**.
1. Ajoutez les exclusions d’utilisateur en cliquant sur **utilisateurs** > **sélectionner les utilisateurs exclus** et en choisissant les utilisateurs qui doivent être exclues. Cliquez sur **sélectionnez** puis **fait**.
1. Cliquez sur **enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection d’accès conditionnel de base](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/azure-ad-secure-steps.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
