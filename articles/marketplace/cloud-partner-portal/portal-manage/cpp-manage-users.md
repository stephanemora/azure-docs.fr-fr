---
title: Gestion des utilisateurs sur le Portail Cloud Partner | Place de marché Azure
description: Gestion des utilisateurs sur le portail Microsoft Cloud Partner
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
manager: pbutlerm
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: pabutler
ms.openlocfilehash: 21c49ad0d73721dd204a7a3899d11cf10d43521b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942068"
---
# <a name="managing-users-on-the-cloud-partner-portal"></a>Gestion des utilisateurs sur le Portail Cloud Partner

Le [Portail Cloud Partner](https://cloudpartner.azure.com) vous permet d’ajouter des utilisateurs avec accès basé sur les rôles aux offres de machine virtuelle. Cela aide à gérer les accès et les autorisations lorsque plusieurs personnes travaillent sur les offres.

## <a name="add-users-and-assign-roles"></a>Ajouter des utilisateurs et attribuer des rôles 

Pour ajouter des utilisateurs et attribuer des rôles pour la publication de l’offre, procédez comme suit :

1. Cliquez sur l’onglet **Utilisateurs** dans le volet de navigation de gauche.

    ![Onglet Utilisateurs](./media/userstab.png)


2. Cliquez sur **Add User**.

    ![Sélectionnez Ajouter un utilisateur](./media/adduser.png)


3. Tapez l’adresse e-mail de l’utilisateur et sélectionnez une attribution de rôle.  Vous pouvez ajouter le nouvel utilisateur en tant que « propriétaire » ou « collaborateur ».

    **E-mail** : ajoutez les adresses e-mail des collègues qui contribueront à la publication de l'offre. Les comptes Microsoft (Outlook, Hotmail et Live) et les ID d’organisation sont pris en charge.

    - Ajoutez un « groupe de sécurité/d’alias d’e-mail d’équipe/de groupe » pour éviter que l’individu ne quitte l’organisation.
    - Veillez à ce que les ID d’e-mail fournis sur le Portail Cloud Partner soient surveillés pour détecter les communications émanant de Microsoft.
    
    ![Affecter le rôle](./media/assignrole.png)

    **Rôle** : reportez-vous au tableau ci-dessous pour identifier le type de rôle utilisateur approprié.

    ![Niveau des rôles](./media/roleaccesslevel.png)

    Seuls les propriétaires peuvent accéder aux onglets Azure Payouts et Azure Customer dans [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md).


4. Sélectionnez le nom de l’éditeur pour lequel vous souhaitez ajouter le nouvel utilisateur, puis cliquez sur Ajouter pour terminer l’ajout de l’utilisateur.

    > [!NOTE]
    > Les « noms des éditeurs » pour lesquels votre adresse e-mail est ajoutée en tant que propriétaire ou contributeur apparaîtront dans la liste déroulante des **éditeurs** à sélectionner.


    ![Attribuer un rôle : sélectionner un éditeur](./media/assignselectpublisher.png)


## <a name="delete-an-existing-user"></a>Supprimer un utilisateur existant

Pour supprimer un utilisateur existant, recherchez son e-mail dans l’onglet **Utilisateurs**, puis cliquez sur le bouton **Supprimer** à droite.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également utiliser le portail pour [gérer les profils d’éditeur](./cpp-manage-publisher-profile.md).
