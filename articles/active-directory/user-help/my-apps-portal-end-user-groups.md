---
title: Mettre à jour vos informations de groupes à partir du portail mes applications - Azure Active Directory | Microsoft Docs
description: Découvrez comment afficher et mettre à jour vos informations liés en groupes, notamment l’affichage des groupes que vous possédez, création de nouveaux groupes, affichage des groupes auxquels vous êtes déjà un membre et rejoindre les groupes dont vous n’êtes pas déjà partie de.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5af86ad12eed4901e577149b9f20216d5004663b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340460"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Mettre à jour vos informations de groupes à partir du portail mes applications
Vous pouvez utiliser votre compte professionnel ou scolaire avec basée sur le web **mes applications** portal, pour afficher et démarrer la plupart des applications de cloud de votre organisation, à mettre à jour certaines de vos informations de compte et un profil pour voir votre **groupes** plus d’informations et d’effectuer **révisions d’accès** pour vos applications et les groupes. Si vous n’avez pas accès à la **mes applications** portail, vous devez contacter votre support technique pour obtenir l’autorisation.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ce contenu est destiné aux utilisateurs. Si vous êtes un administrateur, vous trouverez plus d’informations sur comment configurer et gérer vos applications cloud dans le [Documentation gestion des Application](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Afficher vos informations de groupes
Si votre administrateur vous a donné l’autorisation d’afficher le **groupes** vignette, vous pouvez :

- **En tant qu’un membre du groupe.** Afficher les détails ou laisser un groupe.

- **En tant que propriétaire d’un groupe.** Afficher les détails, créer un nouveau groupe, ajouter ou supprimer des membres ou supprimer votre groupe.

### <a name="to-view-your-groups-information"></a>Afficher vos informations de groupes

1.  Connectez-vous à votre compte professionnel ou scolaire.

2.  Ouvrez votre navigateur web et accédez à https://myapps.microsoft.com, ou utilisez le lien fourni par votre organisation. Par exemple, vous pourrez être dirigé vers une page personnalisée pour votre organisation, tel que https://myapps.microsoft.com/contoso.com.

    Le **applications** page apparaît, affichant toutes les applications cloud détenus par votre organisation et que vous pouvez utiliser.

    ![Page applications dans le portail mes applications](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Sélectionnez le **groupes** vignette pour voir vos informations de groupe.

    ![Page groupes avec à la fois détenus et les groupes de membres](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Selon vos autorisations, vous pouvez utiliser la **groupes** page à :

    - **Passez en revue les groupes que vous possédez.** Vue informations sur les groupes dont vous êtes propriétaire au sein de votre organisation à partir de la **groupes dont je suis propriétaire** zone. Sélection d’un nom de groupe spécifique vous offre plus de détails sur le groupe, y compris le type de groupe, le nombre de membres, la stratégie de jointure et la liste de membres actifs.

    - **Créer un nouveau groupe.** Créer un nouveau groupe avec vous en tant que le propriétaire de la **groupes dont je suis propriétaire** zone. Pour obtenir des instructions spécifiques, consultez [créer un nouveau groupe](#create-a-new-group) section de cet article.

    - **Modifier un groupe existant.** Modifier les détails d’un de vos propres groupes. Pour obtenir des instructions spécifiques, consultez [modifier un groupe existant](#edit-an-existing-group) section de cet article.

    - **Ajouter ou supprimer des membres.** Ajouter ou supprimer des membres pour les groupes que vous possédez. Pour obtenir des instructions spécifiques, consultez [ajouter ou supprimer un membre](#add-or-remove-a-member) section de cet article.

    - **Supprimer un groupe.** Supprimer tous les groupes que vous possédez. Pour obtenir des instructions spécifiques, consultez [supprimer un groupe](#delete-a-group) section de cet article.

    - **Passez en revue les groupes que vous êtes un membre de.** Afficher les noms de tous les groupes auxquels vous êtes un membre de la **groupes auxquels j’appartiens** zone. Sélection d’un nom de groupe spécifique vous offre plus de détails sur le groupe, y compris le type de groupe, le nombre de membres, la stratégie de jointure et la liste de membres actifs.

    - **Rejoindre un groupe.** Joindre un groupe existant pour lequel vous n’êtes pas déjà un membre, à partir de la **groupes auxquels j’appartiens** zone. Pour obtenir des instructions spécifiques, consultez [joindre un groupe existant](#join-an-existing-group).

## <a name="create-a-new-group"></a>Créer un groupe
1. Sur le **groupes** page, sélectionnez **créer un groupe** à partir de la **groupes dont je suis propriétaire** zone.

    Le **créer un groupe** boîte s’affiche.

    ![Créer la zone de groupe](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Entrez les informations requises :

    - **Type du groupe :**
        
        - **Sécurité.** Utilisé pour gérer l’accès de membres et d’ordinateurs aux ressources partagées d’un groupe d’utilisateurs. Par exemple, vous pouvez créer un groupe de sécurité pour une stratégie de sécurité particulière. En procédant ainsi, vous pouvez donner un ensemble d’autorisations à tous les membres à la fois, au lieu de devoir ajouter des autorisations individuellement à chaque membre.

        - **Office 365.** Fournit des opportunités de collaboration en donnant aux membres l’accès à des éléments partagés : une boîte aux lettres, un calendrier, des fichiers, un site SharePoint et bien plus encore. Cette option vous permet également de donner à des personnes extérieures à votre organisation un accès au groupe.

    - **Nom du groupe**. Ajoutez le nom du groupe, quelque chose dont vous vous souviendrez et qui est évocateur.

    - **Description du groupe (facultative).** Ajoutez une description facultative à votre groupe.

    - **Stratégie de groupe.** Choisir d’autoriser toutes les personnes à rejoindre le groupe ou d’autoriser uniquement le propriétaire du groupe ajouter des membres.

3. Sélectionnez **Créer**.

    Le nouveau groupe est créé avec vous en tant que le propriétaire et il apparaît dans votre **groupes dont je suis propriétaire** liste. Étant donné que vous êtes le propriétaire, ce groupe s’affiche également dans le **groupes auxquels j’appartiens** liste.

## <a name="edit-an-existing-group"></a>Modifier un groupe existant
Après avoir créé un groupe, vous pouvez modifier ses détails, y compris la mise à jour les informations existantes.

### <a name="to-edit-your-details"></a>Pour modifier les détails de votre
1. Sélectionnez le groupe que vous souhaitez modifier dans le **groupes** page, puis sélectionnez **modifier les détails** sur le *&lt;nom_groupe&gt;* page.

    Le **modifier les détails** case s’affiche et vous pouvez mettre à jour les informations que vous avez ajouté lorsque vous avez créé initialement le groupe.

2. Effectuer toutes vos modifications, puis sélectionnez **mise à jour**.

## <a name="add-or-remove-a-member"></a>Ajouter ou supprimer un membre
Vous pouvez ajouter ou supprimer des membres pour une de vos propres groupes.

### <a name="to-add-or-remove-a-member"></a>Pour ajouter ou supprimer un membre
1. Sélectionnez le groupe que vous souhaitez ajouter des membres, puis sélectionnez **+** sur le *&lt;nom_groupe&gt;* page.

    ![Ajouter un membre du groupe, avec + mise en surbrillance de la connexion](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Recherchez le membre que vous souhaitez ajouter, à partir de la **ajouter des membres** zone, puis sélectionnez **ajouter**.

    ![Ajouter la zone membres, avec un nouveau membre à ajouter](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Une invitation est envoyée au nouveau membre, à la prise en main de l’accès aux applications de l’organisation.

3. Si vous avez ajouté un membre par erreur, ou si un membre a quitté votre organisation, vous pouvez supprimer le membre en sélectionnant **supprimer un membre** en regard du nom du membre sur le *&lt;nom_groupe&gt;* page.

    ![Supprimer un membre, avec le lien de suppression mis en surbrillance](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="delete-a-group"></a>Supprimer un groupe
Vous pouvez supprimer un de vos propres groupes à tout moment. Toutefois, si vous supprimez un groupe par erreur vous devrez le créer et ajouter des membres à nouveau.

### <a name="to-delete-the-group"></a>Pour supprimer le groupe
1. Sélectionnez le groupe que vous souhaitez supprimer définitivement, puis sélectionnez **supprimer le groupe** sur le *&lt;nom_groupe&gt;* page.

    ![page de < nom_groupe > avec le lien de groupe Supprimer mis en surbrillance](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Sélectionnez **Oui** sur le message de confirmation.

    Le groupe est supprimé définitivement. 
    
## <a name="join-an-existing-group"></a>Joindre un groupe existant
Vous pouvez joindre un groupe déjà existant à partir de la **groupes** page.

### <a name="to-join-or-leave-a-group"></a>Pour rejoindre ou quitter un groupe

1. Sur le **groupes** page, sélectionnez **rejoindre le groupe** à partir de la **groupes auxquels j’appartiens** zone.

    Le **rejoindre groupes** page s’affiche.

    ![Joindre la page groupes, avec le bouton de groupe de jointure mis en surbrillance](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Sur le **rejoindre groupes** page, sélectionnez le nom du groupe que vous souhaitez rejoindre, afficher les détails du groupe associé et, si le groupe est disponible, sélectionnez **rejoindre le groupe**.

    Si le groupe nécessite le propriétaire du groupe à approuver l’appartenance, vous êtes invité à entrer une justification pour pourquoi vous devez joindre le groupe, puis sélectionnez **demande**. Si le groupe ne nécessite pas d’approbation, vous êtes immédiatement ajouté en tant que membre et le groupe apparaît dans votre **groupes auxquels j’appartiens** liste.

3. Si vous avez rejoint un groupe par erreur ou si vous n’avez plus besoin de faire partie de celui-ci, vous pouvez sélectionner le nom du groupe à partir de la **rejoindre groupes** page, puis sélectionnez **quitter le groupe**.

    ![Joindre la page groupes, avec le bouton de groupe laissez le champ mis en surbrillance](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Étapes suivantes

- [Accéder à et utiliser des applications sur le portail mes applications](my-apps-portal-end-user-access.md).

- [Modifier vos informations de profil](my-apps-portal-end-user-update-profile.md).

- [Effectuer votre propre révisions d’accès](my-apps-portal-end-user-access-reviews.md).