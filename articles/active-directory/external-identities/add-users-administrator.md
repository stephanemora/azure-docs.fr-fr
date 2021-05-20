---
title: Ajouter des utilisateurs de la collaboration B2B dans le Portail Azure - Azure AD
description: Montre comment un administrateur peut ajouter des utilisateurs invités à son répertoire à partir d’une organisation partenaire en utilisant Azure Active Directory (Azure AD) B2B Collaboration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/23/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e538be52f21d169aefd350aa2781de0065f04f
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109632889"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure

En tant qu’utilisateur affecté à l’un des rôles d’annuaire administrateur limité, vous pouvez utiliser le Portail Azure pour inviter des utilisateurs B2B Collaboration. Libre à vous de convier des utilisateurs invités à rejoindre le répertoire, un groupe ou une application. Une fois l’utilisateur invité par le biais de l’une de ces méthodes, son compte est ajouté à Azure Active Directory (Azure AD) et son type devient *invité*. L’utilisateur invité doit ensuite échanger son invitation pour accéder aux ressources. L’invitation d’un utilisateur n’expire pas.

Après avoir ajouté un utilisateur invité dans le répertoire, vous pouvez envoyer à l’utilisateur invité un lien direct vers une application partagée, ou l’utilisateur invité peut sélectionner l’URL d’acceptation dans l’e-mail d’invitation. Pour plus d’informations sur le processus d’utilisation, consultez [Utilisation d’une invitation B2B Collaboration](redemption-experience.md).

> [!IMPORTANT]
> Vous devez suivre les étapes de [Guide : ajout des informations de confidentialité de votre organisation dans Azure Active Directory](../fundamentals/active-directory-properties-area.md) pour ajouter l’URL de déclaration de confidentialité de votre organisation. Dans le cadre du processus d’échange lors de la première invitation, un utilisateur invité doit accepter les conditions de confidentialité pour continuer. 

## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous que les paramètres de collaboration externe de votre organisation sont configurés de telle sorte que vous êtes autorisé à inviter des invités. Par défaut, tous les utilisateurs et les administrateurs peuvent inviter des invités. Mais les stratégies de collaboration externe de votre organisation peuvent être configurées pour empêcher certains types d’utilisateurs ou d’administrateurs d’inviter des invités. Pour savoir comment afficher et définir ces stratégies, consultez [Activer une collaboration B2B externe et gérer ceux pouvant inviter des invités](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Ajouter des utilisateurs invités au répertoire

Pour ajouter des utilisateurs B2B Collaboration au répertoire, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’utilisateur affecté à un rôle d’annuaire administrateur limité ou à un rôle Inviteur d’invités.
2. Recherchez et sélectionnez **Azure Active Directory** à partir de n’importe quelle page.
3. Sous **Gérer**, sélectionnez **Utilisateurs**.
4. Sélectionnez **Nouvel utilisateur invité**.
5. Dans la page **Nouvel utilisateur**, sélectionnez **Inviter un utilisateur**, puis ajoutez les informations de l’utilisateur invité.

   ![Image d’un type d’utilisateur invité](media/add-users-administrator/invite-user.png)

      - **Nom.** Prénom et nom de l’utilisateur invité.
   - **Adresse e-mail (obligatoire)** . Adresse e-mail de l’utilisateur invité.
   - **Message personnel (facultatif)**  : inclure un message d’accueil personnalisé pour l’utilisateur invité.
   - **Groupes** : permet d’ajouter l’utilisateur invité à un ou plusieurs groupes.
   -  **Rôles**: Si vous devez attribuer des autorisations administratives Azure AD à l’utilisateur, vous pouvez les ajouter à un rôle Azure AD en sélectionnant **Utilisateur** à côté de **Rôles**.  

   > [!NOTE]
   > Les adresses e-mail de groupe ne sont pas prises en charge. Veuillez entrer des adresses e-mail individuelles. Certains fournisseurs de messagerie permettent aux utilisateurs d’ajouter un signe plus (+) et du texte à leurs adresses e-mail pour faciliter notamment le filtrage de la boîte de réception. Toutefois, Azure AD ne prend pas en charge les signes plus dans les adresses e-mail pour l’instant. Pour éviter les problèmes de livraison, omettez le signe plus (+) et les caractères après celui-ci jusqu’au symbole @.
6. Sélectionnez **Inviter** pour envoyer automatiquement l’invitation à l’utilisateur invité. 
 
Après avoir envoyé l’invitation, le compte d’utilisateur est automatiquement ajouté au répertoire en tant qu’invité.

 ![Image d’un utilisateur invité](media/add-users-administrator//guest-user-type.png)

## <a name="add-guest-users-to-a-group"></a>Ajouter des utilisateurs invités à un groupe
Vous pouvez ajouter manuellement des utilisateurs B2B Collaboration à un groupe. Pour cela, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Recherchez et sélectionnez **Azure Active Directory** à partir de n’importe quelle page.
3. Sous **Gérer**, sélectionnez **Groupes**.
4. Sélectionnez un groupe (ou sélectionnez **Nouveau groupe** pour en créer un nouveau). Il est judicieux d’inclure dans la description que le groupe contient des utilisateurs invités B2B.
5. Sélectionnez le lien sous **Membres**.
6. Effectuez l’une des actions suivantes :

     - Si l’utilisateur invité existe déjà dans le répertoire, recherchez l’utilisateur B2B. Sélectionnez l’utilisateur, puis cliquez sur **Sélectionner** pour l’ajouter au groupe.
     - Si l’utilisateur invité ne se trouve pas déjà dans le répertoire, invitez-le à rejoindre le groupe en saisissant son adresse e-mail dans la zone de recherche et un message personnel facultatif, puis en choisissant **Inviter**. L’invitation est automatiquement envoyée à l’utilisateur invité.
   
![Bouton Ajouter un invité pour ajouter des membres invités](./media/add-users-administrator/group-invite.png)
 
Vous pouvez également utiliser des groupes dynamiques avec Azure AD B2B Collaboration. Pour plus d’informations sur les groupes dynamiques, consultez [Groupes dynamiques et Azure Active Directory B2B Collaboration](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Ajouter des utilisateurs invités à une application

Pour ajouter des utilisateurs B2B Collaboration à une application, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Recherchez et sélectionnez **Azure Active Directory** à partir de n’importe quelle page.
3. Sous **Gérer**, sélectionnez **Applications d’entreprise**.
4. Dans la page **Toutes les applications**, sélectionnez l’application à laquelle vous souhaitez ajouter des utilisateurs invités.
5. Sous **Gérer**, sélectionnez **Utilisateurs et groupes**.
6. Sélectionnez **Ajouter un utilisateur**.
7. Sous **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.
8. Effectuez l’une des actions suivantes :
   - Si l’utilisateur invité existe déjà dans le répertoire, recherchez l’utilisateur B2B. Sélectionnez l’utilisateur, choisissez **Sélectionner**, puis sélectionnez **Attribuer** pour l’ajouter à l’application.
   - Si l’utilisateur invité n’existe pas dans l’annuaire, sous **Sélectionner un membre ou inviter un utilisateur externe**, tapez l’adresse e-mail de l’utilisateur. Dans la boîte de message, tapez un message personnel facultatif. Sous la zone de message, sélectionnez **Inviter**.
               ![Capture d’écran mettant en évidence l’endroit où ajouter l’adresse e-mail de l’utilisateur, le message personnalisé et mettant également en évidence le bouton Inviter.](./media/add-users-administrator/app-invite-users.png)

10.  L’utilisateur invité s’affiche dans la liste **Utilisateurs et groupes** de l’application, avec le rôle **Accès par défaut**. Si l’application propose différents rôles et que vous souhaitez modifier le rôle de l’utilisateur, procédez comme suit :
   - Cochez la case à côté de l’utilisateur invité, puis sélectionnez le bouton **Modifier**. 
   - Sur la page **Modifier l’attribution**, choisissez le lien sous **Sélectionner un rôle**, puis sélectionnez le rôle que vous souhaitez attribuer à l’utilisateur.
   - Choisissez **Sélectionner**.
   - Sélectionnez **Attribuer**.
 
## <a name="resend-invitations-to-guest-users"></a>Renvoyer des invitations aux utilisateurs invités

Si un utilisateur invité n’a pas encore répondu à son invitation, vous pouvez lui renvoyer l’e-mail d’invitation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Recherchez et sélectionnez **Azure Active Directory** à partir de n’importe quelle page.
3. Sous **Gérer**, sélectionnez **Utilisateurs**.
4. Sélectionnez le compte d’utilisateur.
5. Dans la section **Identité**, sous **Invitation acceptée**, sélectionnez le lien **(gérer)** . 
6. Si l’utilisateur n’a pas encore accepté l’invitation, sélectionnez l’option **Oui** pour la renvoyer.

    ![Renvoyer l’invitation](./media/add-users-administrator/resend-invitation.png)

> [!NOTE]
> Une URL d’invitation est générée.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment des administrateurs non-Azure AD peuvent ajouter des utilisateurs invités B2B, consultez [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](add-users-information-worker.md)
- Pour plus d’informations sur l’e-mail d’invitation, consultez les [Éléments de l’e-mail d’invitation de B2B Collaboration](invitation-email-elements.md).