---
title: Afficher, ajouter et supprimer des affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory - Azure Active Directory
description: Découvrez comment afficher, ajouter et supprimer des affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 10/05/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f944caecae6d35e680f5c5beb1a6e23fc422e698
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618084"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Afficher, ajouter et supprimer des affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory

Dans la gestion des droits d'utilisation Azure AD, vous pouvez voir qui a été affecté aux packages d’accès, ainsi que leur stratégie et leur état. Si un package d’accès a une stratégie appropriée, vous pouvez également affecter directement l’utilisateur à un package d’accès. Cet article explique comment afficher, ajouter et supprimer des affectations pour des packages d’accès.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la gestion des droits d’utilisation Azure AD et affecter des utilisateurs aux packages d’accès, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Afficher les utilisateurs qui ont une affectation

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue, gestionnaire de package d’accès ou gestionnaire d’attribution de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Affectations** pour afficher une liste des affectations actives.

    ![Liste des affectations à un package d’accès](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Cliquez sur une affectation spécifique pour voir plus de détails.

1. Pour afficher une liste des affectations dont les rôles de ressources n’étaient pas correctement provisionnés, cliquez sur filtre d’état et sélectionnez **Livraison en cours**.

    Vous pouvez voir des détails supplémentaires sur les erreurs de remise en recherchant la requête correspondante de l'utilisateur sur la page **Requêtes**.

1. Pour voir les affectations expirées, cliquez sur le filtre d’état et sélectionnez **Expiré**.

1. Pour télécharger un fichier CSV contenant la liste filtrée, cliquez sur **Télécharger**.

## <a name="view-assignments-programmatically"></a>Afficher les attributions par programmation
### <a name="view-assignments-with-microsoft-graph"></a>Afficher les attributions avec Microsoft Graph
Vous pouvez également récupérer des affectations dans un package d’accès à l’aide de Microsoft Graph.  Un utilisateur doté d’un rôle approprié avec une application disposant de l’autorisation déléguée `EntitlementManagement.Read.All` ou `EntitlementManagement.ReadWrite.All` peut appeler l’API pour [lister les attributions accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true). Un administrateur de gouvernance des identités peut récupérer des packages d’accès à partir de plusieurs catalogues. Cependant, si l’utilisateur ne dispose que de rôles d’administration délégués propres au catalogue, la demande doit fournir un filtre pour indiquer un package d’accès spécifique comme `$filter=accessPackage/id eq 'a914b616-e04e-476b-aa37-91038f0b165b'`. Une application qui dispose de l’autorisation `EntitlementManagement.Read.All` ou `EntitlementManagement.ReadWrite.All` peut également utiliser cette API.

### <a name="view-assignments-with-powershell"></a>Afficher les attributions avec PowerShell

Vous pouvez exécuter cette requête dans PowerShell avec l’applet de commande `Get-MgEntitlementManagementAccessPackageAssignment` issue du module [Microsoft Graph PowerShell cmdlets for Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) version 1.6.0 ou ultérieure. Cette applet de commande accepte comme paramètre l’ID du package d’accès, qui est inclus dans la réponse de l’applet de commande `Get-MgEntitlementManagementAccessPackage`.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -ExpandProperty target -All -ErrorAction Stop
$assignments | ft Id,AssignmentState,TargetId,{$_.Target.DisplayName}
```

## <a name="directly-assign-a-user"></a>Affecter directement un utilisateur 

Dans certains cas, vous pouvez affecter directement des utilisateurs spécifiques à un package d'accès pour leur éviter d’avoir à passer par le processus de demande du package d'accès. Pour affecter directement des utilisateurs, le package d'accès doit avoir une stratégie qui autorise les affectations directes par l'administrateur.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs, gestionnaire de package d'accès ou gestionnaire d’affectation de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu gauche, cliquez sur **Affectations**.

1. Cliquez sur **Nouvelle affectation** pour ouvrir la fenêtre Ajouter un utilisateur au package d’accès.

    ![Affectations - Ajouter un utilisateur au package d’accès](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1.  Dans la liste **Sélectionner une stratégie**, sélectionnez une stratégie selon laquelle les requêtes et le cycle de vie des utilisateurs seront régis et suivis. Si vous souhaitez que les utilisateurs sélectionnés aient des paramètres de stratégie différents, vous pouvez cliquer sur **Créer une nouvelle stratégie** pour ajouter une nouvelle stratégie.

1.  Une fois que vous avez sélectionné une stratégie, vous pouvez utiliser l’option Ajouter des utilisateurs pour sélectionner les utilisateurs auxquels vous souhaitez attribuer ce package d’accès, dans le cadre de la stratégie choisie.

    > [!NOTE]
    > Si vous sélectionnez une stratégie avec des questions, vous ne pouvez affecter qu’un seul utilisateur à la fois.

1. Définissez la date et l'heure de début et de fin de l'affectation des utilisateurs sélectionnés. Si aucune date de fin n’est fournie, les paramètres du cycle de vie de la stratégie seront utilisés.

1.  Si vous le souhaitez, fournissez une justification de votre affectation directe à des fins d’archivage.

1.  Si la stratégie sélectionnée inclut des informations supplémentaires sur le demandeur, cliquez sur **Afficher les questions** pour répondre à la place des utilisateurs, puis cliquez sur **Enregistrer**.  

     ![Attribution - Cliquez sur Afficher les questions](./media/entitlement-management-access-package-assignments/assignments-view-questions.png)

    ![Attributions - Volet Questions](./media/entitlement-management-access-package-assignments/assignments-questions-pane.png)

1. Cliquez sur **Ajouter** pour affecter directement les utilisateurs sélectionnés au package d'accès.

    Après quelques instants, cliquez sur **Actualiser** pour voir les utilisateurs dans la liste des affectations.
    
> [!NOTE]
> Lorsque vous affectez des utilisateurs à un package d’accès, les administrateurs doivent vérifier que les utilisateurs sont éligibles à ce package d’accès en fonction des exigences de stratégie existantes. Dans le cas contraire, les utilisateurs ne seront pas affectés au package d’accès. Si le package d’accès contient une stratégie qui exige que les demandes de l’utilisateur soient approuvées, les utilisateurs ne peuvent pas être directement attribués au package sans l’approbation nécessaire des approbateurs désignés.

## <a name="directly-assign-any-user-preview"></a>Attribuer directement à n’importe quel utilisateur (préversion)
La gestion des droits d’utilisation Azure AD vous permet également d’attribuer directement un package d’accès à des utilisateurs externes pour faciliter la collaboration avec les partenaires. Pour ce faire, le package d’accès doit disposer d’une stratégie qui permet aux utilisateurs qui ne sont pas encore dans votre annuaire de demander l’accès.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs, gestionnaire de package d'accès ou gestionnaire d’affectation de package d’accès

1.  Dans le portail Azure, sélectionnez **Azure Active Directory**, puis **Identity Governance**.

1.  Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d’accès auquel vous souhaitez ajouter un utilisateur.

1.  Dans le menu gauche, cliquez sur **Affectations**.

1.  Sélectionnez **Nouvelle affectation** pour ouvrir **Ajouter un utilisateur au package d’accès**.

1.  Dans la liste **Sélectionner une stratégie**, sélectionnez une stratégie qui autorise l’option **Pour les utilisateurs qui ne sont pas dans votre répertoire**

1. Sélectionnez **Tous les utilisateurs**. Vous pouvez spécifier les utilisateurs auxquels vous souhaitez attribuer ce package d’accès.
    ![Affectations – Ajouter un utilisateur au package d’accès](./media/entitlement-management-access-package-assignments/assignments-add-any-user.png)

1. Entrez le **Nom** de l’utilisateur (facultatif) et **l’adresse e-mail** de l’utilisateur (obligatoire).

    > [!NOTE]
    > - L’utilisateur que vous souhaitez ajouter doit se trouver dans l’étendue de la stratégie. Par exemple, si votre stratégie est définie sur **Organisations connectées spécifiques**, l’adresse e-mail de l’utilisateur doit être comprise dans le ou les domaines de l’organisation (ou des organisations) sélectionnée. Si vous essayez d’ajouter une utilisatrice ayant l’adresse e-mail jen@*foo.com*, alors que le domaine de l’organisation sélectionnée est *bar.com*, il n’est pas possible d’ajouter cette utilisatrice au package d’accès.
    > - De même, si vous définissez votre stratégie de manière à inclure **Toutes les organisations connectées configurées**, l’adresse e-mail de l’utilisateur doit correspondre à l’une des organisations connectées configurées. Dans le cas contraire, l’utilisateur n’est pas ajouté au package d’accès.
    > - Si vous souhaitez ajouter un utilisateur au package d’accès, vous devez vous assurer de sélectionner **Tous les utilisateurs (toutes les organisations connectées + tous les utilisateurs externes)** lors de la configuration de votre stratégie.

1.  Définissez la date et l'heure de début et de fin de l'affectation des utilisateurs sélectionnés. Si aucune date de fin n’est fournie, les paramètres du cycle de vie de la stratégie seront utilisés.
1.  Cliquez sur **Ajouter** pour affecter directement les utilisateurs sélectionnés au package d'accès.
1.  Après quelques instants, cliquez sur **Actualiser** pour voir les utilisateurs dans la liste des affectations.

## <a name="directly-assigning-users-programmatically"></a>Affectation d’utilisateurs directement par programmation
### <a name="assign-a-user-to-an-access-package-with-microsoft-graph"></a>Affecter un utilisateur à un package d’accès avec Microsoft Graph
Vous pouvez également affecter directement un utilisateur à un package d’accès à l’aide de Microsoft Graph.  Un utilisateur doté d’un rôle approprié avec une application disposant de l’autorisation déléguée `EntitlementManagement.ReadWrite.All`, ou une application disposant de cette autorisation, peut appeler l’API pour [créer un accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true). Dans cette demande, la valeur de la propriété `requestType` doit être `AdminAdd`, et la propriété `accessPackageAssignment` est une structure qui contient le `targetId` de l’utilisateur affecté.

### <a name="assign-a-user-to-an-access-package-with-powershell"></a>Affecter un utilisateur à un package d’accès avec PowerShell

Vous pouvez affecter un utilisateur à un package d’accès dans PowerShell avec l’applet de commande `New-MgEntitlementManagementAccessPackageAssignmentRequest` issue du module [Microsoft Graph PowerShell cmdlets for Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) version 1.6.0 ou ultérieure. Cette applet de commande accepte comme paramètres :
* L’ID du package d’accès, qui est inclus dans la réponse de l’applet de commande `Get-MgEntitlementManagementAccessPackage`,
* L’ID de la stratégie d’attribution du package d’accès, qui est inclus dans la réponse de l’applet de commande `Get-MgEntitlementManagementAccessPackageAssignmentPolicy`,
* L’ID d’objet de l’utilisateur cible.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -TargetId "a43ee6df-3cc5-491a-ad9d-ea964ef8e464"
```

Vous pouvez également affecter plusieurs utilisateurs à un package d’accès dans PowerShell avec l’applet de commande `New-MgEntitlementManagementAccessPackageAssignment` issue du module [Microsoft Graph PowerShell cmdlets for Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) version 1.6.1 ou ultérieure. Cette applet de commande accepte comme paramètres :
* L’ID du package d’accès, qui est inclus dans la réponse de l’applet de commande `Get-MgEntitlementManagementAccessPackage`,
* L’ID de la stratégie d’attribution du package d’accès, qui est inclus dans la réponse de l’applet de commande `Get-MgEntitlementManagementAccessPackageAssignmentPolicy`,
* Les ID d’objet des utilisateurs cibles, soit sous la forme d’un tableau de chaînes, soit sous la forme d’une liste d’utilisateurs membres, retournés par l’applet de commande `Get-MgGroupMember`.

Par exemple, si vous souhaitez faire en sorte que tous les utilisateurs membres d’un groupe soient également affectés à un package d’accès, vous pouvez utiliser cette applet de commande afin de créer des demandes pour les utilisateurs qui n’ont pas d’affectations.  Notez que cette applet de commande crée uniquement des affectations et qu’elle ne permet pas de supprimer les affectations.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All,Directory.Read.All"
Select-MgProfile -Name "beta"
$members = Get-MgGroupMember -GroupId "a34abd69-6bf8-4abd-ab6b-78218b77dc15"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -RequiredGroupMember $members
```

## <a name="remove-an-assignment"></a>Supprimer une affectation

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs, gestionnaire de package d'accès ou gestionnaire d’affectation de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu gauche, cliquez sur **Affectations**.
 
1. Cochez la case en regard de l’utilisateur dont vous souhaitez supprimer l’affectation du package d’accès. 

1. Cliquez sur le bouton **Supprimer** en haut du volet gauche. 
 
    ![Affectations - Supprimer un utilisateur du package d’accès](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Une notification s’affiche pour vous informer que l’affectation a été supprimée. 

## <a name="remove-an-assignment-programmatically"></a>Supprimer une attribution programmatiquement
### <a name="remove-an-assignment-with-microsoft-graph"></a>Supprimer une attribution avec Microsoft Graph
Vous pouvez également supprimer l’attribution d’un package d’accès à un utilisateur à l’aide de Microsoft Graph.  Un utilisateur doté d’un rôle approprié avec une application disposant de l’autorisation déléguée `EntitlementManagement.ReadWrite.All`, ou une application disposant de cette autorisation, peut appeler l’API pour [créer un accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true).  Dans cette demande, la valeur de la propriété `requestType` doit être `AdminRemove`, et la propriété `accessPackageAssignment` est une structure qui contient la propriété `id` qui identifie le `accessPackageAssignment` supprimé.

### <a name="remove-an-assignment-with-powershell"></a>Supprimer une attribution avec PowerShell

Vous pouvez supprimer l’attribution d’un utilisateur dans PowerShell avec l’applet de commande `New-MgEntitlementManagementAccessPackageAssignmentRequest` issue du module [Microsoft Graph PowerShell cmdlets for Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) version 1.6.0 ou ultérieure.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -Filter "accessPackageId eq '9f573551-f8e2-48f4-bf48-06efbb37c7b8' and assignmentState eq 'Delivered'" -All -ErrorAction Stop
$toRemove = $assignments | Where-Object {$_.targetId -eq '76fd6e6a-c390-42f0-879e-93ca093321e7'}
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageAssignmentId $toRemove.Id -RequestType "AdminRemove"
```

## <a name="next-steps"></a>Étapes suivantes

- [Changer la demande et les paramètres pour un package d’accès](entitlement-management-access-package-request-policy.md)
- [Afficher les rapports et les journaux](entitlement-management-reports.md)
