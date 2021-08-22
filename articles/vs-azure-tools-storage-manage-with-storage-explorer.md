---
title: Prise en main Prise en main de l’Explorateur de stockage | Microsoft Docs
description: Commencez à gérer les ressources de stockage Azure avec Explorateur Stockage. Téléchargez et installez Explorateur Stockage Azure, connectez-vous à un compte de stockage ou à un service, et bien plus encore.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 969e2d2424b9f9af1dcd921a1cebfa9407e313eb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438381"
---
# <a name="get-started-with-storage-explorer"></a>Prise en main de l’Explorateur Stockage

## <a name="overview"></a>Vue d’ensemble

L’Explorateur Stockage Microsoft Azure est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux.

Dans cet article, vous allez découvrir différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

:::image type="content" alt-text="Explorateur Stockage Microsoft Azure" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Prérequis

# <a name="windows"></a>[Windows](#tab/windows)

L’Explorateur Stockage est pris en charge sur les versions suivantes de Windows :

* Windows 10 (recommandé)
* Windows 8
* Windows 7

Pour toutes les versions de Windows, l’Explorateur Stockage nécessite au minimum .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

L’Explorateur Stockage est pris en charge sur les versions suivantes de macOS :

* macOS 10.12 Sierra et versions ultérieures

# <a name="linux"></a>[Linux](#tab/linux)

L’Explorateur Stockage est disponible dans le [Snap Store](https://snapcraft.io/storage-explorer) pour les distributions de Linux les plus courantes. Il s’agit de la méthode d’installation recommandée. Le snap Explorateur Stockage installe toutes ses dépendances et mises à jour quand de nouvelles versions sont publiées sur le Snap Store.

Pour obtenir la liste des distributions prises en charge, consultez la [page d’installation de `snapd`](https://snapcraft.io/docs/installing-snapd).

Vous devez utiliser un gestionnaire de mots de passe avec l’Explorateur Stockage. Vous devrez peut-être vous connecter manuellement à un gestionnaire de mots de passe. Vous pouvez connecter l’Explorateur Stockage au gestionnaire de mots de passe de votre système en exécutant la commande suivante :

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Vous pouvez aussi obtenir l’Explorateur Stockage en téléchargeant un fichier au format *.tar.gz*. Si vous utilisez le fichier *.tar.gz*, vous devez installer les dépendances manuellement. L’installation à l’aide du fichier *.tar.gz* est prise en charge sur les distributions suivantes de Linux :

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

L’installation à l’aide du fichier *.tar.gz* peut fonctionner sur d’autres distributions, mais seules celles listées sont officiellement prises en charge.

Pour plus d’informations sur l’installation de l’Explorateur Stockage sur Linux, consultez la section [Dépendances Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) dans le guide de résolution des problèmes de l’Explorateur Stockage Azure.

---

## <a name="download-and-install"></a>Télécharger et installer

Pour télécharger et installer l’Explorateur Stockage, consultez [Explorateur Stockage Azure](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Connexion à un service ou un compte de stockage

L’Explorateur Stockage offre de nombreuses façons de se connecter aux ressources Azure :

* [Vous connecter à Azure pour accéder à vos abonnements et à leurs ressources](#sign-in-to-azure)
* [Attacher à une ressource Stockage Azure individuelle](#attach-to-an-individual-resource)
* [Attacher à une ressource CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Connexion à Azure

> [!NOTE]
> Pour accéder pleinement aux ressources après la connexion, l’Explorateur Stockage nécessite des autorisations de gestion (Azure Resource Manager) et de couche de données. Cela signifie que vous devez disposer d’autorisations Azure Active Directory (Azure AD) pour avoir accès à votre compte de stockage, aux conteneurs du compte et aux données dans les conteneurs. Si vous disposez d’autorisations uniquement au niveau de la couche de données, envisagez de choisir l’option **Se connecter à l’aide d’Azure Active Directory (Azure AD)** pour l’attachement à une ressource. Pour plus d’informations sur les autorisations spécifiques dont l’Explorateur Stockage a besoin, consultez le [guide de résolution des problèmes de l’Explorateur Stockage Azure](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. Dans l’Explorateur Stockage, sélectionnez **Afficher** > **Gestion des comptes** ou sélectionnez le bouton **Gérer les comptes**.

    :::image type="content" alt-text="Gérer les comptes" source ="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-manage-accounts.png":::

1. **GESTION DES COMPTES** affiche maintenant tous les comptes Azure auxquels vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte...** .

1. La boîte de dialogue **Connexion à Stockage Azure** s’ouvre. Dans le volet **Sélectionner une ressource**, sélectionnez **Abonnement**.

    :::image type="content" alt-text="Boîte de dialogue de connexion" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-dialog.png":::

1. Dans le volet **Sélectionner l’environnement Azure**, sélectionnez un environnement Azure pour la connexion. Vous pouvez vous connecter à Azure global, à un cloud national ou à une instance d’Azure Stack. Sélectionnez ensuite **Suivant**.

    :::image type="content" alt-text="Option de connexion" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Pour plus d’informations sur Azure Stack, consultez [Connecter l’Explorateur Stockage à un compte de stockage ou à un abonnement Azure Stack](/azure-stack/user/azure-stack-storage-connect-se).

1. L’Explorateur Stockage ouvre une page web pour que vous vous connectiez.

1. Une fois connecté avec un compte Azure, le compte et les abonnements Azure associés à ce compte s’affichent sous **GESTION DES COMPTES**. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer**.

    :::image type="content" alt-text="Sélectionner les abonnements Azure" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-account-panel.png":::

1. Le volet **EXPLORATEUR** affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

    :::image type="content" alt-text="Abonnements Azure sélectionnés" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Attacher à une ressource individuelle

L’Explorateur Stockage vous permet de vous connecter à des ressources individuelles, telles qu’un conteneur Azure Data Lake Storage Gen2, à l’aide de différentes méthodes d’authentification. Certaines méthodes d’authentification sont uniquement prises en charge pour certains types de ressources.

| Type de ressource    | Azure AD | Nom et clé du compte | Signature d’accès partagé (SAS)  | Public (anonyme) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Comptes de stockage | Oui      | Oui                  | Oui (chaîne de connexion ou URL) | Non                 |
| Conteneurs d’objets blob  | Oui      | Non                   | Oui (URL)                      | Oui                |
| Conteneurs Gen2  | Oui      | Non                   | Oui (URL)                      | Oui                |
| Répertoires Gen2 | Oui      | Non                   | Oui (URL)                      | Oui                |
| Partages de fichiers      | Non       | Non                   | Oui (URL)                      | Non                 |
| Files d’attente           | Oui      | Non                   | Oui (URL)                      | Non                 |
| Tables           | Non       | Non                   | Oui (URL)                      | Non                 |
 
L’Explorateur Stockage peut également se connecter à un [émulateur de stockage local](#local-storage-emulator) à l’aide des ports configurés de l’émulateur.

Pour vous connecter à une ressource individuelle, sélectionnez le bouton **Connecter** dans la barre d’outils de gauche. Suivez ensuite les instructions pour le type de ressource auquel vous souhaitez vous connecter.

:::image type="content" alt-text="Option Se connecter à Azure Storage" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-button.png":::

Quand une connexion à un compte de stockage est correctement ajoutée, un nouveau nœud d’arborescence s’affiche sous **Local et attaché** > **Comptes de stockage**.

Pour les autres types de ressources, un nouveau nœud est ajouté sous **Local et attaché** > **Comptes de stockage** >  **(Conteneurs attachés)** . Le nœud s’affiche sous un nœud de groupe correspondant à son type. Par exemple, une nouvelle connexion à un conteneur d’Azure Data Lake Storage Gen2 s’affiche sous **Conteneurs d’objets blob**.

Si l’Explorateur Stockage n’a pas pu ajouter votre connexion ou si vous ne pouvez pas accéder à vos données après avoir correctement ajouté la connexion, consultez le [guide de résolution des problèmes de l’Explorateur Stockage Azure](./storage/common/storage-explorer-troubleshooting.md).

Les sections suivantes décrivent les différentes méthodes d’authentification que vous pouvez utiliser pour vous connecter à des ressources individuelles.

#### <a name="azure-ad"></a>Azure AD

L’Explorateur Stockage peut utiliser votre compte Azure pour vous connecter aux types de ressources suivants :
* Conteneurs d’objets blob
* Conteneurs Azure Data Lake Storage Gen2
* Répertoires Azure Data Lake Storage Gen2
* Files d’attente
 
Azure AD est l’option recommandée si vous disposez d’un accès à la couche de données de votre ressource, mais pas d’un accès à la couche de gestion.

1. Connectez-vous à au moins un compte Azure à l’aide des [étapes décrites ci-dessus](#sign-in-to-azure).
1. Dans le volet **Sélectionner une ressource** de la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez **Conteneur d’objets blob**, **Conteneur ADLS Gen2** ou **File d’attente**.
1. Sélectionnez **Se connecter à l’aide d’Azure Active Directory (Azure AD)** , puis sélectionnez **Suivant**.
1. Sélectionnez un locataire et un compte Azure. Le compte et le locataire doivent avoir accès à la ressource de stockage à laquelle vous souhaitez les attacher. Sélectionnez **Suivant**.
1. Entrez un nom d’affichage pour votre connexion et l’URL de la ressource. Sélectionnez **Suivant**.
1. Passez en revue les informations de connexion dans le volet **Résumé**. Si les informations de connexion sont correctes, sélectionnez **Connecter**.

#### <a name="account-name-and-key"></a>Nom et clé du compte

L’Explorateur Stockage peut se connecter à un compte de stockage à l’aide du nom et de la clé du compte de stockage.

Vous pouvez rechercher vos clés de compte dans le [portail Azure](https://portal.azure.com). Ouvrez la page de votre compte de stockage et sélectionnez **Paramètres** > **Clés d’accès**.

1. Dans le volet **Sélectionner une ressource** de la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez **Compte de stockage**.
1. Sélectionnez **Nom et clé de compte**, puis sélectionnez **Suivant**.
1. Entrez un nom d’affichage pour votre connexion, le nom du compte et l’une des clés de compte. Sélectionnez l’abonnement Azure approprié. Sélectionnez **Suivant**.
1. Passez en revue les informations de connexion dans le volet **Résumé**. Si les informations de connexion sont correctes, sélectionnez **Connecter**.

#### <a name="shared-access-signature-sas-connection-string"></a>Chaîne de connexion de la signature d’accès partagé (SAS)

L’Explorateur Stockage peut se connecter à un compte de stockage à l’aide d’une chaîne de connexion avec une signature d’accès partagé (SAS). Une chaîne de connexion SAS se présente ainsi :

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. Dans le volet **Sélectionner une ressource** de la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez **Compte de stockage**.
1. Sélectionnez **Signature d’accès partagé (SAS)** et sélectionnez **Suivant**.
1. Entrez un nom d’affichage pour votre connexion et la chaîne de connexion SAS pour le compte de stockage. Sélectionnez **Suivant**.
1. Passez en revue les informations de connexion dans le volet **Résumé**. Si les informations de connexion sont correctes, sélectionnez **Connecter**.

#### <a name="shared-access-signature-sas-url"></a>URL de signature d’accès partagé (SAS)

L’Explorateur Stockage peut se connecter aux types de ressources suivants à l’aide d’un URI SAS :
* Conteneur d’objets blob
* Conteneur ou répertoire Azure Data Lake Storage Gen2
* Partage de fichiers
* File d'attente
* Table de charge de travail

Un URI SAS se présente comme suit :

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. Dans le volet **Sélectionner une ressource** de la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez la ressource à laquelle vous souhaitez vous connecter.
1. Sélectionnez **Signature d’accès partagé (SAS)** et sélectionnez **Suivant**.
1. Entrez un nom d’affichage pour votre connexion et l’URI SAS de la ressource. Sélectionnez **Suivant**.
1. Passez en revue les informations de connexion dans le volet **Résumé**. Si les informations de connexion sont correctes, sélectionnez **Connecter**.

#### <a name="local-storage-emulator"></a>Émulateur de stockage local

L’Explorateur Stockage peut se connecter à un émulateur de stockage Azure. Actuellement, deux émulateurs sont pris en charge :

* [Émulateur de stockage Azure](storage/common/storage-use-emulator.md) (Windows uniquement)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Si votre émulateur est à l’écoute sur les ports par défaut, vous pouvez utiliser le nœud **Local et attaché** > **Comptes de stockage** > **Émulateur – Ports par défaut** pour accéder à votre émulateur.

Si vous souhaitez utiliser un autre nom pour votre connexion ou si votre émulateur ne s’exécute pas sur les ports par défaut :

1. Démarrez votre émulateur.

   > [!IMPORTANT]
   > L’Explorateur Stockage ne démarre pas automatiquement votre émulateur. Vous devez le démarrer manuellement.

1. Dans le volet **Sélectionner une ressource** de la boîte de dialogue **Se connecter à Stockage Azure**, sélectionnez **Émulateur de stockage local**.
1. Entrez un nom d’affichage pour votre connexion et le numéro de port pour chaque service émulé que vous souhaitez utiliser. Si vous ne souhaitez pas utiliser un service, laissez le port correspondant vide. Sélectionnez **Suivant**.
1. Passez en revue les informations de connexion dans le volet **Résumé**. Si les informations de connexion sont correctes, sélectionnez **Connecter**.

### <a name="connect-to-azure-cosmos-db"></a>Se connecter à Azure Cosmos DB

L’Explorateur Stockage prend également en charge la connexion aux ressources Azure Cosmos DB.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connexion à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion

Au lieu de gérer des comptes Azure Cosmos DB par le biais d’un abonnement Azure, vous pouvez vous connecter à Azure Cosmos DB en utilisant une chaîne de connexion. Pour vous connecter, procédez comme suit :

1. Sous **EXPLORATEUR**, développez **Local et attaché**, cliquez avec le bouton droit sur **Comptes Azure Cosmos DB** et sélectionnez **Se connecter à Azure Cosmos DB**.

    ![Se connecter à Azure Cosmos DB avec une chaîne de connexion][21]

1. Sélectionnez l’API Azure Cosmos DB, entrez vos données **Chaîne de connexion**, puis sélectionnez **OK** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion, consultez [Gérer un compte Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Chaîne de connexion][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Se connecter à Azure Data Lake Store par URI

Vous pouvez accéder à une ressource qui n’est pas située dans votre abonnement. Pour cela, une personne qui a accès à cette ressource doit vous fournir l’URI associée. Une fois connecté, connectez-vous à Data Lake Store à l’aide de l’URI. Pour vous connecter, procédez comme suit :

1. Sous **EXPLORATEUR**, développez **Local et attaché**.

1. Cliquez avec le bouton droit sur **Data Lake Storage Gen1**, puis sélectionnez **Se connecter à Data Lake Storage Gen1**.

    ![Menu contextuel Se connecter à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Entrez l’URI, puis sélectionnez **OK**. Votre Data Lake Store s’affiche sous **Data Lake Storage**.

    ![Résultat de la connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Cet exemple utilise Data Lake Storage Gen1. Azure Data Lake Storage Gen2 est désormais disponible. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Générer une signature d’accès partagé dans l’Explorateur Stockage<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Signature d’accès partagé au niveau du compte

1. Cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager, puis sélectionnez **Obtenir une signature d’accès partagé**.

    ![Option de menu contextuel Obtenir une signature d’accès partagé][14]

1. Dans **Signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis sélectionnez **Créer**.

    ![Obtenir une signature d’accès partagé][15]

1. Copiez la **Chaîne de connexion** ou la **Chaîne de requête** brute dans votre presse-papiers.

### <a name="service-level-shared-access-signature"></a>Signature d’accès partagé au niveau du service

Vous pouvez obtenir une signature d’accès partagé au niveau du service. Pour plus d’informations, consultez [Obtenir la signature d’accès partagé pour un conteneur d’objets blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Recherche de comptes de stockage

Vous pouvez utiliser le volet **EXPLORATEUR** pour rechercher une ressource de stockage.

À mesure que vous entrez du texte dans la zone de recherche, l’Explorateur Stockage affiche toutes les ressources correspondant à la valeur en cours de saisie. Cet exemple montre une recherche de **points de terminaison** :

![Recherche de compte de stockage][23]

> [!NOTE]
> Pour accélérer votre recherche, utilisez **Gestion des comptes** afin de désélectionner tous les abonnements qui ne contiennent pas l’élément que vous recherchez. Vous pouvez également cliquer avec le bouton droit sur un nœud et sélectionner **Rechercher à partir d’ici** pour démarrer la recherche à partir d’un nœud spécifique.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les ressources de Stockage Blob Azure avec l’Explorateur Stockage](vs-azure-tools-storage-explorer-blobs.md)
* [Utiliser des données à l’aide de l’Explorateur Stockage Azure](./cosmos-db/storage-explorer.md)
* [Gérer les ressources Azure Data Lake Store avec l’Explorateur Stockage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
