---
title: Vue d’ensemble des comptes de stockage Azure | Microsoft Doc
description: Découvrez les options de création et d’utilisation des comptes de stockage Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f1b2bdcecac0aade21c6c770b2495a1e15ba9bc5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174028"
---
# <a name="azure-storage-account-overview"></a>Vue d’ensemble des comptes de stockage Azure

Un compte de stockage Azure contient tous vos objets de données de stockage Azure : objets blob, fichiers, files d’attente, tables et disques. Le compte de stockage fournit pour vos données de stockage Azure un espace de noms unique, accessible de n’importe où dans le monde via HTTP ou HTTPS. Les données dans votre compte de stockage Azure sont durables et hautement disponibles, sécurisées et massivement évolutives.

Pour plus d’informations sur la création d’un compte de stockage Azure, consultez [Créer un compte de stockage](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Les comptes de stockage à usage général v2

Les comptes de stockage v2 à usage général prennent en charge les dernières fonctionnalités du Stockage Azure, et intègrent toutes les fonctionnalités des comptes de stockage v1 à usage général et des comptes de stockage d’objets blob. Pour le stockage Azure, ce sont les comptes universels v2 qui offrent les tarifs de capacité par gigaoctet les plus bas. En outre, le prix des transactions est l’un des plus compétitifs du secteur. Les comptes de stockage universels v2 prennent en charge les services de stockage Azure suivants :

- Objets blob (tous les types : Block, Append, Page)
- Data Lake Gen2
- Fichiers
- Disques
- Files d’attente
- Tables

> [!NOTE]
> Microsoft recommande d’utiliser des comptes de stockage universels v2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un compte de stockage universel v1 ou un compte de stockage d’objets blob vers un compte universel v2, sans que cela nécessite un temps d’arrêt ou la copie de données.
>
> Pour plus d’informations sur la mise à niveau vers un compte universel v2, consultez [Mettre à niveau vers un compte de stockage universel v2](storage-account-upgrade.md).

Les comptes de stockage universels v2 offrent plusieurs niveaux d’accès pour le stockage des données, qui sont basés sur vos modèles d’utilisation. Pour plus d’informations, consultez [Niveaux d’accès pour les données d’objets blob de blocs](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Comptes GPv1

Les comptes de stockage universels v1 offrent un accès à tous les services des Stockage Azure, mais ils ne proposent pas les fonctionnalités les plus récentes, ni les tarifs par gigaoctet les plus bas. Les comptes de stockage universels v1 prennent en charge les services de stockage Azure suivants :

- Objets blob (tous les types)
- Fichiers
- Disques
- Files d’attente
- Tables

Même si les comptes de stockage universels v2 sont recommandés dans la plupart des cas, les comptes v1 sont mieux adaptés aux scénarios suivants :

* Si vos applications nécessitent le modèle de déploiement Azure Classic. Les comptes universels v2 et les comptes de stockage d’objets blob prennent uniquement en charge le modèle de déploiement Azure Resource Manager.

* Vos applications utilisent une grande quantité de transactions ou de bande passante de géoréplication, mais ne nécessitent pas une capacité importante. Dans ce cas, le compte universel v1 constitue le choix le plus économique.

* Vous utilisez une version de l’ [API REST des services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) antérieure à celle du 14/02/2014 ou une bibliothèque cliente avec une version inférieure à 4.x, et vous ne pouvez pas mettre à niveau votre application.

### <a name="blockblobstorage-accounts"></a>Comptes BlockBlobStorage

Un compte BlockBlobStorage est un compte de stockage spécialisé utilisé pour stocker des données d’objets non structurées en tant qu’objets blob de blocs et créer des objets blob de blocs Premium. Ce genre de compte de stockage prend en charge le stockage des objets blob de blocs et des objets blob d’ajout, mais pas des objets blob de pages, de tables ou de files d’attente.

En comparaison des comptes BlobStorage et v2 universels , les comptes de stockage BlockBlobStorage fournissent une latence faible et cohérente, et des taux de transactions plus élevés.

Les comptes BlockBlobStorage ne prennent actuellement pas en charge la hiérarchisation en niveau d’accès chaud, froid ou archive.

### <a name="filestorage-accounts"></a>Comptes FileStorage

Un compte FileStorage est un compte de stockage spécialisée utilisé pour stocker et créer des partages de fichiers Premium. Ce genre de compte de stockage prend en charge les fichiers mais pas les objets blob de blocs, d’ajout, de pages, de tables ou de files d’attente. 

Les comptes FileStorage offrent des caractéristiques de performances dédiées uniques, telles que l’IOPS en mode rafale. Pour plus d’informations sur ces caractéristiques, consultez la section [Niveaux de performances de partage de fichiers](../files/storage-files-planning.md#file-share-performance-tiers) du guide de planification des fichiers.

## <a name="naming-storage-accounts"></a>Nommage des comptes de stockage

Gardez les règles suivantes à l’esprit lorsque vous nommez votre compte de stockage :

- Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
- Le nom de votre compte de stockage doit être unique dans Azure. Deux comptes de stockage ne peuvent avoir le même nom.

## <a name="performance-tiers"></a>Niveaux de performances

Les comptes de stockage universels peuvent être configurés avec l’un des niveaux de performances suivants :

* Un niveau de performances Standard pour le stockage des objets blob, des fichiers, des tables, des files d’attente et des disques de machine virtuelle Azure
* Un niveau de performance Premium pour le stockage des disques de machine virtuelle non gérés uniquement

Les comptes de stockage BlockBlobStorage fournissent un niveau de performances Premium pour stocker des objets blob de blocs et d’ajout.

Les comptes de stockage FileStorage fournissent un niveau de performances premium pour les partages de fichiers Azure.

## <a name="access-tiers-for-block-blob-data"></a>Niveaux d’accès pour les données d’objets blob de blocs

Le stockage Azure propose différentes options permettant d’accéder aux données d’objets blob de blocs en fonction du modèle d’utilisation. Chaque niveau d’accès du stockage Azure est optimisé pour un modèle particulier d’utilisation des données. En sélectionnant le niveau d’accès qui correspond à vos besoins, vous pouvez stocker vos données d’objets blob de blocs de manière plus économique.

Les niveaux d’accès disponibles sont les suivants :

* Le niveau d’accès **Chaud**, qui est optimisé pour les accès fréquents aux objets du compte de stockage. L’accès aux données de niveau Chaud est le plus économique. Les coûts de stockage sont toutefois plus élevés. Par défaut, les nouveaux comptes de stockage sont créés au niveau Chaud.
* Le niveau d’accès **Froid**, qui est optimisé pour le stockage d’une grande quantité de données rarement sollicitées et stockées depuis au moins 30 jours. Le stockage des données au niveau Froid est plus économique. Toutefois, l’accès à ces données peut être plus onéreux que celui du niveau chaud.
* Le niveau **Archive**, qui est disponible uniquement pour chaque objet blob de blocs. Le niveau d’archive est optimisé pour les données qui peuvent tolérer plusieurs heures de latence de récupération et qui restent dans le niveau Archive pendant au moins 180 jours. Le niveau Archive est l’option de stockage la plus économique. Toutefois, l’accès à ces données peut être un peu plus onéreux que celui du niveau Chaud ou Froid.

En cas de changement de votre modèle d’utilisation des données, vous pouvez basculer d’un niveau d’accès à l’autre à tout moment. Pour plus d’informations sur les niveaux d’accès, consultez [Stockage d’objets blob Azure : niveaux d’accès chaud, froid et archive](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Le changement du niveau d’accès pour un compte de stockage ou un objet blob existant peut entraîner des frais supplémentaires. Pour plus d’informations, consultez la section [Facturation du compte de stockage](#storage-account-billing).

## <a name="replication"></a>Réplication

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Pour plus d’informations sur la réplication du stockage, consultez [Réplication du stockage Azure](storage-redundancy.md).

## <a name="encryption"></a>Chiffrement

Toutes les données de votre compte de stockage sont chiffrées côté service. Pour plus d’informations sur le chiffrement, consultez [Azure Storage Service Encryption pour les données au repos](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Points de terminaison d'un compte de stockage

Un compte de stockage fournit un espace de noms unique dans Azure pour vos données. Chaque objet que vous stockez dans le stockage Azure a une adresse qui comprend votre nom de compte unique. La combinaison du nom du compte et du point de terminaison de service du stockage Azure forme les points de terminaison de votre compte de stockage.

Par exemple, si le nom de votre compte de stockage universel est *mystorageaccount*, les points de terminaison par défaut de votre compte sont les suivants :

* Stockage d’objets blob : http://*mystorageaccount*.blob.core.windows.net
* Stockage de tables : http://*mystorageaccount*.table.core.windows.net
* Stockage de files d’attente : http://*mystorageaccount*.queue.core.windows.net
* Azure Files : http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Les comptes d’objets blob de blocs et de stockage d’objets blob exposent uniquement le point de terminaison de service blob.

L’URL permettant d’accéder à un objet dans un compte de stockage est formée par l’ajout de l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d’objet blob peut avoir ce format : http://*moncomptedestockage*.blob.core.windows.net/*monconteneur*/*monobjetblob*.

Vous pouvez également configurer votre compte de stockage pour qu’il utilise un domaine personnalisé pour les objets blob. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour votre compte de Stockage Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Contrôler l’accès aux données d’un compte

Par défaut, les données de votre compte sont uniquement accessibles par vous, le propriétaire du compte. Vous pouvez contrôler qui a accès à vos données et quel type d’autorisations accorder.

Chaque requête envoyée à votre compte de stockage doit en avoir l’autorisation. Au niveau du service, la requête doit inclure un en-tête *d’autorisation* valide, comprenant toutes les informations nécessaires pour que le service puisse valider la requête avant de l’exécuter.

Vous pouvez accorder l’accès aux données de votre compte de stockage à l’aide de l’une des méthodes suivantes :

- **Azure Active Directory :** Utilisez les informations d’identification Azure Active Directory (Azure AD) afin d’authentifier un utilisateur, un groupe ou une autre identité, pour l’accès aux données d’objets blob et de files d’attente. Si l’authentification d’une identité réussit, Azure AD retourne un jeton qui doit être utilisé pour autoriser la requête dans le stockage Blob ou File d’attente d’Azure. Pour plus d’informations, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory](storage-auth-aad.md).
- **Autorisation par clé partagée :** Utilisez la clé d’accès de votre compte de stockage pour construire une chaîne de connexion que votre application utilisera lors de l’exécution afin d’accéder au stockage Azure. Les valeurs de la chaîne de connexion sont utilisées pour construire l’en-tête *d’autorisation* qui est passé au stockage Azure. Pour plus d’informations, consultez [Configuration des chaînes de connexion Stockage Azure](storage-configure-connection-string.md).
- **Signature d’accès partagé :** Utilisez une signature d’accès partagé pour déléguer l’accès aux ressources de votre compte de stockage, si vous n’utilisez pas l’autorisation Azure AD. Une signature d’accès partagé est un jeton qui encapsule toutes les informations nécessaires à l’autorisation d’une requête envoyée au stockage Azure via l’URL. Dans le cadre de la signature d’accès partagé, vous pouvez spécifier la ressource de stockage, les autorisations accordées et l’intervalle pendant lequel les autorisations sont valides. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-sas-overview.md).

> [!NOTE]
> L’authentification des utilisateurs et des applications à l’aide des informations d’identification Azure AD est plus sécurisée et plus facile à utiliser que les autres modes d’autorisation. Même si vous pouvez continuer à utiliser l’autorisation de clé partagée avec vos applications, avec Azure AD, vous n’avez plus besoin de stocker votre clé d’accès de compte avec votre code. Vous pouvez également continuer à utiliser des signatures d’accès partagé (SAP) pour accorder un accès affiné aux ressources de votre compte de stockage. Toutefois, Azure AD offre des fonctionnalités similaires sans nécessiter de gestion des jetons SAP, ni de révocation des SAP compromises. 
>
> Microsoft recommande d’utiliser l’autorisation Azure AD pour les applications Stockage Blob et File d’attente Azure, lorsque cela est possible.

## <a name="copying-data-into-a-storage-account"></a>Copie de données dans un compte de stockage

Microsoft fournit des utilitaires et des bibliothèques pour l’importation des données situées sur des appareils de stockage locaux ou dans des fournisseurs de stockage cloud tiers. La solution que vous devez utiliser dépend de la quantité de données à transférer. 

Lorsque vous effectuez une mise à niveau vers un compte de stockage universel v2 à partir d’un compte v1 ou d’un compte de stockage d’objets blob, vos données sont automatiquement transférées. Microsoft recommande cette méthode pour la mise à niveau de votre compte. Toutefois, si vous décidez de déplacer les données d’un compte de stockage universel v1 vers un compte de stockage d’objets blob, vous devrez migrer vos données manuellement, à l’aide des outils et des bibliothèques mentionnés ci-dessous. 

### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour la copie de données hautes performances vers ou à partir d’Azure Storage. Vous pouvez utiliser AzCopy pour copier des données dans votre compte de stockage d’objets blob à partir d’un compte de stockage universel existant, ou pour charger des données à partir de vos appareils de stockage locaux. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

### <a name="data-movement-library"></a>Bibliothèque de déplacement des données

La bibliothèque de déplacement de données Stockage Azure pour .NET est basée sur l’infrastructure principale de déplacement de données sous-tendant AzCopy. La bibliothèque est conçue pour assurer des opérations de transfert de données fiables, simples et hautes performances, comme AzCopy. Cela vous permet de tirer pleinement parti des fonctionnalités offertes par AzCopy dans votre application de façon native, sans avoir à gérer l’exécution et la surveillance des instances externes d’AzCopy. Pour plus d’informations, consultez l’article [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement) (Bibliothèque de déplacement de données Stockage Azure pour .NET)

### <a name="rest-api-or-client-library"></a>API REST ou bibliothèque cliente

Vous pouvez créer une application personnalisée pour migrer vos données vers un compte de stockage d’objets blob à l’aide de l’une des bibliothèques clientes Azure ou de l’API REST des services Azure Storage. Azure Storage offre des bibliothèques clientes enrichies pour une diversité de langages et plateformes, par exemple .NET, Java, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes offrent des fonctionnalités avancées telles que la logique de nouvelle tentative, la journalisation et les téléchargements parallèles. Vous pouvez également développer votre application directement avec l’API REST, qui peut être appelée à l’aide de n’importe quel langage permettant de créer des requêtes HTTP/HTTPS.

Pour plus d’informations sur l’API REST du stockage Azure, consultez [Référence de l’API REST des services Stockage Azure](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Les objets blob chiffrés utilisant le chiffrement côté client stockent les métadonnées relatives au chiffrement avec l’objet blob. Si vous copiez un objet blob chiffré avec le chiffrement côté client, assurez-vous que l’opération de copie conserve les métadonnées de l’objet blob, et en particulier les métadonnées relatives au chiffrement. Si vous copiez un objet blob sans ces métadonnées de chiffrement, le contenu de l’objet blob ne peut plus être récupéré. Pour plus d’informations concernant les métadonnées liées au chiffrement, consultez l’article [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Service Azure Import/Export

Si vous avez une grande quantité de données à importer dans votre compte de stockage, il vous est conseillé d’utiliser le service Import/Export d’Azure. Le service Import/Export est utilisé pour importer des volumes importants de données de manière sécurisée dans le Stockage Blob Azure et Azure Files, en expédiant des lecteurs de disque vers un centre de données Azure. 

Vous pouvez également utiliser ce service pour transférer des données de Stockage Blob Azure vers des lecteurs de disque, et les expédier vers vos sites locaux. Les données d’un ou plusieurs lecteurs de disque peuvent être importées dans le Stockage Blob Azure ou Azure Files. Pour plus d’informations, consultez [Qu’est-ce que le service Azure Import/Export ?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Facturation du compte de stockage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à créer un compte de stockage Azure à usage général, consultez [Créer un compte de stockage](storage-quickstart-create-account.md).
* Pour apprendre à créer un compte BlockBlobStorage, voir [Créer un compte de stockage d’objets blob de blocs](../blobs/storage-blob-create-account-block-blob.md).
* Pour gérer ou supprimer un compte de stockage existant, consultez [Gérer des comptes de stockage Azure](storage-account-manage.md).
