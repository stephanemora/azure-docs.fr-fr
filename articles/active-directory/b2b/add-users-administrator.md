---
title: Ajouter des utilisateurs B2B Collaboration dans le Portail Azure - Azure Active Directory | Microsoft Docs
description: Montre comment un administrateur peut ajouter des utilisateurs invités à son répertoire à partir d’une organisation partenaire en utilisant Azure Active Directory (Azure AD) B2B Collaboration.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7dddc41d35ae466f7c1392450fbda3c86a72a538
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267113"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure

En tant qu’administrateur général ou en tant qu’utilisateur affecté à l’un des rôles d’annuaire administrateur limité, vous pouvez utiliser le Portail Azure pour inviter des utilisateurs B2B Collaboration. Libre à vous de convier des utilisateurs invités à rejoindre le répertoire, un groupe ou une application. Une fois l’utilisateur invité par le biais de l’une de ces méthodes, son compte est ajouté à Azure Active Directory (Azure AD) et son type devient *invité*. L’utilisateur invité doit ensuite échanger son invitation pour accéder aux ressources.

Après avoir ajouté un utilisateur invité dans l’annuaire, vous pouvez envoyer à l’utilisateur invité un lien direct vers une application partagée, ou l’utilisateur invité peut cliquer sur l’URL d’échange dans l’e-mail d’invitation. Pour plus d’informations sur le processus d’utilisation, consultez [Utilisation d’une invitation B2B Collaboration](redemption-experience.md).

> [!IMPORTANT]
> Vous devez suivre les étapes de [Guide d’ajout des informations de confidentialité de votre organisation dans Azure Active Directory](https://aka.ms/adprivacystatement) pour ajouter l’URL de déclaration de confidentialité de votre organisation. Dans le cadre du processus d’échange lors de la première invitation, un utilisateur invité doit accepter les conditions de confidentialité pour continuer. 

## <a name="add-guest-users-to-the-directory"></a>Ajouter des utilisateurs invités au répertoire

Pour ajouter des utilisateurs B2B Collaboration au répertoire, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.
4. Sélectionnez **Nouvel utilisateur invité**.

   ![Indique où le nouvel utilisateur invité se trouve dans l’interface utilisateur](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
7. Sous **Convier un invité**, entrez l’adresse e-mail de l’utilisateur externe. Vous pouvez éventuellement inclure un message d’accueil. Par exemple : 

   ![Indique où le nouvel utilisateur invité se trouve dans l’interface utilisateur](./media/add-users-administrator/InviteGuest.png) 

8. Sélectionnez **Inviter** pour envoyer automatiquement l’invitation à l’utilisateur invité. Dans la zone **Notification**, recherchez un message indiquant **Utilisateur invité avec succès**. 
 
Après avoir envoyé l’invitation, le compte d’utilisateur est automatiquement ajouté au répertoire en tant qu’invité.


![Indique un utilisateur B2B dont le type est invité](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Ajouter des utilisateurs invités à un groupe
En tant qu’administrateur Azure AD, vous pouvez ajouter manuellement des utilisateurs B2B Collaboration à un groupe. Pour cela, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs et groupes** > **Tous les groupes**.
4. Sélectionnez un groupe (ou cliquez sur **Nouveau groupe** pour en créer un nouveau). Il est judicieux d’inclure dans la description que le groupe contient des utilisateurs invités B2B.
5. Sélectionnez **Membres** > **Ajouter des membres**. 
6. Effectuez l’une des actions suivantes :
   - Si l’utilisateur invité existe déjà dans le répertoire, recherchez l’utilisateur B2B. Sélectionnez l’utilisateur, puis cliquez sur **Sélectionner** pour l’ajouter au groupe.
   - Si l’utilisateur invité n’existe pas encore dans le répertoire, sélectionnez **Inviter**.
   ![Bouton Ajouter un invité servant à ajouter des membres invités](./media/add-users-administrator/GroupInvite.png)
   
      Sous **Convier un invité**, entrez l’adresse e-mail et un message personnel facultatif, puis sélectionnez **Inviter**. Cliquez sur **Sélectionner** pour ajouter l’utilisateur au groupe.

      L’invitation est automatiquement envoyée à l’utilisateur invité. Dans la zone **Notification**, recherchez un message indiquant **Utilisateur invité**. 

Vous pouvez également utiliser des groupes dynamiques avec Azure AD B2B Collaboration. Pour plus d’informations sur les groupes dynamiques, consultez [Groupes dynamiques et Azure Active Directory B2B Collaboration](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Ajouter des utilisateurs invités à une application

En tant qu’administrateur Azure AD, vous pouvez ajouter des utilisateurs B2B Collaboration à une application. Pour cela, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
4. Sélectionnez l’application à laquelle vous souhaitez ajouter des utilisateurs invités.
5. Sous **Gérer**, sélectionnez **Utilisateurs et groupes**.
6. Sélectionnez **Ajouter un utilisateur**.
7. Sous **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
8. Effectuez l’une des actions suivantes :
   - Si l’utilisateur invité existe déjà dans le répertoire, recherchez l’utilisateur B2B. Sélectionnez l’utilisateur, puis cliquez sur **Sélectionner** pour l’ajouter à l’application.
   - Si l’utilisateur invité n’existe pas encore dans le répertoire, sélectionnez **Inviter**.
   ![Bouton Ajouter un invité servant à ajouter des membres invités](./media/add-users-administrator/AppInviteUsers.png)
   
      Sous **Convier un invité**, entrez l’adresse e-mail et un message personnel facultatif, puis sélectionnez **Inviter**. Cliquez sur **Sélectionner** pour ajouter l’utilisateur à l’application.

      L’invitation est automatiquement envoyée à l’utilisateur invité. Dans la zone **Notification**, recherchez un message indiquant **Utilisateur invité**.

9. Sous **Ajouter une attribution**, cliquez sur **Sélectionner un rôle**, sélectionnez un rôle à appliquer à l’utilisateur sélectionné (le cas échéant), puis cliquez sur **OK**.
10. Cliquez sur **Affecter**.
 
## <a name="resend-invitations-to-guest-users"></a>Renvoyer des invitations aux utilisateurs invités

Si un utilisateur invité n’a pas encore répondu à son invitation, vous pouvez lui renvoyer l’e-mail d’invitation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
3. Sous **Gérer**, sélectionnez **Utilisateurs et groupes**.
4. Sélectionnez **Tous les utilisateurs**.
5. Sélectionnez le compte d’utilisateur.
6. Sous **Gérer**, sélectionnez **Profil**.
7. Si l’utilisateur n’a pas encore accepté l’invitation, une option **Renvoyer l’invitation** est disponible. Cliquez sur ce bouton pour la lui renvoyer.

   ![Option Renvoyer l’invitation dans le profil utilisateur](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Si vous renvoyez une invitation qui au départ, orientait l’utilisateur vers une application spécifique, sachez que le lien contenu dans la nouvelle invitation va au contraire l’orienter vers le volet d’accès de niveau supérieur.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment des administrateurs non-Azure AD peuvent ajouter des utilisateurs invités B2B, consultez [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](add-users-information-worker.md)
- Pour plus d’informations sur l’e-mail d’invitation, consultez les [Éléments de l’e-mail d’invitation de B2B Collaboration](invitation-email-elements.md).

