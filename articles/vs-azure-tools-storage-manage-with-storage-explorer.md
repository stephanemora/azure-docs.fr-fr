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
ms.openlocfilehash: 2b923a381be9d6cdb1a83f582a541c9e17e117a4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086630"
---
# <a name="get-started-with-storage-explorer"></a>Prise en main de l’Explorateur Stockage

## <a name="overview"></a>Vue d'ensemble

L’Explorateur Stockage Microsoft Azure est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux. Dans cet article, vous allez découvrir différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

![Explorateur Stockage Microsoft Azure][0]

## <a name="prerequisites"></a>Prérequis

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

L’Explorateur Stockage est pris en charge sur les versions suivantes de Windows :

* Windows 10 (recommandé)
* Windows 8
* Windows 7

Pour toutes les versions de Windows, le .NET Framework 4.6.2 ou ultérieur est obligatoire.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

L’Explorateur Stockage est pris en charge sur les versions suivantes de macOS :

* macOS 10.12 « Sierra » et versions ultérieures

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

L’Explorateur Stockage est disponible dans le [Snap Store](https://snapcraft.io/storage-explorer) pour les distributions de Linux les plus courantes. Il s’agit de la méthode d’installation recommandée. Le snap Explorateur Stockage installe automatiquement toutes ses dépendances et mises à jour quand de nouvelles versions sont publiées sur le Snap Store.

Pour obtenir la liste des distributions prises en charge, accédez à la [page d’installation de snapd](https://snapcraft.io/docs/installing-snapd).

L’Explorateur Stockage nécessite l’utilisation d’un gestionnaire de mots de passe que vous devrez peut-être connecter manuellement. Vous pouvez connecter l’Explorateur Stockage au gestionnaire de mots de passe de votre système en exécutant la commande suivante :

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

L’Explorateur Stockage est également disponible sous la forme d’un téléchargement .tar.gz, mais vous devrez installer les dépendances manuellement. L’installation .tar.gz est prise en charge sur les distributions suivantes de Linux :

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

L’installation .tar.gz peut fonctionner sur d’autres distributions, mais seules celles listées sont officiellement prises en charge.

Pour obtenir davantage d’aide sur l’installation de l’Explorateur Stockage sur Linux, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Télécharger et installer

[Télécharger et installer l’Explorateur Stockage](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Connexion à un service ou un compte de stockage

L’Explorateur Stockage offre de nombreuses façons de se connecter à des comptes de stockage. En règle générale, vous pouvez :

* [Vous connecter à Azure pour accéder à vos abonnements et à leurs ressources](#sign-in-to-azure)
* [Lier une ressource particulière CosmosDB ou de stockage](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Connexion à Azure

> [!NOTE]
> Pour accéder pleinement aux ressources après la connexion, l’Explorateur Stockage nécessite des autorisations de gestion (Azure Resource Manager) et de couche de données. Cela signifie que vous devez disposer d’autorisations Azure Active Directory (Azure AD) qui vous donnent accès à votre compte de stockage, aux conteneurs du compte et aux données dans les conteneurs. Si vous disposez uniquement d’autorisations au niveau de la couche de données, envisagez d’[ajouter une ressource par le biais d’Azure AD](#add-a-resource-via-azure-ad). Pour plus d’informations sur les autorisations spécifiques dont l’Explorateur Stockage a besoin, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).

1. Dans l’Explorateur Stockage, sélectionnez **Gérer les comptes** pour accéder au panneau de gestion des comptes.

    ![Gérer les comptes][1]

2. Le volet gauche affiche tous les comptes Azure auxquels vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte**.

3. Si vous souhaitez vous connecter à un cloud national ou une pile Azure Stack, sélectionnez la liste déroulante **Environnement Azure** pour choisir le cloud Azure à utiliser. Après avoir choisi votre environnement, sélectionnez le bouton **Connexion**. Pour plus d’informations, consultez [Connecter l’Explorateur Stockage à un abonnement Azure Stack](/azure-stack/user/azure-stack-storage-connect-se).

    ![Option de connexion][2]

4. Une fois que vous êtes connecté avec un compte Azure, le volet gauche est renseigné avec le compte et les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure listés.)

    ![Sélectionner les abonnements Azure][3]

    Le volet gauche affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

    ![Abonnements Azure sélectionnés][4]

### <a name="attach-a-specific-resource"></a>Lier une ressource spécifique

Vous pouvez effectuer l’attachement à une ressource dans l’Explorateur Stockage de plusieurs façons :

* [Ajouter une ressource via Azure AD](#add-a-resource-via-azure-ad). Si vous disposez uniquement d’autorisations au niveau de la couche de données, utilisez cette option pour ajouter un conteneur d’objets blob ou un conteneur de stockage d’objets blob Azure Data Lake Storage Gen2.
* [Utiliser une chaîne de connexion](#use-a-connection-string). Utilisez cette option si vous disposez d’une chaîne de connexion à un compte de stockage. L’Explorateur Stockage prend en charge les deux chaînes de connexion, de clé et [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md).
* [Utiliser un URI SAP](#use-a-sas-uri). Si vous disposez d’un [URI SAP](storage/common/storage-dotnet-shared-access-signature-part-1.md) à un conteneur d’objets blob, un partage de fichiers, une file d’attente ou une table, utilisez-le pour effectuer l’attachement à la ressource. Pour obtenir un URI SAS, vous pouvez utiliser l’[Explorateur de stockage](#generate-a-sas-in-storage-explorer) ou le [portail Azure](https://portal.azure.com).
* [Utiliser un nom et une clé](#use-a-name-and-key). Si vous connaissez l’une ou l’autre des clés de compte de votre compte de stockage, vous pouvez utiliser cette option pour vous connecter rapidement. Les clés de votre compte de stockage se trouvent dans le panneau **Clés d’accès** du compte de stockage dans le [Portail Azure](https://portal.azure.com).
* [Attacher à un émulateur local](#attach-to-a-local-emulator) Si vous utilisez un des émulateurs disponibles du stockage Azure, utilisez cette option pour vous connecter facilement à votre émulateur.
* [Se connecter à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Utilisez cette option si vous disposez d’une chaîne de connexion à une instance CosmosDB.
* [Se connecter à Azure Data Lake Store par URI](#connect-to-azure-data-lake-store-by-uri). Utilisez cette option si vous disposez d’un URI à Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Ajouter une ressource via Azure AD

1. Ouvrez la boîte de dialogue **Connexion** en sélectionnant le bouton **Se connecter**, à gauche dans la barre d’outils verticale :

    ![Option Se connecter à Azure Storage][9]

2. Si ce n’est pas déjà fait, utilisez l’option **Ajouter un compte Azure** pour vous connecter au compte Azure ayant accès à la ressource. Après vous être connecté, revenez à la boîte de dialogue **Connexion**.

3. Sélectionnez **Ajouter une ressource via Azure Active Directory (Azure AD)** , puis **Suivant**.

4. Sélectionnez le compte Azure et le locataire ayant accès à la ressource de stockage à laquelle vous souhaitez vous attacher. Sélectionnez **Suivant**.

5. Choisissez le type de ressource que vous voulez lier, puis entrez les informations nécessaires à la connexion. Les informations que vous entrez dans cette page dépendent du type de ressource que vous ajoutez. Veillez à choisir le type de ressource correct. Une fois que vous avez entré toutes les informations obligatoires, sélectionnez **Suivant**.

6. Passez en revue le récapitulatif des informations de connexion et vérifiez que tout est correct. Si c’est le cas, sélectionnez **Se connecter**. Sinon, utilisez le bouton **Précédent** pour revenir aux pages précédentes et corriger les informations incorrectes.

Une fois la connexion ajoutée, l’arborescence de ressources accède automatiquement au nœud représentant la connexion. Si elle n’y accède pas, regardez sous **Local et attaché** > **Comptes de stockage** >  **(Conteneurs attachés)**  > **Conteneurs d’objets blob**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion ou que vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Utiliser une chaîne de connexion

1. Ouvrez la boîte de dialogue **Connexion** en sélectionnant le bouton **Se connecter**, à gauche dans la barre d’outils verticale :

    ![Option Se connecter à Azure Storage][9]

2. Sélectionnez **Utiliser une chaîne de connexion**, puis **Suivant**.

3. Choisissez un nom d’affichage pour votre connexion et entrez votre chaîne de connexion. Ensuite, sélectionnez **Suivant**.

4. Passez en revue le récapitulatif des informations de connexion et vérifiez que tout est correct. Si c’est le cas, sélectionnez **Se connecter**. Sinon, utilisez le bouton **Précédent** pour revenir aux pages précédentes et corriger les informations incorrectes.

Une fois la connexion ajoutée, l’arborescence de ressources accède automatiquement au nœud représentant la connexion. Si elle n’y accède pas, regardez sous **Local et attaché** > **Comptes de stockage**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion ou que vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-sas-uri"></a>Utiliser un URI SAP

1. Ouvrez la boîte de dialogue **Connexion** en sélectionnant le bouton **Se connecter**, à gauche dans la barre d’outils verticale :

    ![Option Se connecter à Azure Storage][9]

2. Sélectionnez **Utiliser un URI SAP (signature d’accès partagé)** , puis sélectionnez **Suivant**.

3. Choisissez un nom d’affichage pour votre connexion et entrez votre URI SAP. Le point de terminaison de service pour le type de ressource que vous attachez doit se renseigner automatiquement. Si vous utilisez un point de terminaison personnalisé, il est possible que ce ne soit pas le cas. Sélectionnez **Suivant**.

4. Passez en revue le récapitulatif des informations de connexion et vérifiez que tout est correct. Si c’est le cas, sélectionnez **Se connecter**. Sinon, utilisez le bouton **Précédent** pour revenir aux pages précédentes et corriger les informations incorrectes.

Une fois la connexion ajoutée, l’arborescence de ressources accède automatiquement au nœud représentant la connexion. Si elle n’y accède pas, regardez sous **Local et attaché** > **Comptes de stockage** >  **(Conteneurs attachés)**  > *nœud de service pour le type de conteneur que vous avez attaché*. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion ou que vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-name-and-key"></a>Utiliser un nom et une clé

1. Ouvrez la boîte de dialogue **Connexion** en sélectionnant le bouton **Se connecter**, à gauche dans la barre d’outils verticale :

    ![Option Se connecter à Azure Storage][9]

2. Sélectionnez **Utiliser le nom et la clé d’un compte de stockage**, puis **Suivant**.

3. Choisissez un nom d’affichage pour votre connexion.

4. Entrez le nom du compte de stockage et une de ses clés d’accès.

5. Choisissez le **Domaine de stockage** à utiliser, puis sélectionnez **Suivant**.

6. Passez en revue le récapitulatif des informations de connexion et vérifiez que tout est correct. Si c’est le cas, sélectionnez **Se connecter**. Sinon, utilisez le bouton **Précédent** pour revenir aux pages précédentes et corriger les informations incorrectes.

Une fois la connexion ajoutée, l’arborescence de ressources accède automatiquement au nœud représentant la connexion. Si elle n’y accède pas, regardez sous **Local et attaché** > **Comptes de stockage**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion ou que vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Lier à un émulateur local

L’Explorateur Stockage prend actuellement en charge deux émulateurs de stockage officiels :
* [Émulateur de stockage Azure](storage/common/storage-use-emulator.md) (Windows uniquement)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Si votre émulateur est à l’écoute sur les ports par défaut, vous pouvez utiliser le nœud **Émulateur - Ports par défaut** (qui se trouve sous **Local et attaché** > **Comptes de stockage**) pour accéder rapidement à votre émulateur.

Si vous souhaitez utiliser un autre nom pour votre connexion, ou si votre émulateur ne s’exécute pas sur les ports par défaut, effectuez les étapes suivantes :

1. Démarrez votre émulateur. Ce faisant, notez les ports sur lesquels l’émulateur est à l’écoute pour chaque type de service.

   > [!IMPORTANT]
   > L’Explorateur Stockage ne démarre pas automatiquement votre émulateur. Vous devez le démarrer manuellement.

2. Ouvrez la boîte de dialogue **Connexion** en sélectionnant le bouton **Se connecter**, à gauche dans la barre d’outils verticale :

    ![Option Se connecter à Azure Storage][9]

3. Sélectionnez **Attacher à un émulateur local**, puis sélectionnez **Suivant**.

4. Choisissez un nom d’affichage pour votre connexion et indiquez les ports sur lesquels votre émulateur est à l’écoute pour chaque type de service. Les zones de texte commencent par les valeurs de port par défaut pour la plupart des émulateurs. La zone **Port des fichiers** est laissée vide, car aucun des émulateurs officiels ne prend actuellement en charge le service Files. Si l’émulateur que vous utilisez ne prend pas en charge Files, vous pouvez entrer le port utilisé. Ensuite, sélectionnez **Suivant**.

5. Passez en revue le récapitulatif des informations de connexion et vérifiez que tout est correct. Si c’est le cas, sélectionnez **Se connecter**. Sinon, utilisez le bouton **Précédent** pour revenir aux pages précédentes et corriger les informations incorrectes.

Une fois la connexion ajoutée, l’arborescence de ressources accède automatiquement au nœud représentant la connexion. Si elle n’y accède pas, regardez sous **Local et attaché** > **Comptes de stockage**. Si l’Explorateur Stockage n’a pas pu ajouter votre connexion ou que vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [guide de résolution des problèmes](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connexion à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion

Au lieu de gérer des comptes Azure Cosmos DB par le biais d’un abonnement Azure, vous pouvez également vous connecter à Azure Cosmos DB en utilisant une chaîne de connexion. Pour ce faire, procédez comme suit :

1. À gauche de l’arborescence des ressources, développez **Local et attaché**, cliquez avec le bouton droit sur **Comptes Azure Cosmos DB** et sélectionnez **Se connecter à Azure Cosmos DB**

    ![Se connecter à Azure Cosmos DB avec une chaîne de connexion][21]

2. Sélectionnez l’API Azure Cosmos DB, entrez vos données **Chaîne de connexion**, puis sélectionnez **OK** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion, consultez [Obtenir la chaîne de connexion](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Chaîne de connexion][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Se connecter à Azure Data Lake Store par URI

Si vous souhaitez accéder à une ressource qui n’est pas dans votre abonnement, demandez à quelqu’un ayant accès à cette ressource de vous fournir son URI. Une fois connecté, vous pouvez vous connecter à Data Lake Store à l’aide de l’URI. Pour ce faire, procédez comme suit :

1. Ouvrez l’Explorateur de stockage.
2. Dans le volet gauche, développez **Local and Attached** (Local et attaché).
3. Cliquez avec le bouton droit sur **Data Lake Store**. Dans le menu contextuel, sélectionnez **Se connecter à Data Lake Store**.

    ![Menu contextuel Se connecter à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Entrez l’URI. L’outil accède à l’emplacement de l’URL que vous venez d’entrer.

    ![Boîte de dialogue contextuelle de connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Résultat de la connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Générer une SAP dans l’Explorateur Stockage

### <a name="account-level-sas"></a>SAP au niveau du compte

1. Cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager, puis sélectionnez **Obtenir une signature d’accès partagé**.

    ![Option de menu contextuel Obtenir une signature d’accès partagé][14]

2. Dans la boîte de dialogue **Générer une signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis sélectionnez **Créer**.

    ![Boîte de dialogue Obtenir une signature d’accès partagé][15]

3. Vous pouvez maintenant copier la **Chaîne de connexion**, ou la **Chaîne de requête** brute dans votre Presse-papiers.

### <a name="service-level-sas"></a>SAP au niveau du service

[Comment obtenir une SAP pour un conteneur d’objets blob dans l’Explorateur Stockage](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Recherche de comptes de stockage

Si vous avez besoin de rechercher une ressource de stockage et ne savez pas où elle se trouve, vous pouvez utiliser la zone de recherche en haut du volet gauche pour rechercher la ressource.

À mesure que vous tapez dans la zone de recherche, le volet gauche affiche toutes les ressources qui correspondent à la valeur en cours de saisie. Par exemple, la recherche de **points de terminaison** est indiquée dans la capture d’écran suivante :

![Recherche de compte de stockage][23]

> [!NOTE]
> Pour accélérer votre recherche, utilisez le panneau de gestion des comptes afin de désélectionner tous les abonnements qui ne contiennent pas l’élément que vous recherchez. Vous pouvez également cliquer avec le bouton droit sur un nœud et sélectionner **Rechercher à partir d’ici** pour démarrer la recherche à partir d’un nœud spécifique.
>
>

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les ressources de Stockage Blob Azure avec l’Explorateur Stockage](vs-azure-tools-storage-explorer-blobs.md)
* [Gérer Azure Cosmos DB dans l’Explorateur Stockage (préversion)](./cosmos-db/storage-explorer.md)
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
