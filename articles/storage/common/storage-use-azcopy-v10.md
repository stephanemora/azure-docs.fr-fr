---
title: Copier ou déplacer des données vers Stockage Azure avec AzCopy v10 (préversion) | Microsoft Docs
description: Utilisez l’utilitaire AzCopy v10 (préversion) pour déplacer ou copier des données à destination ou à partir d’un contenu d’objet blob, de data lake et de fichier. Copiez des données vers Azure Storage à partir de fichiers locaux ou copiez des données dans ou entre des comptes de stockage. Migrez facilement vos données vers Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: af45081df280f5542b5ba70892ee74c05b3e99cc
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808111"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Transférer des données avec AzCopy v10 (préversion)

AzCopy v10 (préversion) est un utilitaire de ligne de commande nouvelle génération qui vous permet de copier des données à destination/à partir des services de stockage Microsoft Azure Blob et Fichier. Il offre une interface de ligne de commande remaniée et une nouvelle architecture pour garantir des transferts de données fiables et hautes performances. Grâce à AzCopy, vous pouvez copier des données entre un système de fichiers et un compte de stockage, ou entre comptes de stockage.

## <a name="whats-new-in-azcopy-v10"></a>Nouveautés d’AzCopy v10

- Synchronisez un système de fichiers vers Azure Blob ou vice versa. Utilisez `azcopy sync <source> <destination>`. Idéal pour les scénarios de copie incrémentielle.
- Prend en charge les API Azure Data Lake Storage Gen2. Utilisez `myaccount.dfs.core.windows.net` comme URI pour appeler les API ADLS Gen2.
- Prend en charge la copie de la totalité d’un compte (service Blob uniquement) vers un autre compte.
- La copie de compte à compte utilise maintenant les nouvelles API [Put from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url). Aucun transfert de données vers le client n’est nécessaire, accélérant ainsi les transferts.
- Listez/supprimez des fichiers et des objets blob dans un chemin donné.
- Prend en charge les modèles de caractères génériques dans un chemin, ainsi que les indicateurs --include et --exclude.
- Résilience accrue : chaque instance d’AzCopy crée un ordre de travail et un fichier journal associé. Vous pouvez afficher et redémarrer les travaux précédents et reprendre les travaux ayant échoué. AzCopy réessaie automatiquement un transfert après une défaillance.
- Améliorations générales apportées aux performances.

## <a name="download-and-install-azcopy"></a>Téléchargement et installation d’AzCopy

### <a name="latest-preview-version-v10"></a>Dernière préversion (v10)

Téléchargez la dernière préversion d’AzCopy :
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Dernière version de production (v8.1)

Téléchargez la [dernière version de production d’AzCopy pour Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy prenant en charge le service Stockage Table (v7.3)

Téléchargez [AzCopy v7.3 prenant en charge la copie de données à destination/à partir du service Microsoft Azure Stockage Table](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Étapes post-installation

AzCopy v10 ne nécessite pas d’installation. Ouvrez l’application de ligne de commande de votre choix et accédez au dossier dans lequel se trouve l’exécutable `azcopy.exe`. Si vous le souhaitez, ajoutez l’emplacement du dossier AzCopy au chemin de votre système.

## <a name="authentication-options"></a>Options d’authentification

AzCopy v10 vous permet d’utiliser les options suivantes pour l’authentification auprès de Stockage Azure :
- **Azure Active Directory [pris en charge sur les objets Blob et ADLS Gen2]**. Utilisez ```.\azcopy login``` pour vous connecter à l’aide d’Azure Active Directory.  L’utilisateur doit disposer du [rôle « Contributeur aux données Blob du stockage »](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) pour écrire dans Stockage Blob à l’aide de l’authentification Azure Active Directory.
- **Jetons SAS [pris en charge sur les objets Blob et ADLS Gen2]**. Ajoutez le jeton SAS au chemin d’accès de l’objet blob sur la ligne de commande pour pouvoir l’utiliser. Vous pouvez générer un jeton SAP à l’aide du portail Azure, de [l’Explorateur Stockage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), de [PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-AzStorageblobsastoken) ou d’autres outils de votre choix. Pour plus d’informations, consultez les [exemples](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Prise en main

La syntaxe d’AzCopy v10 est simple et autodocumentée. Lorsque vous vous connectez à l’annuaire Azure Active Directory, la syntaxe générale est la suivante :

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Voici comment obtenir la liste des commandes disponibles :

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Pour afficher la page d’aide et des exemples pour une commande spécifique, exécutez la commande suivante :

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Créer un conteneur d’objets blob ou un partage de fichiers 

**Créer un conteneur d’objets blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Créer un partage de fichiers**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Créer un conteneur d’objets blob avec ADLS Gen2**

Si vous avez activé des espaces de noms hiérarchiques sur votre compte de stockage d’objets blob, vous pouvez utiliser la commande suivante pour créer un système de fichiers (conteneur Blob) où vous pourrez charger des fichiers.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copier des données vers Stockage Azure

Utilisez la commande copy pour transférer des données de la source à la destination. La source/destination peut être l’un des éléments suivants :
- Système de fichiers local
- URI d’un objet blob/répertoire virtuel/conteneur Azure
- URI d’un fichier/répertoire/partage de fichiers Azure
- URI d’un système de fichiers/répertoire/fichier Azure Data Lake Storage Gen2

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

La commande suivante charge tous les fichiers sous le dossier `C:\local\path` de manière récursive sur le conteneur `mycontainer1` créant le répertoire `path` dans le conteneur :

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

La commande suivante charge tous les fichiers sous le dossier `C:\local\path` (sans traiter de manière récursive les sous-répertoires) sur le conteneur `mycontainer1` :

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Pour obtenir d’autres exemples, utilisez la commande suivante :

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copier des données entre deux comptes de stockage

La copie de données entre deux comptes de stockage utilise l’API [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) et n’a pas recours à la bande passante réseau de l’ordinateur client. Les données sont copiées directement entre les deux serveurs Stockage Azure (AzCopy orchestre simplement l’opération de copie). Cette option est actuellement disponible uniquement pour le Stockage Blob.

Pour copier les données entre deux comptes de stockage, utilisez la commande suivante :
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> La commande énumère tous les conteneurs d’objets blob et les copie dans le compte de destination. Pour l’instant, AzCopy v10 prend en charge la copie des objets blob de blocs entre deux comptes de stockage. Tous les autres objets de compte de stockage (objets blob d’ajout, objets blob de pages, fichiers, tables et files d’attente) sont ignorés.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copier une image de disque dur virtuel dans un compte de stockage

AzCopy v10 charge par défaut les données sur des objets blob de blocs. Toutefois, si un fichier source a l’extension .vhd, AzCopy v10 le charge par défaut sur un objet blob de pages. Ce comportement n’est pas configurable pour le moment.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sync : copie incrémentielle et suppression (stockage Blob uniquement)

> [!NOTE]
> La commande sync synchronise le contenu de la source vers la destination. Cette opération inclut la suppression des fichiers de destination qui n’existent pas dans la source. Veillez à utiliser la destination que vous prévoyez de synchroniser.

Pour synchroniser votre système de fichiers local vers un compte de stockage, utilisez la commande suivante :

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

De la même façon, vous pouvez synchroniser un conteneur d’objets blob vers un système de fichiers local :

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

La commande vous permet de synchroniser de façon incrémentielle la source vers la destination en fonction des horodatages de dernière modification. Si vous ajoutez ou supprimez un fichier dans la source, AzCopy v10 fait de même dans la destination. AzCopy vous demande si vous souhaitez vraiment supprimer les fichiers.

## <a name="advanced-configuration"></a>Configuration avancée

### <a name="configure-proxy-settings"></a>Configuration des paramètres de proxy

Pour configurer les paramètres de proxy pour AzCopy v10, définissez la variable d’environnement https_proxy à l’aide de la commande suivante :

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimiser le débit

Définissez la variable d’environnement AZCOPY_CONCURRENCY_VALUE pour configurer le nombre de demandes simultanées et contrôler les performances du débit et la consommation des ressources. Par défaut, la valeur est égale à 300. Si vous réduisez cette valeur, vous limitez la bande passante et l’UC utilisées par AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>Résolution de problèmes

AzCopy v10 crée des fichiers journaux et des fichiers de plan pour tous les travaux. Vous pouvez utiliser les journaux pour examiner et résoudre les problèmes potentiels. Les journaux contiennent l’état de la défaillance (UPLOADFAILED, COPYFAILED et DOWNLOADFAILED), le chemin complet et la raison de la défaillance. Les journaux des travaux et les fichiers de plan se trouvent dans le dossier %USERPROFILE\\.azcopy.

### <a name="review-the-logs-for-errors"></a>Passer en revue les journaux pour détecter la présence d’erreurs

La commande suivante obtient toutes les erreurs dont l’état est UPLOADFAILED à partir du journal 04dc9ca9-158f-7945-5933-564021086c79 :

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Afficher et reprendre des travaux

Chaque opération de transfert crée un travail AzCopy. Vous pouvez afficher l’historique des travaux à l’aide de la commande suivante :

```azcopy
.\azcopy jobs list
```

Pour afficher les statistiques du travail, utilisez la commande suivante :

```azcopy
.\azcopy jobs show <job-id>
```

Pour filtrer les transferts par état, utilisez la commande suivante :

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Vous pouvez reprendre un travail annulé/ayant échoué à l’aide de son identificateur et du jeton SAP (il n’est pas persistant pour des raisons de sécurité) :

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>Modifier le niveau de consignation par défaut

Par défaut, le niveau de consignation d’AzCopy est défini sur INFO. Si vous souhaitez réduire la verbosité du journal afin d’économiser de l’espace disque, écrasez le paramètre en utilisant l’option ``--log-level``. Les niveaux de consignation disponibles sont les suivants : DEBUG, INFO, WARNING, ERROR, PANIC et FATAL

## <a name="next-steps"></a>Étapes suivantes

Vos commentaires sont toujours les bienvenus. Si vous avez des questions, des problèmes ou des commentaires d’ordre général, envoyez-les à https://github.com/Azure/azure-storage-azcopy. Merci !