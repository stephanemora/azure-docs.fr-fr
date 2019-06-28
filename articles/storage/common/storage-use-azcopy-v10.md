---
title: Copier ou déplacer des données vers Stockage Azure avec AzCopy v10 | Microsoft Docs
description: AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des données vers, depuis ou entre des comptes de stockage. Cet article vous aide à télécharger AzCopy, connectez-vous à votre compte de stockage et transférez des fichiers.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: bfa3e5a943ee59b1ed335f45e113a60f62572675
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735027"
---
# <a name="get-started-with-azcopy"></a>Bien démarrer avec AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article vous aide à télécharger AzCopy, connectez-vous à votre compte de stockage et transférez des fichiers.

> [!NOTE]
> La version actuellement prise en charge d’AzCopy est la **version 10**.
>
> Si vous avez besoin d’utiliser AzCopy **v8.1**, consultez la section [Utiliser la version précédente d’AzCopy](#previous-version) de cet article.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Télécharger AzCopy

Commencez par télécharger le fichier exécutable AzCopy V10 dans un répertoire sur votre ordinateur. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Si vous voulez copier des données depuis et vers le service [Table de stockage Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview), installez [AzCopy version 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Exécuter AzCopy

Pour plus de commodité, vous pouvez envisager d’ajouter l’emplacement du répertoire de l’exécutable AzCopy dans votre chemin d’accès du système. De cette façon, vous pouvez saisir `azcopy` à partir de n’importe quel répertoire sur votre système.

Si vous choisissez de ne pas ajouter le répertoire AzCopy à votre chemin d’accès, vous devrez remplacer les répertoires par l’emplacement de votre fichier exécutable AzCopy et saisir `azcopy` ou `.\azcopy` dans les invites de commandes Windows PowerShell.

Pour afficher une liste de commandes, saisissez `azcopy -h` et appuyez sur la touche ENTRÉE.

Pour en savoir plus sur une commande spécifique, incluez simplement le nom de la commande (par exemple : `azcopy list -h`).

![Aide en ligne](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Avant d’effectuer une opération pertinente avec AzCopy, vous devez choisir comment fournir les informations d’identification d’autorisation du service de stockage.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choisissez comment vous allez fournir des informations d’identification d’autorisation.

Vous pouvez fournir des informations d’identification d’autorisation à l’aide d’Azure Active Directory (AD), ou en utilisant un jeton de signature d’accès partagé (SAP).

Utilisez ce tableau pour vous guider :

| Type de stockage | Méthode actuellement prise en charge pour l’autorisation |
|--|--|
|**Stockage Blob** | Azure AD et SAS |
|**Stockage de blobs (espace de noms hiérarchique)** | Azure AD uniquement |
|**Stockage Fichier** | SAS uniquement |

### <a name="option-1-use-azure-ad"></a>Option 1 : Utilisez Azure AD

Le niveau d’autorisation dont vous avez besoin repose sur votre volonté de planifier le chargement des fichiers, ou juste leur téléchargement.

#### <a name="authorization-to-upload-files"></a>Autorisation de chargement des fichiers

Vérifiez qu’un de ces rôles a été attribué à votre identité :

- [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ces rôles peuvent être attribués à votre identité sur l’une de ces étendues :

- Conteneur (système de fichiers)
- Compte de stockage
- Groupe de ressources
- Abonnement

Pour savoir comment vérifier et attribuer des rôles, voir [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC dans le Portail Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Si votre identité est ajoutée à la liste de contrôle d’accès (ACL) du conteneur ou répertoire cible, vous n’avez pas besoin qu’un de ces rôles soit affecté à cette identité. Dans la liste ACL, votre identité doit être associée à une autorisation d’écriture sur le répertoire cible, et d’un privilège d’exécution sur le conteneur et chaque répertoire parent.

Pour en savoir plus, voir [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Autorisation de téléchargement des fichiers

Vérifiez qu’un de ces rôles a été attribué à votre identité :

- [Lecteur des données blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ces rôles peuvent être attribués à votre identité sur l’une de ces étendues :

- Conteneur (système de fichiers)
- Compte de stockage
- Groupe de ressources
- Abonnement

Pour savoir comment vérifier et attribuer des rôles, voir [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC dans le Portail Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Si votre identité est ajoutée à la liste de contrôle d’accès (ACL) du conteneur ou répertoire cible, vous n’avez pas besoin qu’un de ces rôles soit affecté à cette identité. Dans la liste ACL, votre identité doit être associée à une autorisation de lecture sur le répertoire cible, et d’un privilège d’exécution sur le conteneur et chaque répertoire parent.

Pour en savoir plus, voir [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Authentifier votre identité

Une fois que vous avez vérifié que votre identité est associée au niveau d’autorisation nécessaire, ouvrez une invite de commandes, saisissez la commande suivante, puis appuyez sur la touche ENTRÉE.

```azcopy
azcopy login
```

Cette commande retourne un code d’authentification et l’URL d’un site web. Ouvrez le site web, indiquez le code, puis choisissez le bouton **Suivant**.

![Créez un conteneur.](media/storage-use-azcopy-v10/azcopy-login.png)

Une fenêtre de connexion s’affiche. Dans cette fenêtre, connectez-vous à votre compte Azure à l’aide de vos informations d’identification de compte Azure. Une fois connecté, vous pouvez fermer la fenêtre du navigateur et commencer à utiliser AzCopy.

### <a name="option-2-use-a-sas-token"></a>Option 2 : Utiliser un jeton SAS

Vous pouvez ajouter un jeton SAP à chaque URL source ou de destination utilisée dans vos commandes AzCopy.

Cet exemple de commande copie de manière récursive les données d’un répertoire local dans un conteneur de blobs. Un jeton SAP fictif est ajouté à la fin de l’URL du conteneur.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Pour en savoir plus sur les jetons SAS et savoir comment en obtenir un, voir [Utilisation des signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Transférer des fichiers

Une fois que vous avez authentifié votre identité ou obtenu un jeton SAP, vous pouvez commencer le transfert des fichiers.

Pour obtenir des exemples de commandes, consultez l’un de ces articles.

- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)

- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)

- [Transférer des données avec AzCopy et des compartiments Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurer, optimiser et dépanner AzCopy

Voir [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>Utiliser AzCopy dans l’Explorateur de stockage

Si vous souhaitez tirer parti des avantages d’AzCopy en termes de performances, mais préférez utiliser l’Explorateur de stockage plutôt que la ligne de commande pour interagir avec vos fichiers, activez AzCopy dans l’Explorateur de stockage.

Dans l’Explorateur de stockage, choisissez **Préversion**->**Use AzCopy for Improved Blob Upload and Download** (Utiliser AzCopy pour le chargement et le téléchargement améliorés des blobs).

![Activer AzCopy en tant que moteur de transfert dans l’Explorateur Stockage Microsoft Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Vous n’êtes pas obligé d’activer ce paramètre si vous avez activé un espace de noms hiérarchique sur votre compte de stockage. En effet, l’Explorateur de stockage utilise automatiquement AzCopy sur les comptes de stockage qui disposent d’un espace de noms hiérarchique.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utiliser la version précédentes d’AzCopy

Si vous devez utiliser la version précédente d’AzCopy (AzCopy v8.1), consultez l’un des liens suivants :

- [AzCopy sur Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy sur Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions, des problèmes ou des commentaires d’ordre général, saisissez-les sur la page [GitHub](https://github.com/Azure/azure-storage-azcopy).
