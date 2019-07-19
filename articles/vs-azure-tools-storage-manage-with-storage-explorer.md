---
title: Prise en main Prise en main de l’Explorateur de stockage | Microsoft Docs
description: Gérez les ressources Stockage Azure avec l’Explorateur Stockage
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508737"
---
# <a name="get-started-with-storage-explorer"></a>Prise en main de l’Explorateur Stockage

## <a name="overview"></a>Vue d’ensemble

L’Explorateur Stockage Azure est une application autonome qui vous permet d’utiliser facilement les données de stockage Azure sur Windows, macOS et Linux. Dans cet article, vous découvrez différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

![Explorateur Stockage Microsoft Azure][0]

## <a name="prerequisites"></a>Prérequis

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

L’Explorateur Stockage Azure est pris en charge sur les versions suivantes de Windows :

* Windows 10 (recommandé)
* Windows 8
* Windows 7

Pour toutes les versions de Windows, .NET Framework 4.6.2 ou version ultérieure est requis.

[Télécharger et installer l’Explorateur Stockage](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

L’Explorateur Stockage Azure est pris en charge sur les versions suivantes de macOS :

* macOS 10.12 « Sierra » et versions ultérieures

[Télécharger et installer l’Explorateur Stockage](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

L’Explorateur Stockage Azure est pris en charge sur les distributions suivantes de Linux :

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

L’Explorateur Stockage Azure peut fonctionner d’autres distributions, mais seules celles répertoriés ci-dessus sont officiellement prises en charge.

Pour plus d’aide sur l’installation de l’Explorateur Stockage sur Linux, consultez le [Guide de dépannage](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Les [Notes de publication](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) de l’Explorateur Stockage Azure contiennent des étapes propres à certaines distributions.

[Télécharger et installer l’Explorateur Stockage](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Connexion à un service ou un compte de stockage

L’Explorateur Stockage offre de nombreuses façons de se connecter à des comptes de stockage. En règle générale, vous pouvez :

* [Vous connecter à Azure pour accéder à vos abonnements et à leurs ressources](#sign-in-to-azure)
* [Lier une ressource particulière CosmosDB ou de stockage](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Connexion à Azure

> [!NOTE]
> Pour accéder pleinement aux ressources après la connexion, l’Explorateur Stockage a besoin d’une gestion (ARM) et d’autorisations de la couche de données. Cela signifie que vous devez disposer d’autorisations Azure AD qui vous donne accès à votre compte de stockage, aux conteneurs du compte et aux données dans les conteneurs. Si vous disposez uniquement d’autorisations au niveau de la couche de données, pensez plutôt à utiliser [Lier à Azure AD](#add-a-resource-via-azure-ad). Pour plus d’informations sur les autorisations précises dont l’Explorateur Stockage a besoin, consultez le [Guide de résolution des problèmes :](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. Dans l’Explorateur Stockage, sélectionnez **Gérer les comptes** pour accéder au **Panneau de gestion des comptes**.

    ![Gérer les comptes][1]

2. Le volet gauche affiche tous les comptes Azure auxquels vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte**

3. Si vous souhaitez vous connecter à un cloud national ou une pile Azure Stack, cliquez sur la liste déroulante **Environnement Azure** pour sélectionner le cloud Azure à utiliser. Lorsque vous avez choisi votre environnement, cliquez sur le bouton **Se connecter...** . Si vous vous connectez à Azure Stack, consultez [Connecter l’Explorateur Stockage à un abonnement Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) pour plus d’informations.

    ![Option de connexion][2]

4. Une fois que vous êtes connecté avec un compte Azure, le volet gauche est renseigné avec le compte et les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer** (sélectionnez **Tous les abonnements :** pour sélectionner/désélectionner la totalité ou aucun des abonnements Azure répertoriés).

    ![Sélectionner les abonnements Azure][3]

    Le volet gauche affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

    ![Abonnements Azure sélectionnés][4]

### <a name="attach-a-specific-resource"></a>Lier une ressource spécifique
    
Il existe une variété d’options permettant de lier une ressource à l’Explorateur Stockage. Vous pouvez :

* [Ajouter une ressource via Azure AD](#add-a-resource-via-azure-ad). Si vous disposez uniquement d’autorisations au niveau de la couche de données, vous pouvez alors utiliser cette option pour ajouter un conteneur d’objets blob ou un conteneur d’objets blob ADLS Gen2.
* [Utiliser une chaîne de connexion](#use-a-connection-string). Si vous disposez d’une chaîne de connexion à un compte de stockage. L’Explorateur Stockage prend en charge les deux chaînes de connexion, de clé et [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md).
* [Utiliser un URI SAP](#use-a-sas-uri). Si vous disposez d’un URI [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) à un conteneur d’objets blob, un partage de fichiers, une file d’attente ou une table. Pour obtenir un URI SAS, vous pouvez utiliser l’[Explorateur de stockage](#generate-a-sas-in-storage-explorer) ou le [portail Azure](https://portal.azure.com).
* [Utiliser un nom et une clé](#use-a-name-and-key). Si vous connaissez l’une ou l’autre des clés de compte de votre compte de stockage, vous pouvez utiliser cette option pour vous connecter rapidement. Les clés de votre compte de stockage se trouvent dans le panneau **Clés d’accès** du compte de stockage dans le [portail Azure](https://portal.azure.com).
* [Lier à un émulateur local](#attach-to-a-local-emulator). Si vous utilisez un des émulateurs disponibles du stockage Azure, utilisez cette option pour vous connecter facilement à votre émulateur.
* [Vous connecter à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Si vous disposez d’une chaîne de connexion à une instance CosmosDB.
* [Se connecter à Azure Data Lake Store par URI](#connect-to-azure-data-lake-store-by-uri). Si vous disposez d’un URI à Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Ajouter une ressource via Azure AD

1. Ouvrez la **boîte de dialogue de connexion** en cliquant sur le **bouton Se connecter**, à gauche dans la barre d’outils verticale.

    ![Option Se connecter à Azure Storage][9]

2. Si ce n’est pas déjà fait, utilisez l’option **Ajouter un compte Azure** pour vous connecter au compte Azure qui a accès à la ressource. Après vous être connecté, revenez à la **boîte de dialogue de connexion**.

3. Sélectionnez l’option **Ajouter une ressource via Azure Active Directory (Azure AD)** et cliquez sur **Suivant**.

4. Sélectionnez le compte Azure qui a accès à la ressource de stockage que vous souhaitez lier, et l’abonnement dans lequel se trouve la ressource, puis cliquez **Suivant**.

5. Choisissez le type de ressource que vous voulez lier, puis entrez les informations nécessaires à la connexion. Les entrées sur cette page varient selon le type de ressource que vous ajoutez. Veillez à choisir le type de ressource correct. Après avoir indiqué les informations utiles, cliquez sur **Suivant**.

6. Contrôlez le récapitulatif des informations de connexion et assurez-vous que tout est correct. Si tous les renseignements semblent exacts, cliquez sur **Se connecter**, sinon retournez dans les pages précédentes à l’aide du bouton **Précédent** pour corriger les informations erronées.

Une fois la connexion ajoutée avec succès, l’arborescence de la ressource accède automatiquement au nœud représentant la connexion. Si pour une raison quelconque, elle n’y accède pas, regardez sous **Local et attaché** → **Comptes de stockage** → **(Conteneurs attachés)** → **Conteneurs d’objets blob**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion, ou si vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [Guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) pour obtenir de l’aide.

#### <a name="use-a-connection-string"></a>Utiliser une chaîne de connexion

1. Ouvrez la **boîte de dialogue de connexion** en cliquant sur le **bouton Se connecter**, à gauche dans la barre d’outils verticale.

    ![Option Se connecter à Azure Storage][9]

2. Sélectionnez l’option **Utiliser une chaîne de connexion** et cliquez sur **Suivant**.

3. Choisissez un nom d’affichage pour votre connexion et entrez votre chaîne de connexion. Cliquez ensuite sur **Suivant**.

4. Contrôlez le récapitulatif des informations de connexion et assurez-vous que tout est correct. Si tous les renseignements semblent exacts, cliquez sur **Se connecter**, sinon retournez dans les pages précédentes à l’aide du bouton **Précédent** pour corriger les informations erronées.

Une fois la connexion ajoutée avec succès, l’arborescence de la ressource accède automatiquement au nœud représentant la connexion. Si pour une raison quelconque, elle n’y accède pas, regardez sous **Local et attaché** → **Comptes de stockage**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion, ou si vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [Guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) pour obtenir de l’aide.

#### <a name="use-a-sas-uri"></a>Utiliser un URI SAP

1. Ouvrez la **boîte de dialogue de connexion** en cliquant sur le **bouton Se connecter**, à gauche dans la barre d’outils verticale.

    ![Option Se connecter à Azure Storage][9]

2. Sélectionnez l’option **Utiliser un URI de signature d’accès partagé (SAP)** , puis cliquez sur **suivant**.

3. Choisissez un nom d’affichage pour votre connexion et entrez votre URI SAS. Le point de terminaison de service pour le type de ressource que vous liez doit se renseigner automatiquement. Si vous utilisez un point de terminaison personnalisé, ce peut ne pas être le cas. Cliquez sur **Suivant**.

4. Contrôlez le récapitulatif des informations de connexion et assurez-vous que tout est correct. Si tous les renseignements semblent exacts, cliquez sur **Se connecter**, sinon retournez dans les pages précédentes à l’aide du bouton **Précédent** pour corriger les informations erronées.

Une fois la connexion ajoutée avec succès, l’arborescence de la ressource accède automatiquement au nœud représentant la connexion. Si pour une raison quelconque, elle n’y accède pas, regardez sous **Local et attaché** → **Comptes de stockage** → **(Conteneurs attachés)** → **le nœud de service pour le type de conteneur que vous avez attaché**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion, ou si vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [Guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) pour obtenir de l’aide.

#### <a name="use-a-name-and-key"></a>Utiliser un nom et une clé

1. Ouvrez la **boîte de dialogue de connexion** en cliquant sur le **bouton Se connecter**, à gauche dans la barre d’outils verticale.

    ![Option Se connecter à Azure Storage][9]

2. Sélectionnez l’option **Utiliser le nom et la clé d’un compte de stockage**, puis cliquez sur **Suivant**.

3. Choisissez un nom d’affichage pour votre connexion.

4. Entrez le nom du compte de stockage et une de ses clés d’accès.

5. Choisissez le **Domaine de stockage** à utiliser, puis cliquez sur **Suivant**.

4. Contrôlez le récapitulatif des informations de connexion et assurez-vous que tout est correct. Si tous les renseignements semblent exacts, cliquez sur **Se connecter**, sinon retournez dans les pages précédentes à l’aide du bouton **Précédent** pour corriger les informations erronées.

Une fois la connexion ajoutée avec succès, l’arborescence de la ressource accède automatiquement au nœud représentant la connexion. Si pour une raison quelconque, elle n’y accède pas, regardez sous **Local et attaché** → **Comptes de stockage**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion, ou si vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [Guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) pour obtenir de l’aide.

#### <a name="attach-to-a-local-emulator"></a>Lier à un émulateur local

L’Explorateur Stockage prend en charge les émulateurs sur toutes les plateformes. Les deux émulateurs officiels actuellement disponibles sont :
* [Émulateur de stockage Azure](storage/common/storage-use-emulator.md) (Windows uniquement)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Si votre émulateur est en cours d’exécution sur les ports par défaut, vous pouvez utiliser le nœud **Émulateur - Ports par défaut**, qui se trouve toujours sous **Local et attaché** → **Comptes de stockage**, pour accéder rapidement à votre émulateur. Si vous souhaitez utiliser un autre nom pour votre connexion, ou si votre émulateur ne s’exécute pas sur les ports par défaut, suivez les étapes ci-dessous.

1. Démarrez votre émulateur. Ce faisant, notez les ports sur lesquels l’émulateur est à l’écoute pour chaque type de service. Ces informations vous seront utiles plus tard.

   > [!IMPORTANT]
   > L’Explorateur Stockage ne démarre pas automatiquement votre émulateur. Vous devez le démarrer vous-même.

2. Ouvrez la **boîte de dialogue de connexion** en cliquant sur le **bouton Se connecter**, à gauche dans la barre d’outils verticale.

    ![Option Se connecter à Azure Storage][9]

3. Sélectionnez l’option **Attacher à un émulateur local**, puis cliquez sur **Suivant**.

4. Choisissez un nom d’affichage pour votre connexion et indiquez les ports sur lesquels votre émulateur écoute pour chaque type de service. Par défaut, les zones de texte contiennent les valeurs de port par défaut pour la plupart des émulateurs. Le **port Files** est, par défaut, également laissé vide, car aucun des émulateurs officiels ne prend actuellement en charge le service Files. Si l’émulateur que vous utilisez le prend en charge malgré tout, vous pouvez entrer le port qui est utilisé. Cliquez sur **Suivant**.

5. Contrôlez le récapitulatif des informations de connexion et assurez-vous que tout est correct. Si tous les renseignements semblent exacts, cliquez sur **Se connecter**, sinon retournez dans les pages précédentes à l’aide du bouton **Précédent** pour corriger les informations erronées.

Une fois la connexion ajoutée avec succès, l’arborescence de la ressource accède automatiquement au nœud représentant la connexion. Si pour une raison quelconque, elle n’y accède pas, regardez sous **Local et attaché** → **Comptes de stockage**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion, ou si vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [Guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) pour obtenir de l’aide.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connexion à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion

En plus de gérer des comptes Azure Cosmos DB par le biais d’un abonnement Azure, une autre façon de se connecter à un compte Azure Cosmos DB consiste à utiliser une chaîne de connexion. Utilisez les étapes suivantes pour vous connecter à l’aide d’une chaîne de connexion.

1. Rechercher **Locaux et joints** dans l’arborescence de gauche, cliquez avec le bouton droit sur **Comptes Azure Cosmos DB**, choisissez **Se connecter à Azure Cosmos DB...**

    ![connexion à Azure Cosmos DB avec une chaîne de connexion][21]

2. Choisissez l’API Azure Cosmos DB, collez votre **chaîne de connexion**, puis cliquez sur **OK** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion, consultez [Obtenir la chaîne de connexion](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Se connecter à Azure Data Lake Store par URI

Vous souhaitez accéder à des ressources, qui n’existent pas dans votre abonnement, mais d’autres personnes vous permettent d’accéder à l’URI de ces ressources. Le cas échéant, vous pouvez vous connecter à Data Lake Store à l’aide de l’URI une fois que vous êtes connecté. Procédez comme suit.

1. Ouvrez l’Explorateur de stockage.
2. Dans le volet gauche, développez **Local and Attached** (Local et attaché).
3. Cliquez avec le bouton droit sur **Data Lake Store**, puis, dans le menu contextuel, sélectionnez **Connect to Data Lake Store...** (Se connecter à Data Lake Store...).

    ![menu contextuel de connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Entrez l’URI. L’outil accède alors à l’emplacement de l’URL que vous venez d’entrer.

    ![boîte de dialogue contextuelle de connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![résultat de la connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Générer une SAP dans l’Explorateur Stockage

### <a name="account-level-sas"></a>SAP au niveau du compte

1. Cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager, puis sélectionnez **Obtenir une signature d’accès partagé...** .

    ![Option de menu contextuel Obtenir une signature d’accès partagé][14]

2. Dans la boîte de dialogue **Générer la signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis cliquez sur le bouton **Créer**.

    ![Boîte de dialogue Obtenir une signature d’accès partagé][15]

3. Vous pouvez maintenant copier la **Chaîne de connexion**, ou la **Chaîne de requête** brute dans votre Presse-papiers.

### <a name="service-level-sas"></a>SAP au niveau du service

[Comment obtenir une SAP pour un conteneur d’objets blob dans l’Explorateur Stockage](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Recherche de comptes de stockage

Si vous avez besoin de rechercher une ressource de stockage et ne savez pas où elle se trouve, vous pouvez utiliser la zone de recherche en haut du volet gauche pour rechercher la ressource.

À mesure que vous tapez dans la zone de recherche, le volet gauche affiche toutes les ressources qui correspondent à la valeur en cours de saisie. Par exemple, la recherche de **points de terminaison** est indiquée dans la capture d’écran suivante :

![Recherche de compte de stockage][23]

> [!NOTE]
> Utilisez le **panneau de gestion des comptes** pour désélectionner tous les abonnements qui ne contiennent pas l’élément que vous recherchez afin d’améliorer le temps d’exécution de la recherche. Vous pouvez également cliquer avec le bouton droit sur un nœud et choisir **Rechercher à partir d’ici** pour démarrer la recherche à partir d’un nœud spécifique.
>
>

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les ressources de Stockage Blob Azure avec l’Explorateur Stockage](vs-azure-tools-storage-explorer-blobs.md)
* [Gérer Azure Cosmos DB dans l’Explorateur Stockage Azure (préversion)](./cosmos-db/storage-explorer.md)
* [Gérer les ressources Azure Data Lake Store avec l’Explorateur Stockage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
