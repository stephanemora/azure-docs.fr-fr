---
title: Stratégie de mot de passe combinée et vérification de mot de passe faible dans Azure Active Directory
description: En savoir plus sur la stratégie de mot de passe combinée et la vérification de mot de passe faible dans Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: justinha
author: sajiang
manager: daveba
ms.reviewer: sajiang
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21fb52047822df5c14e8b5a21c017a2e4ca5f1d6
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138768"
---
# <a name="combined-password-policy-and-weak-password-check-in-azure-active-directory"></a>Stratégie de mot de passe combinée et vérification de mot de passe faible dans Azure Active Directory

À compter d’octobre 2021 octobre, la validation Azure Active Directory (Azure AD) de la conformité avec les stratégies de mot de passe comprend également une vérification des [mots de passe faibles connus](concept-password-ban-bad.md) et de leurs variantes. À mesure que le contrôle combiné pour la stratégie de mot de passe et les mots de passe interdits est déployé pour les locataires, Azure AD et les utilisateurs du centre d’administration Office 365 risquent de constater des différences lorsqu’ils créent, modifient ou réinitialisent leur mot de passe. Cette rubrique explique en détail les critères de stratégie de mot de passe vérifiés par Azure AD. 

## <a name="azure-ad-password-policies"></a>Stratégies de mot de passe Azure AD

La stratégie de mot de passe est appliquée à tous les comptes utilisateur créés et gérés directement dans Azure AD. Vous pouvez [interdire les mots de passe faibles](concept-password-ban-bad.md) et définir des paramètres pour [verrouiller un compte](howto-password-smart-lockout.md) après plusieurs mots de passe erronés. Les autres paramètres de stratégie de mot de passe ne peuvent pas être modifiés.

La stratégie de mot de passe Azure AD ne s’applique pas aux comptes d’utilisateur synchronisés à partir d’un environnement AD DS local à l’aide d’Azure AD Connect, sauf si vous activez EnforceCloudPasswordPolicyForPasswordSyncedUsers.

Les exigences de stratégie de mot de passe Azure AD suivantes s’appliquent à tous les mots de passe créés, modifiés ou réinitialisés dans Azure AD. Ces exigences sont appliquées lors de l’approvisionnement des utilisateurs, de la modification des mots de passe et dans le cadre de la réinitialisation du mot de passe. Sauf indication contraire, vous ne pouvez pas modifier ces paramètres.

| Propriété | Spécifications |
| --- | --- |
| Caractères autorisés |Caractères majuscules (A - Z)<br>Caractères minuscules (a - z)<br>Chiffres (0 - 9)<br>Symboles :<br>- @ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ><br>- espace vide |
| Caractères non autorisés | Caractères Unicode |
| Longueur du mot de passe |Les mots de passe nécessitent<br>- 8 caractères au minimum<br>- 256 caractères au maximum</li> |
| Complexité du mot de passe |Les mots de passe exigent trois des quatre éléments suivants :<br>- caractères en majuscules<br>- caractères en minuscules<br>- chiffres <br>- symboles<br> Remarque : la vérification de la complexité des mots de passe n’est pas requise pour les locataires Education. |
| Mot de passe pas utilisé récemment | Lorsqu’un utilisateur modifie ou réinitialise son mot de passe, le nouveau mot de passe ne peut pas être le même que le mot de passe actuel ou qu’un mot de passe récemment utilisé. |
| Le mot de passe n’est pas interdit par [Protection par mot de passe Azure AD](concept-password-ban-bad.md) | Le mot de passe ne peut pas figurer dans la liste globale des mots de passe interdits pour Protection par mot de passe Azure AD ou figurer dans la liste personnalisable de mots de passe interdits dans votre organisation. |

## <a name="password-expiration-policies"></a>Stratégies d’expiration de mot de passe

Les stratégies d’expiration de mot de passe sont inchangées, mais elles sont incluses dans cette rubrique à des fins d’exhaustivité. Un *administrateur général* ou un *administrateur d’utilisateurs* peut utiliser le [module Microsoft Azure AD pour Windows PowerShell](/powershell/module/Azuread/) afin de définir des mots de passe utilisateur qui n’expirent pas.

> [!NOTE]
> Par défaut, seuls les mots de passe de comptes d’utilisateurs non synchronisés par le biais d’Azure AD Connect peuvent être configurés pour ne pas expirer. Pour plus d’informations sur la synchronisation d’annuaires, consultez [Connecter AD à Azure AD](../hybrid/how-to-connect-password-hash-synchronization.md#password-expiration-policy).

Vous pouvez également utiliser PowerShell pour supprimer la configuration de l’absence d’expiration ou pour voir quels mots de passe utilisateur sont définis pour ne jamais expirer.

Les exigences d’expiration suivantes s’appliquent aux autres fournisseurs qui utilisent Azure AD pour les services d’identité et d’annuaire, par exemple Intune et Microsoft 365. 

| Propriété | Spécifications |
| --- | --- |
| Durée d’expiration du mot de passe (âge maximum du mot de passe) |<ul><li>Valeur par défaut : **90** jours.</li><li>La valeur peut être configurée à l’aide de l’applet de commande `Set-MsolPasswordPolicy` à partir du module Azure Active Directory pour Windows PowerShell.</li></ul> |
| Notification d’expiration du mot de passe (lorsque les utilisateurs sont avertis de l’expiration du mot de passe) |<ul><li>Valeur par défaut : **14** jours (avant l’expiration du mot de passe).</li><li>La valeur peut être configurée à l’aide de l’applet de commande `Set-MsolPasswordPolicy`.</li></ul> |
| Expiration du mot de passe (empêche le mot de passe d’expirer) |<ul><li>Valeur par défaut : **false** (indique que le mot de passe a une date d’expiration).</li><li>La valeur peut être configurée pour des comptes d’utilisateur individuels à l’aide de l’applet de commande `Set-MsolUser`.</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies de mot de passe et restrictions de compte dans Azure Active Directory](concept-sspr-policy.md)
- [Éliminer les mauvais mots de passe à l’aide de Protection de mots de passe d’Azure Active Directory](concept-password-ban-bad.md)
