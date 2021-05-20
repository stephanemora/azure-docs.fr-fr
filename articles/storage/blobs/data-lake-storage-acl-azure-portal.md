---
title: Utiliser le portail Azure pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2
description: Utilisez le portail Azure pour gérer les listes de contrôle d’accès (ACL, access-control list) dans les comptes de stockage dont l’espace de noms hiérarchique (HNS) est activé.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 623395ad09fd29d7102657f93a24085d388485a0
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109635352"
---
# <a name="use-the-azure-portal-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utiliser le portail Azure pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser [Portail Azure](https://ms.portal.azure.com/) pour gérer la liste de contrôle d’accès (ACL, access-control list) d’un répertoire ou d’un blob dans des comptes de stockage sur lesquels la fonctionnalité d’espace de noms hiérarchique est activée. 

Pour plus d’informations sur la structure de l’ACL, consultez [Listes de contrôle d’accès (ACL) dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

Pour savoir comment utiliser conjointement les ACL et les rôles Azure, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage sur lequel la fonctionnalité d’espace de noms hiérarchique est activée. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Vous devez disposer de l’une des autorisations de sécurité suivantes :

  - Votre identité d’utilisateur s’est vue attribuer le rôle [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du compte de stockage, du groupe de ressources parent ou de l’abonnement.  

  - Vous êtes l’utilisateur propriétaire du conteneur, répertoire ou blob cible auquel vous envisagez d’appliquer les paramètres ACL. 
  
## <a name="manage-an-acl"></a>Gérer une liste de contrôle d’accès

1. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Recherchez votre compte de stockage et affichez la vue d’ensemble du compte.

3. Sélectionnez **Conteneurs** sous **Stockage de données**.
   
   Les conteneurs du compte de stockage apparaissent. 

   > [!div class="mx-imgBorder"]
   > ![emplacement des conteneurs du compte de stockage dans le portail Azure](./media/data-lake-storage-acl-azure-portal/find-containers-in-azure-portal.png)

5. Accédez à un conteneur, un répertoire ou un blob. Cliquez avec le bouton droit sur l’objet, puis sélectionnez **Gérer l’ACL**.

   > [!div class="mx-imgBorder"]
   > ![menu contextuel pour la gestion d’une liste de contrôle d’accès](./media/data-lake-storage-acl-azure-portal/manage-acl-menu-item.png)

   L’onglet **Autorisations d’accès** de la page **Gérer l’ACL** s’affiche. Utilisez les contrôles de cet onglet pour gérer l’accès à l’objet. 

   > [!div class="mx-imgBorder"]
   > ![onglet ACL d’accès de la page Gérer l’ACL](./media/data-lake-storage-acl-azure-portal/access-acl-page.png)

7. Pour ajouter un *principal de sécurité* à l’ACL, sélectionnez le bouton **Ajouter un principal**. 

   > [!TIP]
   > Un principal de sécurité est un objet qui représente un utilisateur, un groupe, un principal de service ou une identité managée défini(e) dans Azure Active Directory (AD). 

   Recherchez le principal de sécurité à l’aide de la zone de recherche, puis cliquez sur le bouton **Sélectionner**. 

   > [!div class="mx-imgBorder"]
   > ![Ajouter un principal de sécurité à l’ACL](./media/data-lake-storage-acl-azure-portal/get-security-principal.png)

   > [!NOTE]
   > Nous vous recommandons de créer un groupe de sécurité dans Azure AD, puis de gérer les autorisations sur le groupe plutôt que pour des utilisateurs individuels. Pour plus d’informations sur cette recommandation et d’autres meilleures pratiques, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

8. Pour gérer l’*ACL par défaut*, sélectionnez l’onglet **Autorisations par défaut**, puis cochez la case **Configurer les autorisations par défaut**. 

   > [!TIP]
   > Une ACL par défaut est un modèle d’ACL qui détermine les ACL d’accès pour tous les éléments enfants créés sous un répertoire. Comme un blob n’a pas d’ACL par défaut, cet onglet s’affiche uniquement pour les répertoires. 

   > [!div class="mx-imgBorder"]
   > ![onglet ACL par défaut de la page Gérer l’ACL](./media/data-lake-storage-acl-azure-portal/default-acl-page.png)

## <a name="apply-an-acl-recursively"></a>Appliquer une ACL de manière récursive

Il est possible d’appliquer récursivement des listes ACL au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant. Toutefois, vous ne pouvez pas appliquer des entrées ACL de manière récursive en utilisant le portail Azure. 

Pour appliquer des ACL de manière récursive, utilisez Explorateur Stockage Azure, PowerShell ou Azure CLI. Si vous préférez écrire du code, vous pouvez également utiliser les API .NET, Java, Python ou Node.js. 

Vous trouverez la liste complète des guides ici : [Comment définir des listes de contrôle d’accès](data-lake-storage-access-control.md#how-to-set-acls). 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le modèle d’autorisation Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)