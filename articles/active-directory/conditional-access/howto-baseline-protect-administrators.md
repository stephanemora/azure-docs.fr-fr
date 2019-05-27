---
title: Stratégie de référence nécessitent une authentification Multifacteur pour les administrateurs - Azure Active Directory
description: Stratégie d’accès conditionnel pour exiger une authentification multifacteur pour les administrateurs
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
ms.openlocfilehash: a1ce48126c3e8867ac7f2696d8cf7db992a9a60a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003280"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Stratégie de référence : Demander l’authentification multifacteur pour les administrateurs

Les utilisateurs ayant accès à des comptes privilégiés ont accès sans restriction à votre environnement. En raison de l’importance de ces comptes, vous devez leur accorder une attention particulière. Une méthode courante pour améliorer la protection de comptes privilégiés consiste à demander une forme de vérification de compte plus sévère lorsqu’ils sont utilisés pour se connecter. Dans Azure Active Directory, vous pouvez obtenir une vérification plus sévère des comptes en demandant une authentification multifacteur (MFA).

**Exiger l’authentification Multifacteur pour les administrateurs** est un [stratégie de référence](concept-baseline-protection.md) qui exige l’authentification Multifacteur chaque fois qu’un des rôles d’administrateur privilégiés suivants se connecte :

* Administrateur général
* Administrateur SharePoint
* Administrateur Exchange
* Administrateur de l’accès conditionnel
* Administrateur de sécurité
* Administrateur du support technique / mots de passe administrateur
* Administrateur de facturation
* Administrateur d’utilisateurs

Lors de l’activation de la MFA nécessitent de stratégie des administrateurs, les rôles de neuf administrateur ci-dessus devront s’inscrire à MFA à l’aide de l’application d’authentification. Une fois l’inscription de l’authentification Multifacteur est terminée, les administrateurs doivent utiliser l’authentification Multifacteur chaque fois qu’ils connectez-vous.

![Exiger l’authentification Multifacteur pour la stratégie de base des administrateurs](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Étant donné que le **requièrent une authentification Multifacteur pour les administrateurs** stratégie s’applique à tous les administrateurs critiques, plusieurs considérations doivent être apportées pour garantir un déploiement sans heurts. Ces considérations incluent l’identification des utilisateurs et les principes de service dans Azure AD qui ne peuvent pas ou ne devez pas effectuer d’authentification Multifacteur, ainsi que les applications et les clients utilisés par votre organisation qui ne prennent pas en charge l’authentification moderne.

### <a name="legacy-protocols"></a>Protocoles hérités

Protocoles d’authentification hérités (IMAP, SMTP, POP3, etc.) sont utilisés par les clients de messagerie pour effectuer des demandes d’authentification. Ces protocoles ne prennent pas en charge MFA. La plupart de la compromission de compte indiquée par Microsoft est causée par des mauvais acteurs, effectuer des attaques contre des protocoles hérités de contourner l’authentification Multifacteur. Pour garantir l’authentification Multifacteur est requise lors de la connexion à un compte administrateur et mauvais acteurs ne sont pas capables de contourner l’authentification Multifacteur, cette stratégie bloque toutes les demandes d’authentification effectuées aux comptes d’administrateur à partir de protocoles hérités.

> [!WARNING]
> Avant d’activer cette stratégie, assurez-vous que vos administrateurs ne sont pas à l’aide de protocoles d’authentification hérités. Consultez l’article [Comment : L’authentification héritée de bloc à Azure AD avec l’accès conditionnel](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) pour plus d’informations.

### <a name="user-exclusions"></a>Exclusions de l’utilisateur

Cette stratégie de référence vous offre la possibilité d’exclure les utilisateurs. Avant d’activer la stratégie pour votre client, nous recommandons à l’exclusion des comptes suivants :

* **Accès d’urgence** ou **secours** comptes afin d’éviter le verrouillage de compte de l’échelle du client. Dans le scénario improbable où tous les administrateurs sont verrouillés en dehors de votre locataire, votre compte d’administration d’accès d’urgence peut être utilisé pour vous connecter à la procédure client de récupérer l’accès.
   * Vous trouverez plus d’informations dans l’article [gérer les comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Comptes de service** et **principes de service**, telles que le compte de synchronisation de connexion AD Azure. Comptes de service sont des comptes non interactifs qui ne sont pas liés à un utilisateur spécifique. Ils sont normalement utilisés par les services back-end et autorisent l’accès par programmation aux applications. Comptes de service doivent être exclus, car l’authentification Multifacteur ne peut pas être effectuée par programme.
   * Si votre organisation dispose de ces comptes en cours d’utilisation dans des scripts ou du code, envisagez d’en les remplaçant par [gérés identités](../managed-identities-azure-resources/overview.md). En tant que solution de contournement temporaire, vous pouvez exclure ces comptes spécifiques à partir de la stratégie de référence.
* Utilisateurs qui n’ont pas ou ne sera pas en mesure d’utiliser un Smartphone.
   * Cette stratégie exige des administrateurs pour vous inscrire à MFA à l’aide de l’application Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de référence

La stratégie **stratégie de référence : Exiger l’authentification Multifacteur pour les administrateurs** est préconfigurée et s’affichera en haut lorsque vous accédez au panneau d’accès conditionnel dans le portail Azure.

Pour activer cette stratégie et protéger vos administrateurs :

1. Se connecter à la **Azure portal** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **stratégie de référence : Exiger l’authentification Multifacteur pour les administrateurs**.
1. Définissez **activer la stratégie** à **utiliser immédiatement la stratégie**.
1. Ajoutez les exclusions d’utilisateur en cliquant sur **utilisateurs** > **sélectionner les utilisateurs exclus** et en choisissant les utilisateurs qui doivent être exclues. Cliquez sur **sélectionnez** puis **fait**.
1. Cliquez sur **enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection d’accès conditionnel de base](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/azure-ad-secure-steps.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
