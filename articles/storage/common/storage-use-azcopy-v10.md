---
title: Copier ou déplacer des données vers le stockage Azure à l’aide d’AzCopy v10 (version préliminaire) | Microsoft Docs
description: Utiliser le v10 AzCopy utilitaire de ligne de commande (version préliminaire) pour déplacer ou copier des données vers ou à partir d’objets blob, data lake et contenu du fichier. Copiez des données vers Azure Storage à partir de fichiers locaux ou copiez des données dans ou entre des comptes de stockage. Migrez facilement vos données vers Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ad3e96af95d952956af02acfd87d6d317bc29ed0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574975"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>Transfert de données avec AzCopy v10 (version préliminaire)

AzCopy v10 (version préliminaire) est l’utilitaire de ligne de commande pour copier des données vers ou à partir d’objets Blob Microsoft Azure et stockage fichier. AzCopy v10 offre une interface de ligne de commande remaniée et transfère nouvelle architecture pour les données fiables. En utilisant AzCopy, vous pouvez copier des données entre un système de fichiers et un compte de stockage ou entre des comptes de stockage.

## <a name="whats-new-in-azcopy-v10"></a>Nouveautés d’AzCopy v10

- Synchronise les systèmes de fichiers vers le stockage Blob Azure ou vice versa. Utilisez `azcopy sync <source> <destination>`. Idéal pour les scénarios de copie incrémentielle.
- Prend en charge les API Azure Data Lake Storage Gen2. Utilisez `myaccount.dfs.core.windows.net` comme un URI à appeler l’API Data Lake Storage Gen2.
- Prend en charge la copie de la totalité d’un compte (service Blob uniquement) vers un autre compte.
- Utilise le nouveau [placer un bloc à partir de l’URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API pour prendre en charge la copie de compte à ce compte. Le transfert de données est plus rapide, étant donné que le transfert vers le client n’est pas nécessaire.
- Répertorie ou supprime des fichiers et objets BLOB dans un chemin d’accès donné.
- Prend en charge les modèles de caractère générique dans un chemin d’accès et--exclude indicateurs.
- Crée un ordre de travail et un fichier de journal associées avec chaque instance de AzCopy. Vous pouvez afficher et redémarrer les travaux précédents et reprendre des travaux ayant échoué. AzCopy réessaie automatiquement un transfert après une défaillance.
- Améliorations des performances générales de fonctionnalités.

## <a name="download-and-install-azcopy"></a>Téléchargement et installation d’AzCopy

### <a name="latest-preview-version-v10"></a>Dernière préversion (v10)

Téléchargez la dernière préversion d’AzCopy :
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Dernière version de production (v8.1)

Téléchargez la [dernière version de production d’AzCopy pour Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy prenant en charge le service Stockage Table (v7.3)

Téléchargez [AzCopy v7.3 prenant en charge la copie de données à destination/à partir du service Microsoft Azure Stockage Table](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Étapes de post-installation

AzCopy v10 ne nécessite pas une installation. Ouvrez l’application de ligne de commande de votre choix et accédez au dossier où `azcopy.exe` se trouve. Si nécessaire, vous pouvez ajouter l’emplacement du dossier AzCopy au chemin système pour sa facilité d’utilisation.

## <a name="authentication-options"></a>Options d’authentification

AzCopy v10 prend en charge les options suivantes lors de l’authentification avec le stockage Azure :
- **Azure Active Directory** (prise en charge pour **services Blob et Data Lake Storage Gen2**). Utilisez ```.\azcopy login``` pour vous connecter avec Azure Active Directory.  L’utilisateur doit avoir [rôle « Contributeur aux données stockage Blob » affecté](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) à écrire dans le stockage Blob avec l’authentification Azure Active Directory. Pour l’authentification via des identités gérées pour les ressources Azure, utilisez `azcopy login --identity`.
- **Partagé des jetons de signature d’accès [pris en charge pour les services Blob et fichier]**. Ajouter le jeton de signature (SAS) d’accès partagé pour le chemin d’accès de l’objet blob sur la ligne de commande pour l’utiliser. Vous pouvez générer des jetons SAS avec le portail Azure, [Explorateur de stockage](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), ou d’autres outils de votre choix. Pour plus d’informations, consultez les [exemples](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Prise en main

> [!TIP]
> **Vous préférez une interface utilisateur graphique ?**
>
> [Explorateur de stockage Azure](https://azure.microsoft.com/features/storage-explorer/), un client de bureau qui simplifie la gestion des données de stockage Azure, utilise désormais AzCopy pour accélérer le transfert de données vers et à partir du stockage Azure.
>
> Activer AzCopy dans l’Explorateur de stockage sous le **aperçu** menu.
> ![Activer AzCopy comme un moteur de transfert dans l’Explorateur de stockage Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 a une syntaxe documentée. Lorsque vous êtes connecté à Azure Active Directory, la syntaxe générale ressemble à ceci :

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Voici comment obtenir la liste des commandes disponibles :

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Pour afficher la page d’aide et des exemples pour une commande spécifique, exécutez la commande suivante :

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Créer un partage de conteneur ou du fichier blob 

**Créer un conteneur d’objets blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Créer un partage de fichiers**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Créer un conteneur d’objets blob à l’aide d’Azure Data Lake Storage Gen2**

Si vous avez activé des espaces de noms hiérarchique sur votre compte de stockage d’objets Blob, vous pouvez utiliser la commande suivante pour créer un conteneur d’objets blob pour charger des fichiers.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copier des données vers Stockage Azure

Utilisez la commande copy pour transférer des données de la source à la destination. La source ou la destination peut être un :
- Système de fichiers local
- URI d’un objet blob/répertoire virtuel/conteneur Azure
- URI d’un fichier/répertoire/partage de fichiers Azure
- URI d’un système de fichiers/répertoire/fichier Azure Data Lake Storage Gen2

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

La commande suivante télécharge tous les fichiers sous le dossier `C:\local\path` récursivement dans le conteneur `mycontainer1`, en créant `path` répertoire dans le conteneur :

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

La commande suivante charge tous les fichiers sous le dossier `C:\local\path` (sans traiter de manière récursive les sous-répertoires) sur le conteneur `mycontainer1` :

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Pour trouver plus d’exemples, utilisez la commande suivante :

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copier des données entre deux comptes de stockage

Copie de données entre deux comptes de stockage utilise le [placer le bloc à partir d’URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API et n’utilise pas la bande passante du réseau de l’ordinateur client. Données copiées entre deux serveurs de stockage Azure directement, tandis que AzCopy orchestre simplement l’opération de copie. Cette option est actuellement uniquement disponible pour le stockage Blob.

Pour copier les données entre deux comptes de stockage, utilisez la commande suivante :
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Cette commande énumérer tous les conteneurs d’objets blob et les copier dans le compte de destination. À ce stade, AzCopy v10 prend en charge la copie des objets BLOB de blocs entre deux comptes de stockage. Elle ignore tous les autres objets de compte de stockage (par exemple, ajouter des objets BLOB, les objets BLOB de pages, les fichiers, les tables et les files d’attente).

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copier une image de disque dur virtuel dans un compte de stockage

AzCopy v10 charge par défaut les données sur des objets blob de blocs. Toutefois, si un fichier source a un `.vhd` extension, AzCopy v10 par défaut au téléchargement vers un objet blob de pages. Pour l’instant, cette action n’est pas configurable.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sync : copie incrémentielle et suppression (stockage Blob uniquement)

La commande de synchronisation synchronise le contenu d’un répertoire source dans un répertoire dans la destination de la comparaison des noms de fichiers et les horodateurs de la dernière modification. Cette opération inclut la suppression facultative de fichiers de destination si celles n’existent pas dans la source lorsque le `--delete-destination=prompt|true` indicateur est fourni. Par défaut, le comportement de suppression est désactivé. 

> [!NOTE] 
> Utilisez le `--delete-destination` indicateur avec précaution. Activer la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) fonctionnalité avant d’activer le comportement de suppression synchronisés pour empêcher les suppressions accidentelles dans votre compte. 
>
> Lorsque `--delete-destination` est défini sur true, AzCopy supprimera les fichiers qui n’existent pas dans la source à partir de destination sans invitation à l’utilisateur. Si vous souhaitez être invité à confirmer l’opération, utilisez `--delete-destination=prompt`.

Pour synchroniser votre système de fichiers local vers un compte de stockage, utilisez la commande suivante :

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Vous pouvez également synchroniser un conteneur d’objets blob à un système de fichiers local :

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Cette commande synchronise incrémentielle de la source vers la destination en fonction des horodatages modifiés dernière. Si vous ajoutez ou supprimez un fichier dans la source, AzCopy v10 fait de même dans la destination. Avant la suppression, AzCopy vous invitera à confirmer.

## <a name="advanced-configuration"></a>Configuration avancée

### <a name="configure-proxy-settings"></a>Configuration des paramètres de proxy

Pour configurer les paramètres de proxy pour AzCopy v10, définissez le https_proxy de variable d’environnement à l’aide de la commande suivante :

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimiser le débit

Définissez la variable d’environnement AZCOPY_CONCURRENCY_VALUE pour configurer le nombre de demandes simultanées et pour contrôler la consommation de ressources et des performances de débit. Par défaut, la valeur est égale à 300. Si vous réduisez cette valeur, vous limitez la bande passante et l’UC utilisées par AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Changer l'emplacement des fichiers journaux

Si nécessaire, ou pour éviter de saturer le disque du système d'exploitation, vous pouvez changer l'emplacement des fichiers journaux.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Modifier le niveau de consignation par défaut 

Niveau de journal AzCopy est la valeur par défaut, INFO. Si vous souhaitez réduire la verbosité du journal afin d’économiser de l’espace disque, écrasez le paramètre en utilisant l’option ``--log-level``. Les niveaux de consignation disponibles sont les suivants : DÉBOGAGE, Infos, avertissement, erreur, panique et irrécupérable.

### <a name="review-the-logs-for-errors"></a>Passer en revue les journaux pour détecter la présence d’erreurs

La commande suivante obtient toutes les erreurs dont l’état est UPLOADFAILED à partir du journal 04dc9ca9-158f-7945-5933-564021086c79 :

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Résolution de problèmes

AzCopy v10 crée des fichiers journaux et les fichiers de plan pour chaque travail. Vous pouvez utiliser les journaux pour examiner et résoudre les problèmes potentiels. Les journaux contiennent l’état de la défaillance (UPLOADFAILED, COPYFAILED et DOWNLOADFAILED), le chemin complet et la raison de la défaillance. Les journaux des travaux et les fichiers de plan se trouvent dans le dossier %USERPROFILE\\.azcopy sous Windows et dans le dossier $HOME\\.azcopy sous Mac et Linux.

> [!IMPORTANT]
> Lorsque vous soumettez une demande de Support Microsoft (ou résoudre le problème impliquant un tiers), partagent la version rédigée de la commande que vous souhaitez exécuter. Cela garantit que la SAP n’est pas accidentellement partagée avec tout le monde. Vous trouverez la version expurgée au début du fichier journal.

### <a name="view-and-resume-jobs"></a>Afficher et reprendre des travaux

Chaque opération de transfert crée un travail AzCopy. Utilisez la commande suivante pour afficher l’historique des travaux :

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

Utilisez la commande suivante pour reprendre un travail échec/annulé. Cette commande utilise son identificateur, ainsi que le jeton SAP. Il n’est pas persistant pour des raisons de sécurité :

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions, des problèmes ou des commentaires généraux, envoyez-les [sur GitHub](https://github.com/Azure/azure-storage-azcopy).


