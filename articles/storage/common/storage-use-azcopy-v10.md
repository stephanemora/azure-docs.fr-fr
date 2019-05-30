---
title: Copier ou déplacer des données vers le stockage Azure à l’aide d’AzCopy v10 | Microsoft Docs
description: AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des données vers, depuis ou entre comptes de stockage. Cet article vous aide à télécharger AzCopy, connectez-vous à votre compte de stockage et transférer des fichiers.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: cc65d6d3f7e7dcc08ea29ecc8a299b556563135b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236309"
---
# <a name="get-started-with-azcopy"></a>Prise en main d’AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des objets BLOB ou des fichiers vers ou à partir d’un compte de stockage. Cet article vous aide à télécharger AzCopy, connectez-vous à votre compte de stockage et transférer des fichiers.

> [!NOTE]
> AzCopy **V10** est la version actuellement prise en charge d’AzCopy.
>
> Si vous avez besoin d’utiliser AzCopy **v8.1**, consultez le [utiliser la version précédente d’AzCopy](#previous-version) section de cet article.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Téléchargement d’AzCopy

Tout d’abord, téléchargez le fichier exécutable AzCopy V10 à n’importe quel dossier sur votre ordinateur. Pour plus de commodité, vous pouvez envisager l’emplacement du dossier AzCopy à votre chemin d’accès système pour sa facilité d’utilisation.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Si vous souhaitez copier des données vers et depuis votre [stockage Table Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) de service, puis installer [AzCopy version 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Exécuter AzCopy

À partir d’une invite de commandes, accédez au répertoire où vous avez téléchargé le fichier.

Pour afficher une liste de commandes AzCopy, tapez `azCopy`, puis appuyez sur la touche ENTRÉE.

Pour en savoir plus sur une commande spécifique, tapez `azCopy` suivie du nom de la commande.

Par exemple, pour en savoir plus sur la `copy` de commandes, tapez `azcopy copy`, puis appuyez sur la touche ENTRÉE.

Avant de vous donner un sens avec AzCopy, vous devez décider comment vous allez fournir des informations d’identification d’autorisation pour le service de stockage.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choisissez comment vous allez fournir des informations d’identification de l’autorisation

Vous pouvez fournir des informations d’identification d’autorisation à l’aide d’Azure Active Directory (AD), ou en utilisant un jeton de Signature d’accès partagé (SAP).

Utilisez ce tableau comme guide :

| Type de stockage | Méthode actuellement pris en charge de l’autorisation |
|--|--|
|**Stockage Blob** | Azure AD & SAS |
|**Stockage d’objets BLOB (espace de noms hiérarchique)** | Azure AD uniquement |
|**Stockage Fichier** | SAS uniquement |

### <a name="option-1-use-azure-ad"></a>Option 1 : Utiliser Azure AD

Le niveau d’autorisation dont vous avez besoin dépend de si vous souhaitez charger des fichiers ou simplement de les télécharger.

#### <a name="authorization-to-upload-files"></a>Autorisation de télécharger des fichiers

Vérifiez qu’un de ces rôles a été attribué à votre identité :

- [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ces rôles peuvent être affectés à votre identité dans un de ces étendues :

- Conteneur (système de fichiers)
- Compte de stockage
- Groupe de ressources
- Abonnement

Pour savoir comment vérifier et attribuer des rôles, consultez [accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Vous n’avez pas besoin d’avoir une de ces rôles affectés à votre identité si votre identité est ajoutée à la liste de contrôle d’accès (ACL) du conteneur cible ou du dossier. Dans l’ACL, votre identité a besoin d’autorisation en écriture sur le dossier cible et l’autorisation d’exécution sur le conteneur et chaque dossier parent.

Pour plus d’informations, consultez [contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authorization-to-download-files"></a>Autorisation de télécharger des fichiers

Vérifiez qu’un de ces rôles a été attribué à votre identité :

- [Lecteur des données blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ces rôles peuvent être affectés à votre identité dans un de ces étendues :

- Conteneur (système de fichiers)
- Compte de stockage
- Groupe de ressources
- Abonnement

Pour savoir comment vérifier et attribuer des rôles, consultez [accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Vous n’avez pas besoin d’avoir une de ces rôles affectés à votre identité si votre identité est ajoutée à la liste de contrôle d’accès (ACL) du conteneur cible ou du dossier. Dans l’ACL, votre identité a besoin d’autorisation de lecture sur le dossier cible et l’autorisation execute sur le conteneur et chaque dossier parent.

Pour plus d’informations, consultez [contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-your-identity"></a>Authentifier votre identité

Une fois que vous avez vérifié que votre identité a été donnée le niveau d’autorisation nécessaire, ouvrez une invite de commandes, tapez la commande suivante, puis appuyez sur la touche ENTRÉE.

```azcopy
azcopy login
```

Cette commande retourne un code d’authentification et l’URL d’un site Web. Ouvrez le site Web, fournissez le code, puis choisissez le **suivant** bouton.

![Créez un conteneur.](media/storage-use-azcopy-v10/azcopy-login.png)

Une fenêtre de connexion s’affiche. Dans cette fenêtre, connectez-vous à votre compte Azure à l’aide de vos informations d’identification de compte Azure. Une fois que vous avez correctement connecté, vous pouvez fermer la fenêtre du navigateur et commencer à l’aide d’AzCopy.

### <a name="option-2-use-a-sas-token"></a>Option 2 : Utiliser un jeton SAS

Vous pouvez ajouter un jeton SAP à chaque URL source ou destination utiliser dans vos commandes AzCopy.

Cet exemple commande copie de manière récursive données à partir d’un répertoire local à un conteneur d’objets blob. Un jeton SAP fictif est ajouté à la fin de la de l’URL du conteneur.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Pour en savoir plus sur les jetons SAP et comment en obtenir un, consultez [utilisation des signatures d’accès (SAP) partagé](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Transférer des fichiers

Une fois que vous avez authentifié votre identité ou obtenu un jeton SAP, vous pouvez commencer le transfert de fichiers.

Pour rechercher des exemples de commandes, consultez un de ces articles.

- [Transfert de données avec AzCopy et le stockage blob](storage-use-azcopy-blobs.md)

- [Transfert de données avec AzCopy et stockage fichier](storage-use-azcopy-files.md)

- [Transfert de données avec AzCopy et Amazon S3 compartiments](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurer, optimiser et résoudre les problèmes d’AzCopy

Consultez [configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>Utilisez AzCopy dans l’Explorateur de stockage

Si vous souhaitez exploiter les avantages de performances d’AzCopy, mais vous préférez utiliser l’Explorateur de stockage plutôt que la ligne de commande pour interagir avec vos fichiers, puis activez AzCopy dans l’Explorateur de stockage.

Dans l’Explorateur de stockage, choisissez **aperçu**->**utiliser AzCopy pour chargement amélioré d’objets Blob et le téléchargement**.

![Activer AzCopy comme un moteur de transfert dans l’Explorateur de stockage Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Vous n’êtes pas obligé d’activer ce paramètre si vous avez activé un espace de noms hiérarchique sur votre compte de stockage. C’est parce que l’Explorateur de stockage utilise automatiquement AzCopy sur les comptes de stockage qui ont un espace de noms hiérarchique.  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utilisez la version précédente d’AzCopy

Si vous avez besoin d’utiliser la version précédente d’AzCopy (AzCopy v8.1), consultez un des liens suivants :

- [AzCopy sur Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy sur Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions, des problèmes ou des commentaires généraux, envoyez-les [sur GitHub](https://github.com/Azure/azure-storage-azcopy) page.
