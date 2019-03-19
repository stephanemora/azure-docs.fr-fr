---
title: Quitter une organisation en tant qu’utilisateur invité - Azure Active Directory | Microsoft Docs
description: Montre comment un utilisateur invité Azure AD B2B peut quitter une organisation en utilisant le panneau d’accès.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75f91c037a2f05c999d388ce7bb16ad2d0c9cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005670"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Quitter une organisation en tant qu’utilisateur invité

Un utilisateur invité Azure Active Directory (Azure AD) B2B peut décider de quitter une organisation à tout moment s’il n’a plus besoin d’utiliser les applications de cette organisation ou d’y être associé. Un utilisateur peut quitter une organisation de son propre chef, sans avoir à contacter un administrateur.

## <a name="leave-an-organization"></a>Quitter une organisation

Pour laisser une organisation, procédez comme suit.

1. Accédez à votre page de profil panneau d’accès en effectuant l’une des opérations suivantes :
   
   - Dans le [Azure portal](https://portal.azure.com), cliquez sur votre nom dans le coin supérieur droit et sélectionnez **afficher compte**.
   - Ouvrez votre [volet d’accès](https://myapps.microsoft.com), cliquez sur votre nom dans le coin supérieur droit, puis sur Suivant pour **organisations**, sélectionnez l’icône Paramètres (engrenage).
 
   ![Capture d’écran des paramètres utilisateur dans le Panneau d’accès](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Si vous n’êtes pas déjà connecté à l’organisation que vous souhaitez laisser sous **organisations**, cliquez sur le **connectez-vous quitter l’organisation** lien en regard du nom de l’organisation. Une fois que vous êtes connecté, cliquez sur votre nom dans le coin supérieur droit et en regard **organisations**, sélectionnez l’icône Paramètres (engrenage).

3. Sous **Organisations**, recherchez l’organisation que vous voulez quitter, puis sélectionnez **Quitter l’organisation**.

   ![Capture d’écran montrant l’option Quitter l’organisation dans l’interface utilisateur](media/leave-the-organization/LeaveOrg.png)

4. Lorsque vous êtes invité à confirmer votre choix, sélectionnez **Quitter**. 

## <a name="account-removal"></a>Suppression du compte

Quand un utilisateur quitte une organisation, son compte est « provisoirement supprimé » de l’annuaire. Par défaut, l’objet utilisateur est placé dans la zone **Utilisateurs supprimés** d’Azure AD, mais il n’est pas définitivement supprimé pendant 30 jours. Cette suppression réversible permet à l’administrateur de restaurer le compte d’utilisateur (y compris des groupes et des autorisations), si l’utilisateur effectue une demande de restauration du compte pendant la période de 30 jours.

Si vous le souhaitez, un administrateur de locataires peut supprimer définitivement le compte à tout moment pendant la période de 30 jours. Pour ce faire :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. Sous **Gérer**, sélectionnez **Utilisateurs**.
3. Sélectionnez **Utilisateurs supprimés**.
4. Cochez la case située en regard d’un utilisateur supprimé, puis sélectionnez **Supprimer définitivement**.

Si vous supprimez définitivement un utilisateur, cette action est irréversible.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble d’Azure AD B2B , consultez [Qu’est-ce que la collaboration Azure AD B2B ?](what-is-b2b.md)



