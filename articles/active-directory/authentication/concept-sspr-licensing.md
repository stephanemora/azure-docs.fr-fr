---
title: Licence de réinitialisation de mot de passe libre-service - Azure Active Directory
description: Conditions de licence pour la réinitialisation du mot de passe en libre-service dans Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848559"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Conditions de licence pour la réinitialisation du mot de passe en libre-service Azure AD

Azure Active Directory (Azure AD) est proposé en plusieurs éditions : Gratuit, Premium P1 et Premium P2. Il existe plusieurs fonctionnalités qui rendent la réinitialisation du mot de passe libre-service, y compris la modification, la réinitialisation, le déverrouillage et l’écriture différée, qui sont disponibles dans les différentes éditions d’Azure AD. Cet article tente d’expliquer les différences. Vous trouverez plus d’informations sur les fonctionnalités incluses dans chaque édition Azure AD sur la [Page de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Comparer les éditions et les fonctionnalités

La réinitialisation du mot de passe Azure AD libre-service est fournie sous licence par utilisateur, pour s’assurer que les organisations de conformité soient obligées d’assigner la licence appropriée à leurs utilisateurs.

* Modification libre-service du mot de passe pour les utilisateurs du cloud
   * Je suis un **utilisateur cloud uniquement** et je connais mon mot de passe.
      * J’aimerais **modifier** mon mot de passe pour un nouveau.
   * Cette fonctionnalité est incluse dans toutes les éditions d’Azure AD.

* Réinitialisation libre-service du mot de passe pour les utilisateurs du cloud
   * Je suis un **utilisateur cloud uniquement** et j’ai oublié mon mot de passe.
      * J’aimerais **réinitialiser** mon mot de passe pour un nouveau.
   * Cette fonctionnalité est incluse dans les éditions Premium P1 et Premium P2 d’Azure AD, ainsi que dans Microsoft 365 Business ou Office 365.

* Réinitialisation/modification/déverrouillage du mot de passe en libre-service **avec des réécritures locales**
   * Je suis un **utilisateur hybride** mon compte d’utilisateur Active Directory local est synchronisé avec mon compte Azure AD à l’aide d’Azure AD Connect. Je souhaite modifier mon mot de passe, j’ai oublié mon mot de passe, ou il a été verrouillé.
      * Je souhaite modifier mon mot de passe ou le réinitialiser en quelque chose que je connais, ou déverrouiller mon compte, **et** je souhaite que la modification soit synchronisée de nouveau à Active Directory en local.
   * Cette fonctionnalité est incluse dans les éditions Premium P1 et Premium P2 d'Azure AD, ainsi que dans Microsoft 365 Business.

> [!WARNING]
> Les plans de licences Office 365 édition autonome *ne prennent pas en charge les opérations de « réinitialisation/modification/déverrouillage de mot de passe libre-service avec réécriture locale »* et nécessitent un plan qui inclut l'édition Premium P1 ou Premium P2 d'Azure AD, ou Microsoft 365 Business, pour que cette fonctionnalité soit opérationnelle.
>

Vous trouverez des informations de licence supplémentaires, notamment les prix, dans les pages suivantes :

* [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Fonctionnalités Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Entreprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Description du service Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Activer les licences utilisateur ou groupe

Azure AD prend désormais en charge les licences basées sur des groupes. Les administrateurs peuvent attribuer des licences en bloc à un groupe d’utilisateurs, plutôt que de les attribuer une à la fois. Pour plus d’informations, consultez [Affecter, vérifier et résoudre les problèmes de licences](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété **Emplacement d’utilisation** sur l’utilisateur. Vous pouvez effectuer l’attribution de licences dans la section **Utilisateur** > **Profil** > **Paramètres** du portail Azure. *Quand vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement de l’annuaire.*

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](howto-sspr-deployment.md)
* [Réinitialiser ou modifier votre mot de passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](../user-help/active-directory-passwords-reset-register.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](howto-sspr-authenticationdata.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](concept-sspr-howitworks.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](concept-sspr-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](howto-sspr-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](howto-sspr-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](concept-sspr-howitworks.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)
