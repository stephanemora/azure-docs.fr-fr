---
title: Inscription en libre-service pour les utilisateurs vérifiés par e-mail - Azure AD | Microsoft Docs
description: Utiliser l’inscription en libre-service dans une organisation Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 05/19/2021
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb1869c48e38bdaf56fe4bd428a7bcbd6d316b85
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110535698"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Présentation de l’inscription en libre-service pour Azure Active Directory

Cet article explique comment utiliser l’inscription en libre-service pour renseigner une organisation dans Azure Active Directory (Azure AD). Si vous souhaitez prendre le contrôle d’un nom de domaine d’une organisation Azure AD non managée, consultez [Prendre le contrôle d’un locataire non managé en tant qu’administrateur](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Pourquoi utiliser l’inscription libre-service ?

* Permettre aux clients de bénéficier plus rapidement des services dont ils ont besoin.
* Créer des offres envoyées par e-mail pour un service.
* Créer des flux d’inscription par e-mail permettant aux utilisateurs de créer rapidement des identités à l’aide de leurs alias de messagerie professionnelle, faciles à mémoriser
* Un locataire Azure AD créé en libre-service peut être transformé en locataire managé pouvant être utilisé pour d’autres services

## <a name="terms-and-definitions"></a>Termes et définitions

* **Inscription libre- service** : méthode selon laquelle un utilisateur s‘abonne à un service cloud et bénéficie automatiquement d’une identité créée pour lui dans Azure AD en fonction de son domaine de messagerie.
* **Locataire Azure AD non managé** : il s’agit du locataire dans lequel cette identité est créée. Un locataire non managé est un locataire sans administrateur général.
* **Utilisateur vérifié par e-mail** : type de compte d’utilisateur dans Azure AD. Un utilisateur qui possède une identité créée automatiquement après s’être abonné à une offre libre-service est considéré comme un utilisateur vérifié par e-mail. Un utilisateur vérifié par e-mail est un membre ordinaire d’un locataire avec l’étiquette creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Comment vérifier les paramètres libre-service ?

Les administrateurs peuvent désormais effectuer deux vérifications libre-service. Ils peuvent contrôler si :

* Les utilisateurs peuvent joindre le locataire par e-mail
* Les utilisateurs peuvent s’abonner eux-mêmes aux applications et services

### <a name="how-can-i-control-these-capabilities"></a>Comment puis-je vérifier ces fonctionnalités ?

Un administrateur peut configurer ces fonctionnalités à l’aide des paramètres Set-MsolCompanySettings d’applet de commande Azure AD suivants :

* **AllowEmailVerifiedUsers** contrôle si les utilisateurs peuvent joindre le locataire via la validation par e-mail. Pour le joindre, l’utilisateur doit avoir une adresse e-mail dans un domaine qui correspond à l’un des domaines vérifiés dans le locataire. Ce paramètre est appliqué à l’ensemble de l’entreprise pour tous les domaines du locataire. Si vous définissez ce paramètre sur $false, aucun utilisateur vérifié par e-mail ne peut joindre le locataire.
* **AllowAdHocSubscriptions** vérifie la capacité des utilisateurs à réaliser une inscription libre-service. Si vous définissez ce paramètre sur $false, aucun utilisateur ne peut effectuer une inscription libre-service.
  
AllowEmailVerifiedUsers et AllowAdHocSubscriptions sont des paramètres à l’échelle du locataire qui peuvent être appliqués à un locataire managé ou non managé. Voici un exemple où :

* Vous administrez un locataire avec un domaine vérifié, par exemple contoso.com
* Vous utilisez la collaboration B2B à partir d’un autre locataire pour inviter un utilisateur qui n’existe pas encore (userdoesnotexist@contoso.com) dans le locataire de base de contoso.com
* AllowEmailVerifiedUsers est activé pour le locataire de base

Si les conditions précédentes sont remplies, un utilisateur membre est créé dans le locataire de base et un utilisateur invité B2B est créé dans le locataire à l’origine de l’invitation.

Pour en savoir plus sur les inscriptions d’essai à Microsoft Flow et PowerApps, consultez les articles suivants :

* [Comment faire pour empêcher les utilisateurs existants de commencer à utiliser Power BI ?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Questions et réponses sur Flow dans l’organisation](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Comment les vérifications parviennent-elles à fonctionner ensemble ?
Ces deux paramètres peuvent être utilisés conjointement pour définir une vérification plus précise de l’inscription libre-service. Par exemple, la commande suivante permettra aux utilisateurs de réaliser une inscription libre-service, mais uniquement si ces utilisateurs possèdent déjà un compte dans Azure AD (en d’autres termes, les utilisateurs qui ont besoin d’un compte vérifié par e-mail ne peuvent pas réaliser d’inscription libre-service) :

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

L‘organigramme suivant décrit les différentes combinaisons de paramètres et les conditions qui en résultent pour le locataire et l’inscription en libre-service.

![Organigramme des contrôles de l’inscription en libre-service](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Les détails de ce paramètre peuvent être récupérés à l’aide de la cmdlet PowerShell Get-MsolCompanyInformation suivante. Pour plus d’informations à ce sujet, consultez [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation).

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

Pour en savoir plus et obtenir des exemples d'utilisation de ces paramètres, consultez [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter un nom de domaine personnalisé à Azure AD](../fundamentals/add-custom-domain.md)
* [Installation et configuration d’Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Guide de référence des applets de commande Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [Fermer votre compte professionnel ou scolaire dans un locataire Azure non managé](users-close-account.md)
