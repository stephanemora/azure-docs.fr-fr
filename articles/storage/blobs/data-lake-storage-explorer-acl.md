---
title: 'Explorateur Stockage : Définir des listes de contrôle d’accès dans Azure Data Lake Storage Gen2'
description: Utilisez l’Explorateur Stockage Azure pour gérer les listes de contrôle d’accès (ACL, Access Control List) sur les comptes de stockage pour lesquels un espace de noms hiérarchique (HNS) est activé.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100656311"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utiliser l’Explorateur Stockage Azure pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour gérer les listes de contrôle d’accès (ACL, Access Control List) sur les comptes de stockage pour lesquels un espace de noms hiérarchique (HNS) est activé.

Vous pouvez utiliser l’Explorateur Stockage pour afficher, puis mettre à jour les ACL des répertoires et des fichiers. L’héritage des listes ACL est déjà disponible pour les nouveaux éléments enfants créés sous un répertoire parent. Vous pouvez tout aussi bien appliquer de manière récursive les paramètres ACL au niveau des éléments enfants existants d’un répertoire parent, sans avoir à apporter ces modifications individuellement à chaque élément enfant. 

Cet article explique comment modifier la liste ACL d’un fichier ou d’un répertoire, et comment appliquer les paramètres ACL de manière récursive aux répertoires enfants.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](../common/storage-account-create.md) instructions.

- Explorateur Stockage Azure installé sur votre ordinateur local. Pour installer l’Explorateur Stockage Azure pour Windows, Macintosh ou Linux, consultez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> L’Explorateur Stockage utilise à la fois les [points de terminaison](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) d’objet blob (blob) et de Data Lake Storage Gen2 (DFS) lors de l’utilisation d’Azure Data Lake Storage Gen2. Si l’accès à Azure Data Lake Storage Gen2 est configuré à l’aide de points de terminaison privés, assurez-vous que deux points de terminaison privés sont créés pour le compte de stockage : un avec la sous-ressource cible `blob` et l’autre avec la sous-ressource cible `dfs`.

## <a name="sign-in-to-storage-explorer"></a>Se connecter à l’Explorateur Stockage

Lors du premier démarrage de l’Explorateur Stockage, la fenêtre **Microsoft Azure Storage Explorer - Connect** (Explorateur Stockage Microsoft Azure - Connexion) s’affiche. Bien que l’Explorateur Stockage propose plusieurs méthodes de connexion aux comptes de stockage, une seule est actuellement prise en charge pour la gestion des ACL.

|Tâche|Objectif|
|---|---|
|Ajouter un compte Azure | Vous redirige vers la page de connexion de votre organisation afin de vous authentifier sur Azure. Il s’agit actuellement de la seule méthode d’authentification prise en charge si vous souhaitez gérer et définir des ACL.|
|Utiliser un URI de chaîne de connexion ou de signature d’accès partagé | Peut être utilisé pour accéder directement à un conteneur ou un compte de stockage avec un jeton SAP ou une chaîne de connexion partagée. |
|Utiliser le nom et la clé d’un compte de stockage| Utilisez le nom et la clé et de votre compte de stockage pour vous connecter à Stockage Azure.|

Sélectionnez **Ajouter un compte Azure**, puis cliquez sur **Connexion..** . Suivez les instructions de connexion à votre compte Azure qui s’affichent à l’écran.

![Capture d’écran montrant l’Explorateur Stockage Microsoft Azure, avec l’option Ajouter un compte Azure et le bouton Se connecter en évidence.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Une fois que la connexion est établie, l’Explorateur Stockage Azure se charge avec l’onglet **Explorateur** affiché. Cette vue vous donne accès à l’ensemble de vos comptes Stockage Azure, ainsi qu’au stockage local configuré via les comptes de l’[émulateur de stockage Azure](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou les environnements [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Fenêtre de connexion à l’Explorateur Stockage Microsoft Azure](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="manage-an-acl"></a>Gérer une liste de contrôle d’accès

Cliquez avec le bouton droit sur le conteneur, un répertoire ou un fichier, puis sur **Gérer les listes de contrôle d’accès**.  La capture d’écran suivante montre le menu tel qu’il apparaît lorsque l’on clique avec le bouton droit sur un répertoire.

> [!div class="mx-imgBorder"]
> ![Clic droit sur un répertoire dans l’Explorateur Stockage Azure](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

La boîte de dialogue **Gérer l’accès** permet de gérer les autorisations du propriétaire et du groupe de propriétaires. Elle vous permet également d’ajouter de nouveaux utilisateurs et groupes à la liste de contrôle d’accès pour lesquels vous pouvez ensuite gérer les autorisations.

> [!div class="mx-imgBorder"]
> ![Boîte de dialogue Gérer l’accès](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Pour ajouter un nouvel utilisateur ou un nouveau groupe à la liste de contrôle d’accès, sélectionnez le bouton **Ajouter**. Indiquez ensuite l’entrée Azure Active Directory (Azure AD) correspondante que vous souhaitez ajouter à la liste, puis sélectionnez **Ajouter**.  L’utilisateur ou le groupe apparaît alors dans le champ **Utilisateurs et groupes**, ce qui vous permet de commencer à gérer leurs autorisations.

> [!NOTE]
> Créer un groupe de sécurité dans Azure AD et mettre à jour les autorisations au niveau du groupe plutôt qu’au niveau des utilisateurs individuels est recommandé et constitue une bonne pratique. Pour plus d’informations sur cette recommandation et d’autres meilleures pratiques, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Utilisez les contrôles de type case à cocher pour définir l’accès et les listes ACL par défaut. Pour plus d’informations sur la différence entre ces types de listes ACL, consultez [Types de listes ACL](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Application récursive de listes ACL

Il est possible d’appliquer récursivement des listes ACL au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant.

Pour appliquer des entrées ACL de manière récursive, cliquez avec le bouton droit sur le conteneur ou un répertoire, puis cliquez sur **Propagation des listes de contrôle d’accès**.  La capture d’écran suivante montre le menu tel qu’il apparaît lorsque l’on clique avec le bouton droit sur un répertoire.

> [!div class="mx-imgBorder"]
> ![Clic droit sur un répertoire et choix du paramètre de propagation du contrôle d’accès](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le modèle d’autorisation Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
