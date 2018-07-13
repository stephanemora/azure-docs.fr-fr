---
title: Licence de réinitialisation de mot de passe libre-service - Azure Active Directory
description: Conditions de licence pour la réinitialisation du mot de passe en libre-service dans Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 64c12177e5cf6c82018731b493c0da22e1895b7f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855522"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Conditions de licence pour la réinitialisation du mot de passe en libre-service Azure AD

Pour que la réinitialisation du mot de passe Azure Active Directory (Azure AD) fonctionne, vous *devez disposer d’au moins une licence affectée dans votre organisation* pour cet utilisateur. Une licence appropriée est requise si un utilisateur bénéficie directement ou indirectement d’une fonctionnalité couverte par cette licence.

* **Utilisateurs du cloud uniquement** : n’importe quelle référence Office 365 payante ou Azure AD Basic
* Utilisateurs **cloud** ou **locaux** : Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Microsoft 365

## <a name="licensing-requirements-for-password-writeback"></a>Conditions de licence pour la réécriture du mot de passe

**Réinitialisation/modification/déverrouillage de mot de passe libre-service avec écriture différée locale est une fonctionnalité Premium d’Azure AD**. Pour plus d’informations sur les licences, consultez la [page des tarifs Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Pour que vous puissiez utiliser la réécriture du mot de passe, il faut que l’une des licences suivantes soit attribué sur votre locataire :

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou A3
* Enterprise Mobility + Security E5 ou A5
* Microsoft 365 E3 ou A3
* Microsoft 365 E5 ou A5
* Microsoft 365 F1

> [!WARNING]
> Les plans de licences Office 365 édition autonome *ne prennent pas en charge la réécriture du mot de passe* et nécessitent l’un des plans précédents pour que cette fonctionnalité soit opérationnelle.
>

Vous trouverez des informations de licence supplémentaires, notamment les prix, dans les pages suivantes :

* [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Fonctionnalités Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Entreprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Activer les licences utilisateur ou groupe

Azure AD prend désormais en charge les licences basées sur des groupes. Les administrateurs peuvent attribuer des licences en bloc à un groupe d’utilisateurs, plutôt que de les attribuer une à la fois. Pour plus d’informations, consultez [Affecter, vérifier et résoudre les problèmes de licences](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété **Emplacement d’utilisation** sur l’utilisateur. Vous pouvez effectuer l’attribution de licences dans la section **Utilisateur** > **Profil** > **Paramètres** du portail Azure. *Quand vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement de l’annuaire.*

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](howto-sspr-deployment.md)
* [Réinitialiser ou modifier votre mot de passe](../active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](../active-directory-passwords-reset-register.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](howto-sspr-authenticationdata.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](concept-sspr-howitworks.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](concept-sspr-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](howto-sspr-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](howto-sspr-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](concept-sspr-howitworks.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)
