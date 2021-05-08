---
title: Identity Protection et utilisateurs B2B - Azure Active Directory
description: Utilisation d’Identity Protection avec les utilisateurs B2B
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 094b2ee101b10da4ec6eda9d6537656e5de28e54
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891034"
---
# <a name="identity-protection-and-b2b-users"></a>Utilisateurs Identity Protection et B2B

Identity Protection détecte les informations d’identification compromises pour les utilisateurs d’Azure AD. Si vos informations d’identification sont détectées comme étant compromises, cela signifie qu’une autre personne est susceptible d’avoir votre mot de passe et de l’utiliser de manière illégitime. Pour éviter tout risque supplémentaire pour votre compte, il est important de réinitialiser votre mot de passe de manière sécurisée afin que le mauvais intervenant ne puisse plus utiliser votre mot de passe compromis. Identity Protection marque les comptes susceptibles d’être compromis comme étant « à risque ».

Vous pouvez utiliser les informations d’identification de votre organisation pour vous connecter à une autre organisation en tant qu’invité. Ce processus est appelé authentification B2B. Les organisations peuvent configurer des stratégies pour empêcher les utilisateurs de se connecter si leurs informations d’identification sont à risque. Si votre compte est à risque et que vous ne parvenez pas à vous connecter à une autre organisation en tant qu’invité, vous pourrez peut-être corriger automatiquement votre compte en suivant les étapes suivantes. Si votre organisation n’a pas activé la réinitialisation de mot de passe en libre-service, votre administrateur devra corriger manuellement votre compte.

## <a name="how-to-unblock-your-account"></a>Comment débloquer votre compte 

Si vous tentez de vous connecter à une autre organisation en tant qu’invité et que vous êtes bloqué en raison d’un risque, le message de blocage suivant s’affiche : « Votre compte est bloqué. Nous avons détecté une activité suspecte sur votre compte. » 

![Compte invité bloqué, contactez l’administrateur de votre organisation](./media/concept-identity-protection-b2b/risky-guest-user-blocked.png)

Si votre organisation le permet, vous pouvez utiliser la réinitialisation de mot de passe en libre-service pour débloquer votre compte et rétablir la sécurité de vos informations d’identification.
1. Accédez au [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/) et lancez la réinitialisation du mot de passe. Si la réinitialisation de mot de passe en libre-service n’est pas activée pour votre compte et que vous ne pouvez pas poursuivre la procédure, contactez votre administrateur informatique avec les informations [ci-dessous](#how-to-remediate-a-users-risk-as-an-administrator).
2. Si la réinitialisation de mot de passe en libre-service est activée pour votre compte, vous serez invité à vérifier votre identité à l’aide de méthodes de sécurité avant de modifier votre mot de passe. Pour obtenir de l’aide, consultez l’article [Réinitialiser votre mot de passe professionnel ou scolaire](../user-help/active-directory-passwords-update-your-own-password.md).
3. Une fois que vous avez réinitialisé votre mot de passe en toute sécurité, le risque est corrigé. Vous pouvez maintenant réessayer de vous connecter en tant qu’utilisateur invité.

Si, après avoir réinitialisé votre mot de passe, vous êtes toujours bloqué en tant qu’invité en raison d’un risque, contactez l’administrateur informatique de votre organisation.

## <a name="how-to-remediate-a-users-risk-as-an-administrator"></a>Comment corriger le risque d’un utilisateur en tant qu’administrateur

Identity Protection détecte automatiquement les utilisateurs à risque pour les locataires Azure AD. Si vous n’avez pas vérifié les rapports d’Identity Protection auparavant, il peut y avoir un grand nombre d’utilisateurs présentant un risque. Étant donné que les locataires de ressources peuvent appliquer des stratégies de risque utilisateur aux utilisateurs invités, vos utilisateurs peuvent être bloqués en raison d’un risque, même s’ils n’étaient pas conscients de leur état à risque auparavant. Si votre utilisateur signale qu’il a été bloqué en tant qu’utilisateur invité dans un autre locataire en raison d’un risque, il est important de corriger l’utilisateur pour protéger son compte et permettre la collaboration. 

### <a name="reset-the-users-password"></a>Réinitialisez le mot de passe de l'utilisateur.

Dans le [rapport Utilisateurs à risque](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers) du menu Sécurité Azure AD, recherchez l’utilisateur concerné à l’aide du filtre « Utilisateur ». Sélectionnez l’utilisateur concerné dans le rapport et cliquez sur « Réinitialiser le mot de passe » dans la barre d’outils supérieure. L’utilisateur se verra attribuer un mot de passe temporaire qu’il devra modifier lors de sa prochaine connexion. Ce processus corrige le risque de l’utilisateur et rétablit la sécurité de ses informations d’identification.

### <a name="manually-dismiss-users-risk"></a>Ignorer manuellement le risque de l’utilisateur

Si la réinitialisation de mot de passe n’est pas une option pour vous à partir du portail Azure AD, vous pouvez choisir d’ignorer manuellement le risque de l’utilisateur. Ce processus fera en sorte que l’utilisateur ne sera plus à risque, mais n’aura aucun impact sur le mot de passe actuel. Il est important que vous modifiez le mot de passe de l’utilisateur en utilisant tous les moyens à votre disposition afin de rétablir la sécurité de l’identité. 

Pour ignorer le risque de l’utilisateur, accédez au [rapport Utilisateurs à risque](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers) dans le menu Sécurité Azure AD. Recherchez l’utilisateur concerné à l’aide du filtre « Utilisateur », puis cliquez sur l’utilisateur. Cliquez sur l’option « Ignorer le risque de l’utilisateur » dans la barre d’outils supérieure. Cette action peut prendre quelques minutes pour se terminer et mettre à jour l’état de risque de l’utilisateur dans le rapport.

Pour en savoir plus sur Identity Protection, consultez [Qu’est-ce qu’Identity Protection](overview-identity-protection.md).

## <a name="how-does-identity-protection-work-for-b2b-users"></a>Comment Identity Protection fonctionne-t-il pour les utilisateurs B2B ?

Le risque de l’utilisateur pour les utilisateurs de la collaboration B2B est évalué dans leur répertoire de départ. Le risque de connexion en temps réel pour ces utilisateurs est évalué dans le répertoire de ressources lorsqu’ils essaient d’accéder à la ressource. Grâce à la collaboration entre Azure AD et B2B, les organisations peuvent appliquer des stratégies basées sur les risques pour les utilisateurs B2B à l’aide d’Identity Protection. Ces stratégies doivent être configurées de deux manières :

- Les administrateurs peuvent configurer les stratégies intégrées d’Identity Protection basées sur les risques, qui s’appliquent à toutes les applications, y compris les utilisateurs invités.
- Les administrateurs peuvent configurer leurs stratégies d’accès conditionnel, en utilisant le risque de connexion comme condition, qui comprend les utilisateurs invités.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Limitations d’Identity Protection pour les utilisateurs de la collaboration B2B

Il existe des limitations dans l’implémentation d’Identity Protection pour les utilisateurs de la collaboration B2B dans un répertoire de ressources, car leur identité existe dans leur répertoire de départ. Les limitations principales sont les suivantes :

- Si un utilisateur invité déclenche la stratégie de risque de l’utilisateur Identity Protection pour forcer la réinitialisation du mot de passe, **il sera bloqué**. Ce bloc est dû à l’impossibilité de réinitialiser les mots de passe dans le répertoire des ressources.
- **Les utilisateurs invités n’apparaissent pas dans le rapport utilisateurs à risque**. Cette perte de visibilité est due à l’évaluation des risques se produisant dans le répertoire de départ de l’utilisateur B2B.
- Les administrateurs **ne peuvent pas ignorer ou corriger un utilisateur à risque de la collaboration B2B** dans leur répertoire de ressources. Cette perte de fonctionnalité est due au fait que les administrateurs du répertoire de ressources n’ont pas accès au répertoire de départ de l’utilisateur B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Pourquoi ne puis-je pas corriger les utilisateurs à risque de la collaboration B2B dans mon annuaire ?

L’évaluation et la correction des risques pour les utilisateurs B2B se produisent dans leur répertoire de départ. En raison de ce fait, les utilisateurs invités n’apparaissent pas dans le rapport utilisateurs à risque dans le répertoire des ressources, et les administrateurs dans le répertoire de ressources ne peuvent pas forcer la réinitialisation de mot de passe sécurisé pour ces utilisateurs.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Que dois-je faire si un utilisateur de la collaboration B2B a été bloqué en raison d’une stratégie basée sur les risques dans mon organisation ?

Si un utilisateur B2B risqué dans votre répertoire est bloqué par votre stratégie basée sur des risques, l’utilisateur doit corriger ce risque dans son répertoire de départ. Les utilisateurs peuvent corriger ce risque en effectuant une réinitialisation sécurisée du mot de passe dans leur répertoire de base, [comme indiqué ci-dessus](#how-to-unblock-your-account). Si la réinitialisation de mot de passe en libre-service n’est pas activée dans son répertoire de départ, l’utilisateur doit contacter le personnel informatique de son organisation pour qu’un administrateur fasse disparaître manuellement le risque ou réinitialiser son mot de passe.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Comment puis-je éviter que les utilisateurs de la collaboration B2B ne soient affectés par les politiques basées sur les risques ?

L’exclusion des utilisateurs B2B des stratégies d’accès conditionnel en fonction des risques de votre organisation empêchera les utilisateurs B2B d’être affectés ou bloqués par leur évaluation des risques. Pour exclure ces utilisateurs B2B, créez un groupe dans Azure AD qui contient tous les utilisateurs invités de votre organisation. Ensuite, ajoutez ce groupe en tant qu’exclusion à vos stratégies intégrées de risque utilisateur et de risque de connexion Identity Protection, ainsi qu’à toutes les stratégies d’accès conditionnel qui utilisent le risque de connexion comme condition.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](../external-identities/what-is-b2b.md)
