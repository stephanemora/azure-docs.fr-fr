---
title: Quitter une organisation en tant qu’utilisateur invité - Azure Active Directory | Microsoft Docs
description: Montre comment un utilisateur invité Azure AD B2B peut quitter une organisation en utilisant le panneau d’accès.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077704"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Quitter une organisation en tant qu’utilisateur invité

Un utilisateur invité Azure Active Directory (Azure AD) B2B peut décider de quitter une organisation à tout moment s’il n’a plus besoin d’utiliser les applications de cette organisation ou d’y être associé. Un utilisateur peut quitter une organisation de son propre chef, sans avoir à contacter un administrateur.

## <a name="leave-an-organization"></a>Quitter une organisation

Pour quitter une organisation, en tant qu’utilisateur connecté au [panneau d’accès](https://myapps.microsoft.com), effectuez les étapes suivantes :

1. Si vous n’êtes pas déjà connecté à l’organisation que vous voulez quitter, sélectionnez votre nom dans le coin supérieur droit, puis cliquez sur l’organisation concernée.
2. En haut à droite, sélectionnez votre nom.
3. En regard d’**Organisations**, sélectionnez l’icône des paramètres (engrenage).
 
   ![Capture d’écran illustrant les paramètres utilisateur dans le panneau d’accès](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. Sous **Organisations**, recherchez l’organisation que vous voulez quitter, puis sélectionnez **Quitter l’organisation**.

   ![Capture d’écran montrant l’option Quitter l’organisation dans l’interface utilisateur](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. Lorsque vous êtes invité à confirmer votre choix, sélectionnez **Quitter**. 

## <a name="account-removal"></a>Suppression du compte

Quand un utilisateur quitte une organisation, son compte est « provisoirement supprimé » de l’annuaire. Par défaut, l’objet utilisateur est placé dans la zone **Utilisateurs supprimés** d’Azure AD, mais il n’est pas définitivement supprimé pendant 30 jours. Cette suppression réversible permet à l’administrateur de restaurer le compte d’utilisateur (y compris des groupes et des autorisations), si l’utilisateur effectue une demande de restauration du compte pendant la période de 30 jours.

Si vous le souhaitez, un administrateur de locataires peut supprimer définitivement le compte à tout moment pendant la période de 30 jours. Pour ce faire :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. Sous **Gérer**, sélectionnez **Utilisateurs**.
3. Sélectionnez **Utilisateurs supprimés**.
4. Cochez la case située en regard d’un utilisateur supprimé, puis sélectionnez **Supprimer définitivement**.

Si vous supprimez définitivement un utilisateur, cette action est irréversible.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble d’Azure AD B2B , consultez [Qu’est-ce que la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)



