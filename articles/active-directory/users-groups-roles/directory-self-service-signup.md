---
title: Inscription en libre-service pour les comptes d’utilisateurs vérifiés par e-mail - Azure Active Directory | Microsoft Docs
description: Utilisez l’inscription en libre-service dans un locataire Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d949b746f05eb440f5ae28f683dfc838217ab47
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956511"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>What ' s libre-service d’abonnement pour Azure Active Directory ?

Cet article explique comment utiliser l’inscription libre-service pour remplir une organisation dans Azure Active Directory (Azure AD). Si vous souhaitez reprendre un nom de domaine à partir d’un Azure non géré organisation AD, consultez [reprendre un répertoire non géré en tant qu’administrateur](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Pourquoi utiliser inscription libre-service ?
* Permettre aux clients de bénéficier plus rapidement des services dont ils ont besoin.
* Créer des offres envoyées par e-mail pour un service.
* Créer des flux d’abonnement par courrier électronique qui rapidement permettant aux utilisateurs de créer des identités à l’aide de leurs alias de messagerie professionnelle, faciles à mémoriser
* Un répertoire Azure AD créé en libre-service peut être transformé en répertoire géré utilisable pour d’autres services.

## <a name="terms-and-definitions"></a>Termes et définitions
* **Inscription libre-service**: méthode selon laquelle un utilisateur s‘abonne à un service cloud et bénéficie automatiquement d’une identité créée pour lui dans Azure AD en fonction de son domaine de messagerie.
* **Annuaire Azure AD non managé** : annuaire dans lequel cette identité est créée. Un répertoire non géré est un répertoire qui ne possède aucun administrateur général.
* **Utilisateur vérifié par e-mail** : type de compte d’utilisateur dans Azure AD. Un utilisateur qui possède une identité créée automatiquement après s’être abonné à une offre libre-service est considéré comme un utilisateur vérifié par e-mail. Un utilisateur vérifié par e-mail est un membre ordinaire d'un répertoire marqué par la valeur creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Comment vérifier les paramètres libre-service ?
Les administrateurs peuvent désormais effectuer deux vérifications libre-service. Ils peuvent contrôler si :

* Les utilisateurs peuvent rejoindre l’annuaire par e-mail
* Les utilisateurs peuvent s’abonner eux-mêmes aux applications et services

### <a name="how-can-i-control-these-capabilities"></a>Comment puis-je vérifier ces fonctionnalités ?
Un administrateur peut configurer ces fonctionnalités à l’aide des paramètres Set-MsolCompanySettings d’applet de commande Azure AD suivants :

* **AllowEmailVerifiedUsers** vérifie si un utilisateur peut créer ou rejoindre un annuaire. Si vous définissez ce paramètre sur $false, aucun utilisateur vérifié par e-mail ne peut rejoindre l’annuaire.
* **AllowAdHocSubscriptions** contrôle la capacité des utilisateurs à effectuer l’inscription libre-service. Si vous définissez ce paramètre sur $false, aucun utilisateur ne peut effectuer d’inscription libre-service.
  
AllowEmailVerifiedUsers et AllowAdHocSubscriptions sont des paramètres de niveau de l’annuaire qui peuvent être appliqués à un répertoire géré ou non géré. Voici un exemple où :

* Vous administrez un annuaire avec un domaine vérifié, par exemple, contoso.com
* Collaboration B2B à partir d’un autre répertoire vous permet d’inviter un utilisateur qui n’existe pas déjà (userdoesnotexist@contoso.com) dans le répertoire de base de contoso.com
* AllowEmailVerifiedUsers est activé pour l’annuaire local

Si les conditions précédentes sont remplies, un utilisateur membre est créé dans l’annuaire local, tandis qu’un utilisateur invité B2B est créé dans l’annuaire à l’origine de l’invitation.

Flow et PowerApps version d’évaluation de l’opération n’est pas contrôlées par le **AllowAdHocSubscriptions** paramètre. Pour plus d’informations, consultez les articles suivants :

* [Comment faire pour empêcher les utilisateurs existants de commencer à utiliser Power BI ?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Questions et réponses sur Flow dans l’organisation](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Comment les vérifications parviennent-elles à fonctionner ensemble ?
Ces deux paramètres peuvent être utilisés conjointement pour définir un contrôle plus précis sur Inscription libre-service. Par exemple, la commande suivante permettra aux utilisateurs d’effectuer le libre-service d’abonnement, mais uniquement si ces utilisateurs possèdent déjà un compte dans Azure AD (en d’autres termes, les utilisateurs qui ont besoin de créer un compte vérifié par e-mail tout d’abord ne peut pas effectuer inscription libre-service) :

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

L’organigramme suivant décrit les différentes combinaisons de ces paramètres et les conditions qui en résultent pour le répertoire et l’inscription libre-service.

![Organigramme de contrôles de l’inscription libre-service](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Pour en savoir plus et obtenir des exemples d'utilisation de ces paramètres, consultez [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter un nom de domaine personnalisé à Azure AD](../fundamentals/add-custom-domain.md)
* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Guide de référence des applets de commande Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Fermer votre compte professionnel ou scolaire dans un répertoire non géré](users-close-account.md)
