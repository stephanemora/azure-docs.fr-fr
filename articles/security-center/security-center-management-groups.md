---
title: Gagner en visibilité au niveau locataire dans Azure Security Center | Microsoft Docs
description: Découvrez comment gagner en visibilité au niveau des locataires dans Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: terrylan
ms.openlocfilehash: 800ec83b3599dba716e7a4a015b9b8c1745a0975
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144565"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Gagner en visibilité au niveau locataire dans Azure Security Center
Cet article vous aide à démarrer en effectuant plusieurs actions qui optimisent les avantages qu’offre Azure Security Center. En effet, en effectuant ces actions, vous obtenez une meilleure visibilité de tous les abonnements Azure qui sont liés à votre locataire Azure Active Directory et vous gérez efficacement la sécurité de votre organisation à grande échelle en appliquant des stratégies de sécurité sur plusieurs abonnements en même temps.

## <a name="management-groups"></a>Groupes d’administration
Avec les groupes d’administration Azure, vous avez la possibilité de gérer efficacement non seulement l’accès, les stratégies et les rapports de groupes d’abonnements, mais aussi l’intégralité du domaine Azure en intervenant sur le groupe d’administration racine. Chaque locataire Azure AD reçoit un seul groupe d’administration de niveau supérieur appelé groupe d’administration racine. Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des attributions de rôles RBAC au niveau de l’annuaire. 

Le groupe d’administration racine est créé automatiquement quand vous effectuez l’une des actions suivantes : 
1. Choisir d’utiliser des groupes d’administration Azure en accédant à **Groupes d’administration** dans le [portail Azure](https://portal.azure.com).
2. Créer un groupe d’administration via un appel d’API.
3. Créer un groupe d’administration avec PowerShell.

Pour une présentation détaillée des groupes d’administration, consultez l’article [Organiser vos ressources avec des groupes d’administration Azure](../azure-resource-manager/management-groups-overview.md).

## <a name="create-a-management-group-in-the-azure-portal"></a>Créer un groupe d’administration dans le portail Azure
Vous pouvez organiser les abonnements en groupes d’administration et y appliquer vos stratégies de gouvernance. Tous les abonnements d’un groupe d’administration héritent automatiquement des stratégies appliquées à ce groupe d’administration. Même si les groupes d’administration ne sont pas obligatoires pour intégrer Security Center, il est vivement recommandé d’en créer au moins un pour que le groupe d’administration racine soit créé. Une fois que le groupe est créé, tous les abonnements sous votre locataire Azure AD y sont liés. Pour des instructions sur PowerShell et d’autres informations, consultez [Créer des groupes d’administration pour gérer les ressources et l’organisation](../azure-resource-manager/management-groups-create.md).

 
1. Connectez-vous au [Portail Azure](http://portal.azure.com).
2. Sélectionnez **Tous les services** > **Groupes d’administration**.
3. Dans la page principale, sélectionnez **Nouveau groupe d’administration**. 

    ![Groupe principal](./media/security-center-management-groups/main.png) 
4.  Renseignez le champ ID du groupe d’administration. 
    - L’**ID du groupe d’administration** est l’identificateur unique de l’annuaire utilisé pour envoyer des commandes sur ce groupe d’administration. Cet identificateur n’est pas modifiable après sa création car il est utilisé dans tout le système Azure pour identifier ce groupe. 
    - Le champ du nom d’affichage correspond au nom qui s’affiche dans le portail Azure. Un nom d’affichage distinct est un champ facultatif lors de la création du groupe d’administration. Il peut être modifié à tout moment.  

      ![Créer](./media/security-center-management-groups/create_context_menu.png)  
5.  Sélectionnez **Enregistrer**.

### <a name="view-management-groups-in-the-azure-portal"></a>Afficher les groupes d’administration dans le portail Azure
1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Pour voir les groupes d’administration, sélectionnez **Tous les services** sous le menu principal d’Azure.
3. Sous **Général**, sélectionnez **Groupes d’administration**.

    ![Créer un groupe d’administration](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Donner une visibilité au niveau locataire et affecter des stratégies

Pour avoir une visibilité de la sécurité de tous les abonnements inscrits dans le locataire Azure AD, un rôle RBAC avec des autorisations de lecture suffisantes doit être attribué au groupe d’administration racine.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Élever l’accès d’un administrateur général dans Azure Active Directory
Les administrateurs de locataires Azure Active Directory n’ont pas d’accès direct aux abonnements Azure. Toutefois, en tant qu’administrateur d’annuaire, ils ont le droit de s’élever à un rôle qui bénéficie d’un accès. Un administrateur de locataires Azure AD doit s’élever à la fonction d’administrateur des accès utilisateur au niveau du groupe d’administration racine pour pouvoir attribuer des rôles RBAC. Pour des instructions sur PowerShell et d’autres informations, consultez [Élever l’accès d’un administrateur général dans Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).

2. Dans la liste de navigation, cliquez sur **Azure Active Directory**, puis sur **Propriétés**.

   ![Propriétés Azure AD - capture d’écran](./media/security-center-management-groups/aad-properties.png)

3. Sous **L’administrateur général peut gérer des abonnements et des groupes d’administration Azure**, positionnez le commutateur sur **Oui**.

   ![L’administrateur général peut gérer des abonnements et des groupes d’administration Azure - capture d’écran](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Lorsque vous positionnez le commutateur sur **Oui**, votre compte d’administrateur général (utilisateur actuellement connecté) est ajouté au rôle Administrateur de l’accès utilisateur dans RBAC Azure dans l’étendue racine (`/`), ce qui vous donne accès à tous les abonnements Azure associés à votre client Azure AD pour les consulter et générer des rapports à leur sujet.

   - Lorsque vous positionnez le commutateur sur **Non**, votre compte d’administrateur général (utilisateur actuellement connecté) est supprimé du rôle Administrateur de l’accès utilisateur dans RBAC Azure. Vous ne pouvez pas voir tous les abonnements Azure associés au client Azure AD, et ne pouvez afficher et gérer que les abonnements Azure auxquels vous avez été autorisé à accéder.

4. Cliquez sur **Enregistrer** pour enregistrer votre paramètre.

    - Ce paramètre n’est pas une propriété globale et s’applique uniquement à l’utilisateur actuellement connecté.

5. Effectuez les tâches que vous devez accomplir via un accès élevé. Lorsque vous avez terminé, repositionnez le commutateur sur **Non**.

### <a name="open-or-refresh-security-center"></a>Ouvrir ou actualiser Security Center
Une fois que vous disposez d’un accès élevé, ouvrez ou actualisez Azure Security Center pour vérifier que vous voyez tous les abonnements sous votre locataire Azure AD. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Veillez à sélectionner tous les abonnements dans le sélecteur d’abonnements que vous souhaitez afficher dans Security Center.
    ![Capture d’écran du sélecteur d’abonnements](./media/security-center-management-groups/subscription-selector.png)
1. Sélectionnez **Tous les services** sous le menu principal d’Azure, puis sélectionnez **Security Center**.
2. Dans la **Vue d’ensemble**, se trouve un graphique des abonnements couverts. 
    ![Capture d’écran du graphique des abonnements couverts](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. Cliquez sur **Couverture** pour voir la liste des abonnements couverts. 
    ![Capture d’écran de la liste des abonnements couverts](./media/security-center-management-groups/security-center-coverage.png)

### <a name="assign-rbac-roles-to-users"></a>Attribuer des rôles RBAC aux utilisateurs
Une fois qu’un administrateur de locataires a un accès élevé, il peut attribuer un rôle RBAC aux utilisateurs appropriés au niveau du groupe d’administration racine. Le rôle recommandé à attribuer est [ **Lecteur**](../role-based-access-control/built-in-roles.md#reader). Ce rôle est nécessaire pour obtenir une visibilité au niveau locataire. Le rôle attribué est automatiquement propagé à tous les groupes d’administration et tous les abonnements sous le groupe d’administration racine. Pour plus d’informations sur les rôles RBAC, consultez [Rôles disponibles](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles). 

1. Installez [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Exécutez les commandes suivantes : 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Quand vous y êtes invité, connectez-vous avec des informations d’identification d’administrateur général. 

    ![Capture d’écran de l’invite de connexion](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Accordez les autorisations du rôle de lecteur en exécutant la commande suivante :

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Pour supprimer le rôle, utilisez la commande suivante : 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Supprimer l’accès élevé 
Une fois que les rôles RBAC ont été attribués aux utilisateurs, l’administrateur de locataires doit se supprimer du rôle d’administrateur des accès utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).

2. Dans la liste de navigation, cliquez sur **Azure Active Directory**, puis sur **Propriétés**.

3. Sous **L’administrateur général peut gérer des abonnements et des groupes d’administration Azure**, positionnez le commutateur sur **Non**.

4. Cliquez sur **Enregistrer** pour enregistrer votre paramètre.



## <a name="adding-subscriptions-to-a-management-groups"></a>Ajout d’abonnements à un groupe d’administration
Vous pouvez ajouter des abonnements au groupe d’administration que vous avez créé. Ces étapes ne sont pas obligatoires pour obtenir une visibilité au niveau locataire ni pour gérer les accès et les stratégies globales.

1. Sous **Groupes d’administration**, sélectionnez un groupe d’administration pour y ajouter votre abonnement.

    ![Sélectionner un groupe d’administration pour y ajouter un abonnement](./media/security-center-management-groups/management-group-subscriptions.png)

2. Sélectionnez **Ajouter existant**.

    ![Ajouter existant](./media/security-center-management-groups/add-existing.png)

3. Entrez un abonnement sous **Ajouter une ressource existante** et cliquez sur **Enregistrer**.

4. Répétez les étapes 1 à 3 pour tous les abonnements concernés.

 > [!NOTE]
 > Les groupes d’administration peuvent contenir à la fois des abonnements et des groupes d’administration enfants. Lorsque vous attribuez un rôle RBAC à un utilisateur dans le groupe d’administration parent, l’accès est hérité par les abonnements du groupe d’administration enfant. Les stratégies définies au niveau du groupe d’administration parent sont aussi héritées par les enfants. 

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à gagner en visibilité au niveau locataire dans Azure Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gérer et répondre aux alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md)

