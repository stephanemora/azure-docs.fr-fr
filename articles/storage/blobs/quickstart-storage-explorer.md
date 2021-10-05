---
title: 'Démarrage rapide : Créer un objet blob avec l’Explorateur Stockage Azure'
titleSuffix: Azure Storage
description: Découvrez comment utiliser l’Explorateur Stockage Azure pour créer un conteneur et un objet blob, télécharger l’objet blob sur votre ordinateur local et voir tous les objets blob du conteneur.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: tamram
ms.openlocfilehash: abf75bb39610deb54b72002cde3dd8e28131ed94
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838988"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>Démarrage rapide : Utiliser l’Explorateur Stockage Azure pour créer un objet blob

Dans le cadre de ce démarrage rapide, vous apprenez à utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour créer un conteneur et un objet blob. Vous apprenez ensuite à télécharger l’objet blob sur votre ordinateur local, et à afficher tous les objets blob dans un conteneur. Vous découvrez également comment créer une capture instantanée d’objet blob, gérer les stratégies d’accès de conteneur et créer une signature d’accès partagé.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Pour exécuter ce guide de démarrage rapide, vous devez installer l’Explorateur Stockage Azure. Pour installer l’Explorateur Stockage Azure pour Windows, Macintosh ou Linux, consultez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Se connecter à l’Explorateur Stockage

Au premier lancement, la fenêtre **Explorateur Stockage Microsoft Azure - Se connecter** s’affiche. L’Explorateur Stockage offre de nombreuses façons de se connecter à des comptes de stockage. Le tableau suivant répertorie les différentes méthodes de connexion :

|Tâche|Objectif|
|---|---|
|Ajouter un compte Azure | Vous redirige vers la page de connexion de votre organisation afin de vous authentifier sur Azure. |
|Utiliser un URI de chaîne de connexion ou de signature d’accès partagé | Peut être utilisé pour accéder directement à un conteneur ou un compte de stockage avec un jeton SAP ou une chaîne de connexion partagée. |
|Utiliser le nom et la clé d’un compte de stockage| Utilisez le nom et la clé et de votre compte de stockage pour vous connecter à Stockage Azure.|

Sélectionnez **Ajouter un compte Azure**, puis cliquez sur **Connexion..** . Suivez les instructions de connexion à votre compte Azure qui s’affichent à l’écran.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-connect.png" alt-text="Capture d’écran de la fenêtre de connexion de l’Explorateur Stockage Microsoft Azure":::

Une fois que l’Explorateur Stockage a fini de se connecter, il affiche l’onglet **Explorateur**. Cette vue vous donne des informations sur l’ensemble de vos comptes de stockage Azure, ainsi que sur le stockage local configuré via les comptes de l’[émulateur de stockage Azure](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) et [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), ou les environnements [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-main-page.png" alt-text="Capture d’écran montrant la page principale de l’Explorateur Stockage":::

## <a name="create-a-container"></a>Créer un conteneur

Pour créer un conteneur, développez le compte de stockage créé lors de l’étape précédente. Sélectionnez **Conteneurs d’objets blob**, cliquez avec le bouton droit, puis sélectionnez **Créer un conteneur d’objets blob**. Entrez le nom de votre conteneur d’objets blob. Consultez la section [Créer un conteneur](storage-quickstart-blobs-dotnet.md#create-a-container) pour obtenir la liste des règles et restrictions applicables aux noms de conteneurs d’objets blob. Lorsque vous avez terminé, appuyez sur **Entrée** pour créer le conteneur d’objets blob. Une fois le conteneur créé, il apparaît sous le dossier **Conteneurs d’objets blob** correspondant au compte de stockage sélectionné.

## <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les fichiers de disque dur virtuel utilisés pour appuyer les machines virtuelles IaaS sont des objets blob de pages. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des fichiers stockés dans Stockage Blob sont des objets blob de blocs.

Dans le ruban du conteneur, sélectionnez **Télécharger**. Ce faisant, vous avez la possibilité de télécharger un dossier ou un fichier.

Choisissez les fichiers ou le dossier à télécharger. Sélectionnez le **type d’objet blob**. Les choix acceptables sont **Ajout**, **Page** ou **Bloc**.

Si vous téléchargez un fichier .vhd or .vhdx, sélectionnez **Charger les fichiers .vhd/.vhdx en tant qu'objets blob de pages (recommandé)** .

Dans le champ **Charger dans le dossier** (facultatif), sélectionnez un nom de dossier dans lequel stocker les fichiers ou de sous-dossiers, sous le conteneur. Si aucun dossier n’est sélectionné, les fichiers sont chargés automatiquement dans le conteneur.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-upload-blob.png" alt-text="Explorateur Stockage Microsoft Azure - Charger un objet blob":::

Lorsque vous sélectionnez **OK**, les fichiers sélectionnés sont mis en file d’attente de téléchargement ; ils sont traités un à un. Une fois le téléchargement terminé, les résultats s’affichent dans la fenêtre **Activités**.

## <a name="view-blobs-in-a-container"></a>Afficher les objets blob d’un conteneur

Dans l’application **Explorateur Stockage Azure**, sélectionnez un conteneur sous un compte de stockage. Le volet principal affiche une liste des objets blobs hébergés dans le conteneur sélectionné.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-list-blobs.png" alt-text="Capture d’écran montrant comment sélectionner un conteneur dans l’Explorateur Stockage Microsoft Azure":::

## <a name="download-blobs"></a>Télécharger des objets blob

Pour télécharger les objets blob à l’aide de l’**Explorateur Stockage Azure**, avec un objet blob sélectionné, sélectionnez **Télécharger** dans le ruban. La boîte de dialogue qui s’ouvre vous permet de saisir un nom de fichier. Sélectionnez **Enregistrer** pour démarrer le téléchargement d’un objet blob sur l’emplacement local.

## <a name="manage-snapshots"></a>Gérer les instantanés

L’Explorateur Stockage Azure vous permet de prendre et de gérer des [instantanés](./snapshots-overview.md) de vos objets blob. Pour prendre un instantané d’un objet blob, cliquez avec le bouton droit sur l’objet blob, puis sélectionnez **Create Snapshot** (Créer un instantané). Pour afficher les instantanés d’un objet blob, cliquez avec le bouton droit sur l’objet blob, puis sélectionnez **Manage Snapshots** (Gérer les instantanés). Une liste des instantanés associés à l’objet blob s’affiche dans l’onglet actuel.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-snapshots.png" alt-text="Capture d’écran montrant comment prendre et gérer des instantanés d’objet blob":::

## <a name="generate-a-shared-access-signature"></a>Générer une signature d’accès partagé

Vous pouvez utiliser l’Explorateur Stockage pour générer des signatures d’accès partagé (SAS). Cliquez sur un compte de stockage, sur un conteneur ou sur un objet blob, puis sélectionnez **Obtenir une signature d’accès partagé...** . Sélectionnez l’heure de départ et d’expiration, ainsi que les autorisations pour l’URL de la stratégie d’accès partagé, puis sélectionnez **Créer**. L’Explorateur Stockage génère le jeton SAS avec les paramètres que vous avez spécifiés et l’affiche en vue de sa copie.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-shared-access-signature.png" alt-text="Capture d’écran montrant comment générer une signature d’accès partagé":::

Lorsque vous créez une signature SAS pour un compte de stockage, l’Explorateur Stockage génère une signature SAS de compte. Pour plus d’informations sur les signatures SAS de compte, consultez [Créer une signature SAS de compte](/rest/api/storageservices/create-account-sas).

Lorsque vous créez une signature SAS pour un conteneur ou un objet blob, l’Explorateur Stockage génère une signature SAS de service. Pour plus d’informations sur les signatures SAS de service, consultez [Créer une signature SAS de service](/rest/api/storageservices/create-service-sas).

> [!NOTE]
> Lorsque vous créez une signature SAS avec l’Explorateur Stockage, la signature SAS est toujours affectée avec la clé de compte de stockage. L’Explorateur Stockage ne prend pas actuellement en charge la création d’une signature SAS de délégation d’utilisateur, qui est une signature SAS signée avec des informations d’identification Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et le stockage Blob Azure à l’aide de l’**Explorateur Stockage Azure**. Pour en savoir plus sur l’utilisation du stockage Blob, consultez la vue d’ensemble du stockage Blob.

> [!div class="nextstepaction"]
> [Présentation de Stockage Blob Azure](./storage-blobs-introduction.md)