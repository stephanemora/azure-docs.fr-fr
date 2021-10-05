---
title: Créer et gérer un catalogue de ressources dans la gestion des droits d'utilisation - Azure AD
description: Découvrez comment créer un conteneur de ressources et de packages d’accès dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: ''
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 8/31/2021
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540c207388dfc5b37e2dd03939b14753c3d8ec7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791780"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Créer et gérer un catalogue de ressources dans la gestion des droits d’utilisation Azure AD

Cet article explique comment créer et gérer un catalogue de ressources et de packages d'accès dans la gestion des droits d'utilisation Azure Active Directory (Azure AD).

## <a name="create-a-catalog"></a>Créer un catalogue

Un catalogue est un conteneur de ressources et de packages d’accès. Vous créez un catalogue lorsque vous souhaitez regrouper des ressources et packages d’accès liés. La personne qui crée le catalogue en devient le premier propriétaire. Un propriétaire de catalogue peut ajouter d’autres propriétaires de catalogue.

**Rôles prérequis :** administrateur d’entreprise, administrateur de la gouvernance des identités, administrateur d’utilisateurs ou créateur de catalogue

> [!NOTE]
> Les utilisateurs qui se sont vu attribuer le rôle d’administrateur d’utilisateurs ne pourront plus créer de catalogues ni gérer les packages d’accès dans un catalogue dont ils ne sont pas propriétaires. Si le rôle d’administrateur d’utilisateurs a été attribué à des utilisateurs de votre organisation pour configurer des catalogues, des packages d’accès ou des stratégies de la gestion des droits d’utilisation, vous devez attribuer à ces utilisateurs le rôle d’administrateur de gouvernance des identités.

Pour créer un catalogue :

1. Dans le Portail Azure, sélectionnez **Azure Active Directory** > **Gouvernance des identités**.

1. Dans le menu de gauche, sélectionnez **Catalogues**.

    ![Capture d’écran qui montre des catalogues de gestion des droits d’utilisation dans le Portail Azure.](./media/entitlement-management-catalog-create/catalogs.png)

1. Sélectionnez **Nouveau catalogue**.

1. Entrez un nom unique pour le catalogue et fournissez une description.

    Les utilisateurs verront ces informations dans les détails d’un package d’accès.

1. Si vous souhaitez que les packages d’accès dans ce catalogue soient accessibles aux utilisateurs qui les demandent dès leur création, définissez **Activé** sur **Oui**.

1. Si vous souhaitez que les utilisateurs de répertoires externes sélectionnés puissent demander des packages d’accès dans ce catalogue, définissez **Activé pour les utilisateurs externes** sur **Oui**.

    ![Capture d’écran montrant le volet Nouveau catalogue.](./media/entitlement-management-shared/new-catalog.png)

1. Sélectionnez **Créer** pour créer le catalogue.

## <a name="create-a-catalog-programmatically"></a>Créer un catalogue par programmation

Il existe deux manières de créer un catalogue par programmation.

### <a name="create-a-catalog-with-microsoft-graph"></a>Créer un catalogue avec Microsoft Graph

Vous pouvez créer un catalogue à l'aide de Microsoft Graph. Un utilisateur doté d’un rôle approprié avec une application disposant de l’autorisation déléguée `EntitlementManagement.ReadWrite.All`, ou une application disposant de cette autorisation, peut appeler l’API pour [créer un accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

### <a name="create-a-catalog-with-powershell"></a>Créer un catalogue avec PowerShell

Vous pouvez également créer un catalogue dans PowerShell avec la cmdlet `New-MgEntitlementManagementAccessPackageCatalog` issue du module [Cmdlets Microsoft Graph PowerShell pour la gouvernance des identités](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) version 1.6.0 ou ultérieure.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>Ajouter des ressources à un catalogue

Pour inclure des ressources dans un package d’accès, les ressources doivent exister dans un catalogue. Les types de ressources que vous pouvez ajouter sont des groupes, des applications et des sites SharePoint Online. Par exemple :

* Les groupes peuvent être des groupes Microsoft 365 ou de groupes de sécurité Azure AD créés sur le cloud. Les groupes issus d’Active Directory local ne peuvent pas être attribués en tant que ressources, car leurs attributs de propriétaire ou de membre ne sont pas modifiables dans Azure AD. Les groupes qui proviennent d’Exchange Online en tant que groupes de distribution ne peuvent pas non plus être modifiés dans Azure AD.
* Les applications peuvent être des applications d’entreprise Azure AD, ce qui inclut les applications SaaS (software as a service) et vos propres applications intégrées avec Azure AD. Pour plus d’informations sur la sélection des ressources appropriées pour les applications avec plusieurs rôles, consultez [Ajouter des rôles de ressources](entitlement-management-access-package-resources.md#add-resource-roles).
* Les sites peuvent être des sites ou des collections de sites SharePoint Online.

**Rôles prérequis :** consultez [Rôles requis pour ajouter des ressources à un catalogue](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

Pour ajouter des ressources à un catalogue :

1. Dans le Portail Azure, sélectionnez **Azure Active Directory** > **Gouvernance des identités**.

1. Dans le menu de gauche, sélectionnez **Catalogues**, puis ouvrez le catalogue auquel vous souhaitez ajouter des ressources.

1. Sélectionnez **Ressources** dans le menu de gauche.

1. Sélectionnez **Ajouter des ressources**.

1. Sélectionnez **Groupes et équipes**, **Applications** ou **Sites SharePoint** du type de ressource.

    Si vous ne voyez aucune ressource que vous souhaitez ajouter ou si vous ne pouvez pas ajouter de ressource, vérifiez que vous possédez le rôle d’annuaire Azure AD et le rôle de gestion des droits d’utilisation requis. Vous devrez peut-être demander à quelqu’un avec les rôles requis d’ajouter la ressource à votre catalogue. Pour plus d’informations, consultez [Rôles requis pour ajouter des ressources à un catalogue](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Sélectionnez une ou plusieurs ressources du type que vous souhaitez ajouter au catalogue.

    ![Capture d’écran qui montre le volet Ajouter des ressources à un catalogue.](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Lorsque vous avez terminé, sélectionnez **Ajouter**.

    Ces ressources peuvent désormais être incluses dans des packages d’accès du catalogue.

### <a name="add-resource-attributes-preview-in-the-catalog"></a>Ajouter des attributs de ressource (préversion) dans le catalogue

Les attributs sont des champs obligatoires auxquels les demandeurs sont invités à répondre avant de soumettre leur demande d’accès. Leurs réponses à ces attributs seront présentées aux approbateurs et également horodaté sur l’objet utilisateur dans Azure AD. 

> [!NOTE]
>Tous les attributs configurés sur une ressource requièrent une réponse avant qu’une requête de package d’accès contenant cette ressource puisse être envoyée. Si les demandeurs ne fournissent pas de réponse, leur requête n’est pas traitée.

Pour exiger des attributs pour les demandes d’accès :

1. Sélectionnez **Ressources** dans le menu de gauche et une liste de ressources dans le catalogue s’affiche. 

1. Sélectionnez les points de suspension en regard de la ressource dans laquelle vous souhaitez ajouter des attributs, puis sélectionnez **Exiger les attributs (préversion)** . 

    ![Capture d’écran qui montre la sélection des attributs requis (préversion).](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  Sélectionner le type d'attribut :

    1. **Intégré** comprend les attributs du profil utilisateur Azure AD.
    1. L'**extension de schéma d’annuaire** permet de stocker davantage de données dans Azure AD sur des objets utilisateur et d’autres objets d’annuaire. Cela comprend les groupes, les détails de l’abonné et les principaux du service. Seuls les attributs d’extension sur les objets utilisateur peuvent être utilisés pour envoyer des revendications vers des applications.
1. Si vous avez choisi **Intégré**, sélectionnez un attribut dans la liste déroulante. Si vous avez choisi **Extension de schéma d’annuaire**, entrez le nom de l’attribut dans la zone de texte.

    > [!NOTE]
    > L’attribut User.mobilePhone peut être mis à jour uniquement pour les utilisateurs non-administrateurs. En savoir plus sur [ce site web](/graph/permissions-reference#remarks-5).

1.  Sélectionnez le format de la réponse que les demandeurs doivent utiliser pour leur réponse. Les formats de réponse incluent le **texte court**, le **choix multiples** et le **texte long**.

1.  Si vous sélectionnez le choix multiples, sélectionnez **Modifier et localiser** pour configurer les options de réponse. 
    1. Dans le volet **Afficher/modifier la question** qui s’affiche, entrez les options de réponse que vous souhaitez attribuer au demandeur lorsqu’il répond à la question dans les boîtes **Valeurs de réponse**.
    1. Sélectionnez le langage de l’option de réponse. Vous pouvez localiser les options de réponse si vous choisissez plus de langages.
    1. Entrez le nombre de réponses dont vous avez besoin, puis sélectionnez **Enregistrer**.

1. Si vous souhaitez que la valeur de l’attribut soit modifiable lors des affectations directes et des demandes en libre-service, sélectionnez **Oui**.

    > [!NOTE]
    > ![Capture d’écran montrant comment rendre les attributs modifiables.](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - Si vous sélectionnez **Non** dans la boîte **La valeur de l’attribut est modifiable** et que la valeur de l’attribut *est vide*, les utilisateurs peuvent entrer la valeur de cet attribut. Après l’enregistrement, la valeur ne peut pas être modifiée. 
    > - Si vous sélectionnez **Non** dans la boîte **La valeur de l’attribut est modifiable** et que la valeur de l’attribut *n’est pas vide*, les utilisateurs ne peuvent pas modifier la valeur préexistante lors des affectations directes et des demandes en libre-service.
 
    ![Capture d’écran qui montre l’ajout de localisations.](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  Si vous souhaitez ajouter la localisation, sélectionnez **Ajouter une localisation**.

    1. Dans le volet **Ajouter des localisations pour la question**, sélectionnez le code du langage dans lequel vous localisez la question relative à l’attribut sélectionné.
    1. Dans le langage que vous avez configuré, entrez la question dans la boîte **Texte localisé**.
    1. Après avoir ajouté toutes les localisations dont vous avez besoin, sélectionnez **Enregistrer**.

       ![Capture d’écran montrant l’enregistrement des localisations.](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  Une fois toutes les informations d’attribut renseignées sur la page **Attributs requis (préversion)** , sélectionnez **Enregistrer**.

### <a name="add-a-multi-geo-sharepoint-site"></a>Ajouter un site SharePoint multigéographique

1. Si les capacités [multigéographiques](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) sont activées pour SharePoint, sélectionnez l’environnement à partir duquel vous souhaitez sélectionner des sites.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="Capture d’écran montrant le volet Sélectionner des sites SharePoint Online.":::

1. Sélectionnez ensuite les sites que vous souhaitez ajouter au catalogue.

### <a name="add-a-resource-to-a-catalog-programmatically"></a>Ajouter une ressource à un catalogue par programmation

Vous pouvez également ajouter une ressource à un catalogue à l'aide de Microsoft Graph. Un utilisateur doté d'un rôle approprié, ou un propriétaire de catalogue et de ressource, avec une application disposant de l'autorisation déléguée `EntitlementManagement.ReadWrite.All` peut appeler l'API pour [créer une demande accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true). Toutefois, une application avec des autorisations d’application ne peut pas encore ajouter par programmation une ressource sans contexte utilisateur au moment de la requête.

## <a name="remove-resources-from-a-catalog"></a>Supprimer des ressources d’un catalogue

Vous pouvez supprimer des ressources d’un catalogue. Une ressource ne peut être supprimée d’un catalogue que si elle n’est pas utilisée dans un des packages d’accès du catalogue.

**Rôles prérequis :** consultez [Rôles requis pour ajouter des ressources à un catalogue](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

Pour supprimer des ressources d’un catalogue :

1. Dans le Portail Azure, sélectionnez **Azure Active Directory** > **Gouvernance des identités**.

1. Dans le menu de gauche, sélectionnez **Catalogues**, puis ouvrez le catalogue duquel vous souhaitez supprimer des ressources.

1. Sélectionnez **Ressources** dans le menu de gauche.

1. Sélectionnez les ressources que vous souhaitez supprimer.

1. Sélectionnez **Supprimer**. Si vous le souhaitez, sélectionnez les points de suspension ( **...** ), puis sélectionnez **Supprimer la ressource**.

## <a name="add-more-catalog-owners"></a>Ajouter plus de propriétaires de catalogue

L’utilisateur qui a créé un catalogue devient le premier propriétaire de catalogue. Pour déléguer la gestion d’un catalogue, ajoutez des utilisateurs au rôle de propriétaire de catalogue. L’ajout de propriétaires de catalogue permet de partager les responsabilités de gestion du catalogue.

**Rôle prérequis :** administrateur d’entreprise, administrateur de la gouvernance des identités, administrateur d’utilisateurs ou propriétaire de catalogue

Pour attribuer un utilisateur au rôle de propriétaire de catalogue :

1. Dans le Portail Azure, sélectionnez **Azure Active Directory** > **Gouvernance des identités**.

1. Dans le menu de gauche, sélectionnez **Catalogues**, puis ouvrez le catalogue auquel vous souhaitez ajouter des administrateurs.

1. Dans le menu de gauche, sélectionnez **Rôles et administrateurs**.

    ![Capture d’écran montrant les rôles de catalogue et les administrateurs.](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Sélectionnez **Ajouter des propriétaires** pour sélectionner les membres de ces rôles.

1. Cliquez sur **Sélectionner** pour ajouter ces membres.

## <a name="edit-a-catalog"></a>Modifier un catalogue

Vous pouvez modifier le nom et la description d’un catalogue. Les utilisateurs verront ces informations dans les détails d’un package d’accès.

**Rôle prérequis :** administrateur d’entreprise, administrateur de la gouvernance des identités, administrateur d’utilisateurs ou propriétaire de catalogue

Pour modifier un catalogue :

1. Dans le Portail Azure, sélectionnez **Azure Active Directory** > **Gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **Catalogues**, puis ouvrez le catalogue que vous souhaitez modifier.

1. Dans la page **Vue d’ensemble** du catalogue, sélectionnez **Modifier**.

1. Modifiez le nom, la description ou les paramètres activés du catalogue.

    ![Capture d’écran montrant la modification des paramètres du catalogue.](./media/entitlement-management-shared/catalog-edit.png)

1. Sélectionnez **Enregistrer**.

## <a name="delete-a-catalog"></a>Supprimer un catalogue

Vous pouvez supprimer un catalogue, mais seulement s’il ne contient pas de packages d’accès.

**Rôle prérequis :** administrateur d’entreprise, administrateur de la gouvernance des identités, administrateur d’utilisateurs ou propriétaire de catalogue

Pour supprimer un catalogue :

1. Dans le Portail Azure, sélectionnez **Azure Active Directory** > **Gouvernance des identités**.

1. Dans le menu de gauche, sélectionnez **Catalogues**, puis ouvrez le catalogue que vous souhaitez supprimer.

1. Dans la page **Vue d’ensemble** du catalogue, sélectionnez **Supprimer**.

1. Dans la boîte de message qui s’affiche, sélectionnez **Oui**.

### <a name="delete-a-catalog-programmatically"></a>Supprimer un catalogue par programmation

Vous pouvez également supprimer un catalogue à l'aide de Microsoft Graph. Un utilisateur doté d'un rôle approprié avec une application disposant de l'autorisation déléguée `EntitlementManagement.ReadWrite.All` peut appeler l'API pour [supprimer un catalogue accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Étapes suivantes

[Déléguer la gouvernance des accès aux gestionnaires de package d’accès](entitlement-management-delegate-managers.md)