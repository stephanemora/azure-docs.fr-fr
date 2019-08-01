---
title: 'Stratégie de référence : Exiger l’authentification multifacteur pour les administrateurs - Azure Active Directory'
description: La stratégie d’accès conditionnel nécessite l’authentification multifacteur pour les administrateurs
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
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608162"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Stratégie de référence : Exiger l’authentification multifacteur pour les administrateurs (préversion)

Les utilisateurs ayant accès à des comptes privilégiés ont accès sans restriction à votre environnement. En raison de l’importance de ces comptes, vous devez leur accorder une attention particulière. Une méthode courante pour améliorer la protection de comptes privilégiés consiste à demander une forme de vérification de compte plus sévère lorsqu’ils sont utilisés pour se connecter. Dans Azure Active Directory, vous pouvez obtenir une vérification plus sévère des comptes en demandant une authentification multifacteur (MFA).

La stratégie **Exiger l’authentification multifacteur pour les administrateurs (préversion)**  est une [stratégie de référence](concept-baseline-protection.md) qui exige l’authentification multifacteur chaque fois qu’un des rôles d’administrateur avec élévation de privilèges suivants se connecte :

* Administrateur général
* Administrateur SharePoint
* Administrateur Exchange
* Administrateur de l’accès conditionnel
* Administrateur de sécurité
* Administrateur de mot de passe/Administrateur du support technique
* Administrateur de facturation
* Administrateur d’utilisateurs

Lors de l’activation de la stratégie Exiger l’authentification multifacteur pour les administrateurs, les neuf rôles d’administrateur ci-dessus sont requis pour l’inscription à l’authentification multifacteur à l’aide de l’application Authenticator. Une fois l’inscription à l’authentification multifacteur terminée, les administrateurs devront utiliser cette dernière à chaque connexion.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Comme la stratégie **Exiger l’authentification multifacteur pour les administrateurs** s’applique à tous les administrateurs critiques, vous devez tenir compte de différentes considérations pour assurer un déploiement harmonieux. Ces considérations incluent l’identification, dans Azure AD, des utilisateurs et des principes de service qui ne peuvent pas ou ne doivent pas utiliser l’authentification multifacteur, ainsi que des applications et clients utilisés par votre organisation qui ne prennent pas en charge l’authentification moderne.

### <a name="legacy-protocols"></a>Protocoles hérités

Les protocoles d’authentification hérités (IMAP, SMTP, POP3, etc.) sont utilisés par les clients de messagerie pour effectuer des demandes d’authentification. Ces protocoles ne prennent pas en charge l’authentification multifacteur. La plupart des problèmes de compromission de comptes détectés par Microsoft sont causés par des utilisateurs malveillants, qui s’attaquent à des protocoles hérités pour tenter de contourner l’authentification multifacteur. Pour s’assurer que l’authentification multifacteur est bien requise lors de la connexion à un compte administrateur et que les utilisateurs malveillants ne peuvent pas la contourner, cette stratégie bloque toutes les demandes d’authentification effectuées auprès de comptes d’administrateur à partir de protocoles hérités.

> [!WARNING]
> Avant d’activer cette stratégie, assurez-vous que vos administrateurs n’utilisent aucun protocole d’authentification hérité. Consultez l’article [ Comment bloquer l’authentification héritée sur Azure AD avec l’accès conditionnel](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) pour en savoir plus.

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de base

La stratégie **Stratégie de référence : Exiger l’authentification multifacteur pour les administrateurs (préversion)** est préconfigurée et s’affiche sur la partie supérieure de la fenêtre lorsque vous accédez au panneau Accès conditionnel dans le portail Microsoft Azure.

Pour activer cette stratégie et protéger vos administrateurs, procédez comme suit :

1. Connectez-vous au  **portail Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **Stratégie de référence : Exiger l’authentification multifacteur pour les administrateurs (préversion)** .
1. Définissez **Activer la stratégie** sur **Utiliser la stratégie immédiatement**.
1. Cliquez sur  **Enregistrer**.

> [!WARNING]
> Lorsque cette stratégie était en préversion, l’option **Activer automatiquement la stratégie à l’avenir** était disponible, mais nous l’avons supprimée, afin d’éviter un impact soudain sur l’utilisateur. Si vous avez sélectionné cette option lorsqu’elle était disponible, l’option **Ne pas utiliser la stratégie** est automatiquement sélectionnée. S’ils souhaitent utiliser cette stratégie de référence, consultez les étapes ci-dessus pour l’activer.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection de base de référence pour l’accès conditionnel](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
