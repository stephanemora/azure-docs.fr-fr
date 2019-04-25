---
title: Assigner ou supprimer des licences – Azure Active Directory | Microsoft Docs
description: Ces instructions expliquent comment assigner des licences Azure Active Directory à vos utilisateurs ou groupes et les supprimer.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e7a3f80067adb3093bd27e34a45b3afd72b4993
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60247644"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Assigner ou supprimer des licences à l’aide du portail Azure Active Directory
De nombreux services Azure Active Directory (Azure AD) nécessitent que vous activiez un produit Azure AD et que vous assigniez une licence à chacun de vos utilisateurs ou groupes (et membres associés) pour ce produit. Seuls les utilisateurs disposant de licences actives seront en mesure d’accéder et d’utiliser les services Azure AD sous licence.

## <a name="available-product-editions"></a>Éditions disponibles du produit
Il existe plusieurs éditions disponibles pour le produit Azure AD.

- Azure AD Gratuit

- Azure AD Standard

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

Pour obtenir des informations spécifiques sur chacune des éditions du produit et sur les licences associées, consultez [De quelle licence ai-je besoin ?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Afficher les informations d’édition et de licence du produit
Vous pouvez afficher les produits disponibles, y compris les licences individuelles, en recherchant les dates d’expiration en attente et le nombre d’affectations disponibles.

### <a name="to-find-your-product-and-license-details"></a>Trouver le produit et les informations de licence
1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Azure Active Directory**, puis **Licences**.

    La page **Licences** s’affiche.

    ![Page des licences, avec le nombre de produits achetés et les licences attribuées](media/license-users-groups/license-details-blade.png)
    
3. Sélectionnez le lien **Produits achetés** pour afficher la page **Produits** et les informations **Assigné**, **Disponible**, et **Arrive bientôt à expiration** pour chaque édition spécifique du produit.

    ![Page Produits, avec les éditions du produit et les informations de licences associées](media/license-users-groups/license-products-blade-with-products.png)

4. Sélectionnez un nom d’édition de produit pour afficher ses utilisateurs et groupes sous licence.

## <a name="assign-licenses-to-users-or-groups"></a>Attribuer des licences aux utilisateurs ou aux groupes
Assurez-vous que toutes les personnes devant utiliser un service Azure AD sous licence disposent de la licence appropriée. C’est à vous qu’il appartient d’ajouter des droits de licence à des utilisateurs individuels ou à un groupe entier.

>[!Note]
>La gestion des licences par groupe est une fonctionnalité en préversion publique d’Azure AD qui est disponible avec un plan de licence Azure AD payante. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Pour plus d’informations sur l’ajout d’utilisateurs, consultez [Comment ajouter ou supprimer des utilisateurs dans Azure Active Directory](add-users-azure-active-directory.md). Pour plus d’informations sur la création de groupes et l’ajout de membres, consultez [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Attribuer une licence à un utilisateur spécifique
1. Sur la page **Produits**, sélectionnez le nom de l’édition que vous souhaitez affecter à l’utilisateur. Par exemple, _l’offre 2 d’Azure Active Directory Premium_.

    ![Page Produits, avec l’édition du produit en surbrillance](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Sur la page **Offre 2 d’Azure Active Directory Premium**, sélectionnez **Affecter**.

    ![Page Produits, avec l’option Affecter en surbrillance](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Sur la page **Affecter**, sélectionnez **Utilisateurs et groupes**, puis recherchez et sélectionnez l’utilisateur auquel vous souhaitez affecter la licence. Par exemple, _Mary Parker_.

    ![Page Affecter une licence, avec les options Sélectionner et Rechercher en surbrillance](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Sélectionnez **Options d’affectation**. Assurez-vous que les options de licence appropriées sont activées, puis sélectionnez **OK**.

    ![Page d’options de licence, avec toutes les options disponibles dans l’édition](media/license-users-groups/license-option-blade-assignments.png)

    La page **Affecter une licence** s’actualise pour indiquer qu’un utilisateur est sélectionné et que les attributions sont configurées.

    >[!NOTE]
    >Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, vous devez spécifier l’**Emplacement d’utilisation**. Vous pouvez définir cette valeur dans la zone **Azure Active Directory &gt; Utilisateurs &gt; Profil &gt; Paramètres** dans Azure AD.

5. Sélectionnez **Attribuer**.

    L’utilisateur est ajouté à la liste des utilisateurs sous licence et a accès aux services Azure AD inclus.

### <a name="to-assign-a-license-to-an-entire-group"></a>Attribuer une licence à un groupe entier
1. Sur la page **Produits**, sélectionnez le nom de l’édition que vous souhaitez affecter à l’utilisateur. Par exemple, _l’offre 2 d’Azure Active Directory Premium_.

    ![Panneau Produits, avec l’édition du produit en surbrillance](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Sur la page **Offre 2 d’Azure Active Directory Premium**, sélectionnez **Affecter**.

    ![Page Produits, avec l’option Affecter en surbrillance](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Sur la page **Affecter**, sélectionnez **Utilisateurs et groupes**, puis recherchez et sélectionnez le groupe auquel vous souhaitez affecter la licence. Par exemple, _MDM policy - West_ (Stratégie GPM - Ouest).

    ![Page Affecter une licence, avec les options Sélectionner et Rechercher en surbrillance](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Sélectionnez **Options d’affectation**. Assurez-vous que les options de licence appropriées sont activées, puis sélectionnez **OK**.

    ![Page d’options de licence, avec toutes les options disponibles dans l’édition](media/license-users-groups/license-option-blade-group-assignments.png)

    La page **Affecter une licence** s’actualise pour indiquer qu’un utilisateur est sélectionné et que les attributions sont configurées.

    >[!NOTE]
    >Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un groupe, vous devez spécifier l’**Emplacement d’utilisation** pour tous les membres. Vous pouvez définir cette valeur dans la zone **Azure Active Directory &gt; Utilisateurs &gt; Profil &gt; Paramètres** dans Azure AD. Tout utilisateur dont l’emplacement d’utilisation n’est pas spécifié hérite de l’emplacement du locataire.

5. Sélectionnez **Attribuer**.

    Le groupe est ajouté à la liste des groupes sous licence et tous les membres ont accès aux services Azure AD inclus.

## <a name="remove-a-license"></a>Supprimer une licence
Vous pouvez supprimer une licence d’un utilisateur ou d’un groupe depuis la page **Licences**.

### <a name="to-remove-a-license-from-a-specific-user"></a>Pour supprimer une licence d’un utilisateur spécifique
1. Dans la page **Utilisateurs sous licence** de l’édition du produit, sélectionnez l’utilisateur dont vous souhaitez supprimer la licence. Par exemple, _Alain Charon_.

2. Sélectionnez **Supprimer la licence**.

    ![Page Utilisateurs sous licence, avec l’option Supprimer une licence en surbrillance](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Pour supprimer une licence d’un groupe
1. Dans la page **Groupes sous licence** de l’édition du produit, sélectionnez le groupe dont vous souhaitez supprimer la licence. Par exemple, _MDM policy - West_ (Stratégie GPM - Ouest).

2. Sélectionnez **Supprimer la licence**.

    ![Page Groupes sous licence, avec l’option Supprimer une licence en surbrillance](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Les licences héritées par un utilisateur à partir d’un groupe ne peuvent pas être supprimées directement. Au lieu de cela, vous devez supprimer l’utilisateur du groupe à partir duquel il a hérité de la licence.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez assigné vos licences, vous pouvez exécuter les procédures suivantes :

- [Identifier et résoudre les problèmes d’affectation de licence](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Ajouter des utilisateurs sous licence à un groupe pour la gestion des licences](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scénarios, limitations et problèmes connus liés à l’utilisation de groupes dans le cadre de la gestion des licences dans Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)
