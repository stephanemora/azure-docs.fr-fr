---
title: Stratégies de protection d’accès conditionnel de base - Azure Active Directory
description: Stratégies d’accès conditionnel de base pour protéger les entreprises contre les attaques courantes
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
ms.openlocfilehash: ca062f4024bb5b0946812e00c8ccc1254b56d333
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003295"
---
# <a name="what-are-baseline-policies"></a>Quelles sont les stratégies de base ?

Stratégies de base sont un ensemble de stratégies prédéfinies qui aident à protéger les organisations contre de nombreuses attaques courantes. Ces attaques courantes peuvent inclure anti-hameçonnage, relecture et pulvérisation de mot de passe. Stratégies de base sont disponibles dans toutes les éditions d’Azure AD. Microsoft propose ces stratégies de protection de base pour tout le monde, car les attaques basées sur l’identité ont été à la hausse au cours des quelques dernières années. L’objectif de ces quatre stratégies est pour vous assurer que toutes les organisations ont un niveau de référence de sécurité est activée sur aucun coût supplémentaire.  

La gestion des stratégies d’accès conditionnel personnalisée requiert une licence Azure AD Premium.

## <a name="baseline-policies"></a>Stratégies de base de référence

![Stratégies de base de l’accès conditionnel dans le portail Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Il existe quatre stratégies de base que les organisations peuvent activer :

* [Exiger l’authentification Multifacteur pour les administrateurs](howto-baseline-protect-administrators.md)
* [Protection de l’utilisateur final (version préliminaire)](howto-baseline-protect-end-users.md)
* [Authentification hérités de bloc (version préliminaire)](howto-baseline-protect-legacy-auth.md)
* [Exiger l’authentification Multifacteur pour la gestion de service (version préliminaire)](howto-baseline-protect-azure.md)

Les quatre de ces stratégies aura un impact sur les flux d’authentification hérités telles que POP, IMAP et les clients de bureau Office plus anciens.

### <a name="require-mfa-for-admins"></a>Demander l’authentification multifacteur pour les administrateurs

En raison de la puissance et l’accès des comptes d’administrateur, vous devez les traiter avec une attention spéciale. Une méthode courante pour améliorer la protection des comptes privilégiés consiste à exiger une forme plus puissante de vérification du compte lorsqu’ils sont utilisés pour se connecter. Dans Azure Active Directory, vous pouvez obtenir une vérification du compte plus forte en exigeant que les administrateurs pour vous inscrire et utiliser l’authentification multifacteur Azure.

[Exiger l’authentification Multifacteur pour les administrateurs](howto-baseline-protect-administrators.md) est une stratégie de base qui nécessite une authentification multifacteur (MFA) pour les rôles d’annuaire suivantes, considérées comme les rôles d’Azure AD plus privilégiés :

* Administrateur général
* Administrateur SharePoint
* Administrateur Exchange
* Administrateur de l’accès conditionnel
* Administrateur de sécurité
* Administrateur du support technique / mots de passe administrateur
* Administrateur de facturation
* Administrateur d’utilisateurs

Si votre organisation dispose de ces comptes en cours d’utilisation dans des scripts ou du code, envisagez d’en les remplaçant par [gérés identités](../managed-identities-azure-resources/overview.md). Pour contourner provisoirement le problème, vous pouvez exclure des comptes d’utilisateurs spécifiques de la stratégie de base.

### <a name="end-user-protection-preview"></a>Protection de l’utilisateur final (version préliminaire)

Administrateurs privilégiés élevés ne sont pas les seuls ciblés dans les attaques. Mauvais acteurs ont tendance à cibler les utilisateurs normaux. Après avoir obtenu l’accès, ces mauvais acteurs peut demander l’accès à des informations confidentielles au nom du titulaire du compte d’origine ou télécharger l’ensemble du répertoire et effectuer une attaque par phishing sur toute l’organisation. Une méthode courante pour améliorer la protection pour tous les utilisateurs consiste à exiger une forme plus puissante de vérification du compte lors de la détection d’une connexion à risque sign-in.

**Protection de l’utilisateur final (version préliminaire)** est une stratégie de base qui protège tous les utilisateurs dans un répertoire. L’activation de cette stratégie nécessite tous les utilisateurs à s’inscrire pour l’authentification multifacteur Azure dans les 14 jours. Une fois inscrit, les utilisateurs seront invités pour l’authentification Multifacteur uniquement pendant les tentatives de connexion à risque. Comptes d’utilisateur compromis sont bloquées jusqu'à ce que la réinitialisation de mot de passe et risque de licenciement.

### <a name="block-legacy-authentication-preview"></a>Authentification hérités de bloc (version préliminaire)

Protocoles d’authentification hérités (ex : IMAP, SMTP, POP3) sont normalement utilisés par les clients de messagerie plus anciens pour authentifier des protocoles. Les protocoles hérités ne gèrent pas l’authentification multifacteur. Même si vous avez une stratégie exigeant une authentification multifacteur pour votre annuaire, un acteur peut s’authentifier à l’aide d’un de ces protocoles hérités et contourner l’authentification multifacteur.

La meilleure façon de protéger votre compte à partir de demandes d’authentification malveillants effectuées par les protocoles hérités est de les bloquer.

Le **authentification hérités de bloc (version préliminaire)** stratégie de référence bloque les demandes d’authentification qui sont établies en utilisant les protocoles hérités. L’authentification moderne doit être utilisée pour vous connecter avec succès pour tous les utilisateurs. Utilisé conjointement avec les autres stratégies de base, les demandes provenant des protocoles hérités seront bloquées. En outre, tous les utilisateurs devront MFA chaque fois que nécessaire. Cette stratégie ne bloque pas Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Exiger l’authentification Multifacteur pour la gestion de service (version préliminaire)

Les organisations utilisent une variété de services Azure et les gérer à partir d’Azure Resource Manager en fonction des outils tels que :

* Portail Azure
* Azure PowerShell
* Azure CLI

À l’aide de ces outils pour effectuer la gestion des ressources est une action disposant de privilèges élevés. Ces outils peuvent modifier les configurations à l’échelle de l’abonnement, telles que les paramètres de service et de facturation d’abonnement.

Pour protéger les actions privilégiées, cela **requièrent une authentification Multifacteur pour la gestion de service (version préliminaire)** stratégie nécessiteront l’authentification multifacteur pour tout utilisateur accédant au portail Azure, Azure PowerShell ou Azure CLI.

## <a name="enable-a-baseline-policy"></a>Activer une stratégie de base

Pour activer une stratégie de base :

1. Se connecter à la **Azure portal** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez une stratégie de base que vous souhaitez activer.
1. Définissez **activer la stratégie** à **sur**.
1. Cliquez sur Enregistrer.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/azure-ad-secure-steps.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
* [Exiger l’authentification Multifacteur pour les administrateurs](howto-baseline-protect-administrators.md)
* [Protection de l’utilisateur final (version préliminaire)](howto-baseline-protect-end-users.md)
* [Authentification hérités de bloc (version préliminaire)](howto-baseline-protect-legacy-auth.md)
* [Exiger l’authentification Multifacteur pour la gestion de service (version préliminaire)](howto-baseline-protect-azure.md)