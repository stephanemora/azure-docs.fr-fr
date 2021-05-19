---
title: Quitter une organisation en tant qu’utilisateur invité – Azure Active Directory
description: Montre comment un utilisateur invité Azure AD B2B peut quitter une organisation en utilisant le panneau d’accès.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/05/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc07e038ef382a44c4672b2d63c49d8f5a961cda
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109486312"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Quitter une organisation en tant qu’utilisateur invité

Un utilisateur invité Azure Active Directory (Azure AD) B2B peut décider de quitter une organisation à tout moment s’il n’a plus besoin d’utiliser les applications de cette organisation ou d’y être associé. Un utilisateur peut quitter une organisation de son propre chef, sans avoir à contacter un administrateur.

> [!NOTE]
> Un utilisateur invité ne peut pas quitter une organisation si son compte est désactivé dans le locataire de base ou le locataire de la ressource. Si son compte est désactivé, l’utilisateur invité doit contacter l’administrateur de locataire, qui peut supprimer le compte invité ou l’activer afin que l’utilisateur puisse quitter l’organisation.

## <a name="leave-an-organization"></a>Quitter une organisation

Pour quitter une organisation, procédez comme suit.

1. Accédez à la page Profil Panneau d’accès en effectuant l’une des étapes suivantes :
   
   - Dans le [portail Azure](https://portal.azure.com), cliquez sur votre nom dans le coin supérieur droit et sélectionnez **Afficher le compte**.
   - Ouvrez votre [Panneau d’accès](https://myapps.microsoft.com), cliquez sur votre nom dans le coin supérieur droit, puis sur **Organisations** et sélectionnez **Voir le compte**.
 
 
2. Sélectionnez **Gérer les organisations**.
  ![Capture d’écran des paramètres utilisateur dans le Panneau d’accès](media/leave-the-organization/manage-organizations.png) 
  
3. Sous **Organisations**, recherchez l’organisation que vous voulez quitter, puis sélectionnez **Quitter l’organisation**.

   ![Capture d’écran montrant l’option Quitter l’organisation dans l’interface utilisateur](media/leave-the-organization/leave-org.png)
4. Lorsque vous êtes invité à confirmer votre choix, sélectionnez **Quitter**.
> [!NOTE]
   > Vous ne pouvez pas quitter votre organisation d’origine.

## <a name="account-removal"></a>Suppression du compte

Quand un utilisateur quitte une organisation, son compte est « provisoirement supprimé » de l’annuaire. Par défaut, l’objet utilisateur est placé dans la zone **Utilisateurs supprimés** d’Azure AD, mais il n’est pas définitivement supprimé pendant 30 jours. Cette suppression réversible permet à l’administrateur de restaurer le compte d’utilisateur (y compris des groupes et des autorisations), si l’utilisateur effectue une demande de restauration du compte pendant la période de 30 jours.

Si vous le souhaitez, un administrateur de locataires peut supprimer définitivement le compte à tout moment pendant la période de 30 jours. Pour ce faire :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. Sous **Gérer**, sélectionnez **Utilisateurs**.
3. Sélectionnez **Utilisateurs supprimés**.
4. Cochez la case située en regard d’un utilisateur supprimé, puis sélectionnez **Supprimer définitivement**.

Si vous supprimez définitivement un utilisateur, cette action est irréversible.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble d’Azure AD B2B , consultez [Qu’est-ce que la collaboration Azure AD B2B ?](what-is-b2b.md)



