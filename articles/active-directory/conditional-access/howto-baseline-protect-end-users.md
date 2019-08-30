---
title: Stratégie de base de protection de l’utilisateur final (préversion) – Azure Active Directory
description: Stratégie d’accès conditionnel pour exiger une authentification multifacteur des utilisateurs
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
ms.openlocfilehash: 131d38f6154e7a6e2f3175838b084e47e17ec582
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532926"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Stratégie de base : Protection de l’utilisateur final (préversion)

Nous avons tendance à considérer que les comptes administrateur sont les seuls qui nécessitent une protection par authentification multifacteur (MFA). Les administrateurs ont largement accès à des informations sensibles et peuvent modifier des paramètres à l’échelle d’un abonnement. Pourtant, les cybercriminels ont tendance à cibler les utilisateurs finaux. Après s’être introduits, ils peuvent demander l’accès à des informations confidentielles au nom du titulaire du compte d’origine, ou télécharger l’ensemble du répertoire pour effectuer une attaque par hameçonnage dans toute l’organisation. Une méthode courante pour améliorer la protection de tous les utilisateurs finaux consiste à demander une forme de vérification de compte plus stricte, telle qu’une authentification multifacteur (MFA).

Pour trouver un équilibre raisonnable entre sécurité et convivialité, les utilisateurs ne doivent pas être invités à s’authentifier chaque fois qu’ils se connectent. Des demandes d’authentification correspondant à un comportement normal, telles qu’une connexion à l’aide d’un même appareil depuis un même emplacement, sont peu susceptibles de constituer une menace. Seules les connexions jugées risquées et présentant les caractéristiques d’un accès malveillant devraient justifier une demande d’authentification multifacteur.

La protection de l’utilisateur final est une [stratégie de base](concept-baseline-protection.md) d’authentification multifacteur qui protège tous les utilisateurs au sein d’annuaire, incluant tous les rôles administrateur. L’activation de cette stratégie nécessite que tous les utilisateurs s’inscrivent pour l’authentification multifacteur via l’application d’authentification. Les utilisateurs peuvent ignorer l’invite d’inscription à l’authentification multifacteur pendant 14 jours à l’issue desquels il ne peuvent plus se connecter sans authentification multifacteur. Une fois inscrits à l’authentification multifacteur, les utilisateurs sont invités à s’identifier de cette manière uniquement lors de tentatives de connexion risquées. Les comptes d’utilisateurs compromis sont bloqués jusqu’à ce que leur mot de passe soit réinitialisé et que les risques aient été écartés.

> [!NOTE]
> Cette stratégie vaut pour tous les utilisateurs, y compris les comptes invités, et est appliquée lors de la connexion à toutes les applications.

## <a name="recovering-compromised-accounts"></a>Récupération de comptes compromis

Pour protéger nos clients, le service de Microsoft en charge des informations d’identification ayant fuité recherche des paires nom d’utilisateur/mot de passe accessibles au public. Si elles correspondent à l’un de nos utilisateurs, nous contribuons immédiatement à sécuriser le compte correspondant. Les utilisateurs dont les informations d’identification ont fuité sont confirmés comme étant compromis. Leur connexion est bloquée jusqu’à ce que leur mot de passe soit réinitialisé.

Les utilisateurs titulaires d’une licence Azure AD Premium peuvent restaurer l’accès via une réinitialisation de mot de passe en libre-service (SSPR) si cette fonctionnalité est activée dans leur annuaire. Les utilisateurs dépourvus d’une telle licence Premium qui se retrouvent bloqués doivent demander à un administrateur d’effectuer une réinitialisation manuelle du mot de passe et de supprimer l’indicateur de risque de l’utilisateur.

### <a name="steps-to-unblock-a-user"></a>Étapes pour débloquer un utilisateur

Vérifiez que l’utilisateur a été bloqué par la stratégie en examinant les journaux de connexion de l’utilisateur.

1. Un administrateur doit se connecter au **portail Azure** pour accéder à **Azure Active Directory** > **Utilisateurs**, puis cliquer sur le nom de l’utilisateur et accéder à Connexions.
1. Pour réinitialiser le mot de passe d’un utilisateur bloqué, un administrateur doit accéder à **Azure Active Directory** > **Utilisateurs avec indicateur de risque**.
1. Cliquez sur l’utilisateur dont le compte est bloqué pour afficher les informations concernant son activité de connexion récente.
1. Cliquez sur Réinitialiser le mot de passe pour affecter un mot de passe temporaire qui devra être modifié lors de la prochaine connexion.
1. Cliquez sur Ignorer tous les événements pour réinitialiser l’indice de risque de l’utilisateur.

L’utilisateur peut désormais se connecter, réinitialiser son mot de passe et accéder à l’application.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Étant donné que la stratégie de **protection de l’utilisateur final** s’applique à tous les utilisateurs figurant dans votre annuaire, il convient de prendre plusieurs aspects en considérations pour garantir un déploiement fluide. Ces considérations incluent l’identification des utilisateurs et principaux de service dans Azure AD qui ne peuvent pas ou ne doivent pas utiliser l’authentification multifacteur, ainsi que des applications et clients utilisés par votre organisation qui ne prennent pas en charge l’authentification moderne.

### <a name="legacy-protocols"></a>Protocoles hérités

Les protocoles d’authentification hérités (IMAP, SMTP, POP3, etc.) sont utilisés par les clients de messagerie pour effectuer des demandes d’authentification. Ces protocoles ne prennent pas en charge l’authentification multifacteur.  La plupart des problèmes de compromission de comptes détectés par Microsoft sont causés par des utilisateurs malveillants, qui s’attaquent à des protocoles hérités pour tenter de contourner l’authentification multifacteur. Pour s’assurer que l’authentification multifacteur est bien requise lors de la connexion à un compte et que des utilisateurs malveillants ne peuvent pas la contourner, cette stratégie bloque toutes les demandes d’authentification adressées à des comptes administrateur à partir de protocoles hérités.

> [!WARNING]
> Avant d’activer cette stratégie, assurez-vous que vos utilisateurs n’utilisent aucun protocole d’authentification hérité. Consultez l’article [ Comment bloquer l’authentification héritée sur Azure AD avec l’accès conditionnel](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) pour en savoir plus.

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de base

La stratégie **Stratégie de base : la protection des utilisateurs finaux (préversion)** est préconfigurée et s’affiche dans la partie supérieure de la fenêtre lorsque vous accédez au panneau Accès conditionnel dans le portail Microsoft Azure.

Pour activer cette stratégie et protéger vos utilisateurs, procédez comme suit :

1. Connectez-vous au **portail Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **Stratégie de base : Protection de l’utilisateur final (préversion)** .
1. Définissez **Activer la stratégie** sur **Utiliser la stratégie immédiatement**.
1. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection de base de référence pour l’accès conditionnel](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
