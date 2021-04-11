---
title: Utiliser l’Explorateur Stockage Azure avec Azure Data Lake Storage Gen2
description: Utilisez l'Explorateur Stockage Azure pour gérer les répertoires, les fichiers et les listes de contrôle d'accès sur les comptes de stockage pour lesquels un espace de noms hiérarchique (HNS) est activé.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652273"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utiliser l’Explorateur Stockage Azure pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser l’[Explorateur Azure Storage](https://azure.microsoft.com/features/storage-explorer/) pour créer et gérer des répertoires et des fichiers sur les comptes de stockage pour lesquels un espace de noms hiérarchique (HNS) est activé.

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

## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur contient des répertoires et des fichiers. Pour en créer un, développez le compte de stockage que vous avez créé à l'étape précédente. Sélectionnez **Conteneurs d’objets blob**, cliquez avec le bouton droit, puis sélectionnez **Créer un conteneur d’objets blob**. Entrez le nom de votre conteneur. Consultez la section [Créer un conteneur](storage-quickstart-blobs-dotnet.md#create-a-container) pour obtenir la liste des règles et restrictions applicables aux noms de conteneurs. Quand vous avez terminé, appuyez sur **Entrée** pour créer le conteneur. Une fois le conteneur créé, il apparaît sous le dossier **Conteneurs d'objets blob** correspondant au compte de stockage sélectionné.

![Explorateur Stockage Microsoft Azure - Création d’un conteneur](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Créer un répertoire

Pour créer un répertoire, sélectionnez le conteneur que vous avez créé à l'étape précédente. Sur le ruban du conteneur, choisissez le bouton **Nouveau dossier**. Entrez le nom de votre répertoire. Lorsque vous avez terminé, appuyez sur **Entrée** pour créer le répertoire. Une fois le répertoire créé, il apparaît dans la fenêtre de l'éditeur.

![Explorateur Stockage Microsoft Azure - Création d'un répertoire](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Charger des objets blob dans le répertoire

Sur le ruban du répertoire, choisissez le bouton **Charger**. Ce faisant, vous avez la possibilité de télécharger un dossier ou un fichier.

Choisissez les fichiers ou le dossier à télécharger.

![Explorateur Stockage Microsoft Azure - Charger un objet blob](media/data-lake-storage-explorer/upload-file.png)

Lorsque vous sélectionnez **OK**, les fichiers sélectionnés sont mis en file d’attente de téléchargement ; ils sont traités un à un. Une fois le téléchargement terminé, les résultats s’affichent dans la fenêtre **Activités**.

## <a name="view-blobs-in-a-directory"></a>Afficher les objets blob d’un répertoire

Dans l’application **Explorateur Stockage Azure**, sélectionnez un répertoire sous un compte de stockage. Le volet principal affiche une liste des objets blobs hébergés dans le répertoire sélectionné.

![Explorateur Stockage Microsoft Azure - Liste des objets blob dans un répertoire](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Télécharger des objets blob

Pour télécharger un fichier à l'aide de l'**Explorateur Stockage Azure**, sélectionnez le fichier, puis choisissez **Télécharger** sur le ruban. La boîte de dialogue qui s’ouvre vous permet de saisir un nom de fichier. Sélectionnez **Enregistrer** pour lancer le téléchargement d'un fichier à l'emplacement local.

## <a name="next-steps"></a>Étapes suivantes

Découvrir comment gérer les autorisations de fichiers et de répertoires en définissant des listes de contrôle d’accès (ACL)

> [!div class="nextstepaction"]
> [Utiliser l’Explorateur Stockage Azure pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
