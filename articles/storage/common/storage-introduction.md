---
title: Présentation de Stockage Azure - Stockage cloud sur Azure | Microsoft Docs
description: Le Stockage Azure est une solution de stockage cloud de Microsoft. Le Stockage Azure offre un stockage des objets de données hautement disponible, sécurisé, fiable, évolutif et redondant.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79228573"
---
# <a name="introduction-to-azure-storage"></a>Présentation de Stockage Azure

Le Stockage Azure est une solution de stockage cloud de Microsoft pour les scénarios de stockage de données actuels. Le Stockage Azure offre un magasin d’objets hautement évolutif pour des objets de données, un service de système de fichiers pour le cloud, un magasin de messagerie pour une messagerie fiable et un magasin NoSQL. Le Stockage Azure est :

- **Durable et hautement disponible.** La redondance garantit que vos données sont sécurisées en cas de défaillance matérielle temporaire. Vous pouvez également choisir de répliquer des données entre des centres de données ou des régions géographiques pour une protection supplémentaire contre les catastrophes locales ou les catastrophes naturelles. Les données ainsi répliquées restent hautement disponibles en cas de panne inattendue.
- **Sécurisé.** Toutes les données écrites dans le Stockage Azure sont chiffrées par le service. Le Stockage Azure vous permet de contrôler de manière plus précise qui a accès à vos données.
- **Évolutif.** Le Stockage Azure est conçu pour être hautement évolutif afin de répondre aux besoins de stockage de données et de performances des applications actuelles.
- **Géré.** Microsoft Azure gère la maintenance du matériel, les mises à jour et les problèmes critiques pour vous.
- **Accessible.** Les données dans le Stockage Azure sont accessibles n’importe où dans le monde via HTTP ou HTTPS. Microsoft fournit des bibliothèques clientes pour le Stockage Azure dans une variété de langages, dont .NET, Java, Node.js, Python, PHP, Ruby, Go et autres encore, ainsi qu’une API REST avancée. Le stockage Azure prend en charge l’écriture de scripts Azure PowerShell ou l’interface de ligne de commande Azure. De plus, le portail Azure et l’Explorateur Stockage Azure offrent des solutions visuelles simples pour utiliser vos données.  

## <a name="azure-storage-services"></a>Services de stockage Azure

Le Stockage Azure inclut les services de données suivants :

- [Objets blob Azure](../blobs/storage-blobs-introduction.md) : magasin d’objets hautement scalable pour les données texte ou binaires.
- [Azure Files](../files/storage-files-introduction.md) : partages de fichiers gérés pour les déploiements sur le cloud ou locaux.
- [Files d’attente Azure](../queues/storage-queues-introduction.md) : magasin de messagerie pour une messagerie fiable entre les composants d’application.
- [Tables Azure](../tables/table-storage-overview.md) : magasin NoSQL pour le stockage sans schéma de données structurées.

Chaque service est accessible via un compte de stockage. Pour commencer, consultez [Créer un compte de stockage](storage-account-create.md).

## <a name="blob-storage"></a>Stockage d'objets blob

Le stockage Blob Azure est la solution de stockage d’objet de Microsoft pour le cloud. Le stockage Blob est optimisé pour stocker de grandes quantités de données non structurées, telles que des données texte ou binaires.

Le stockage Blob est idéal pour :

- Mise à disposition d’images ou de documents directement dans un navigateur.
- Stockage de fichiers pour un accès distribué.
- Diffusion en continu de vidéo et d’audio.
- Stockage de données pour la sauvegarde et la restauration, la récupération d’urgence et l’archivage.
- Stockage des données pour l’analyse par un service local ou hébergé par Azure.

Les objets du stockage Blob sont accessibles n’importe où dans le monde via HTTP ou HTTPS. Les utilisateurs ou applications clientes peuvent accéder aux objets blob via des URL, l’[API REST Stockage Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) ou une bibliothèque de client Stockage Azure. Les bibliothèques de client de stockage sont disponibles dans plusieurs langages, tels que [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) et [Ruby](https://azure.github.io/azure-storage-ruby).

Pour plus d’informations sur le stockage Blob, consultez [Présentation du Stockage Blob](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files

[Azure Files](../files/storage-files-introduction.md) vous permet de configurer les partages de fichiers réseau hautement disponibles qui sont accessibles à l’aide du protocole Server Message Block (SMB) standard. Cela signifie que plusieurs machines virtuelles peuvent partager les mêmes fichiers avec accès en lecture et en écriture. Vous pouvez également consulter les fichiers à l’aide de l’interface REST ou des bibliothèques clientes de stockage.

Ce qui distingue Azure Files des fichiers sur un partage de fichiers d’entreprise est le fait de pouvoir accéder aux fichiers depuis n’importe où dans le monde, à l’aide d’une URL qui pointe vers le fichier et inclut un jeton de signature (SAP) d’accès partagé. Vous pouvez générer des jetons SAP, qui autorisent un accès spécifique à une ressource privée pour une durée définie.

Les partages de fichiers peuvent être utilisés dans de nombreux scénarios courants :

- Par exemple, de nombreuses applications locales utilisent les partages de fichiers. Cette fonctionnalité simplifie la migration de ces applications qui partagent des données vers Azure. Si vous montez le partage de fichiers sur la même lettre de lecteur que celle utilisée par l’application locale, la partie de votre application qui accède au partage de fichiers doit fonctionner, avec très peu de modifications à apporter (voire aucune).

- Les fichiers de configuration peuvent être stockés sur un partage de fichiers et sont accessibles à partir de plusieurs machines virtuelles. Les outils et utilitaires utilisés par plusieurs développeurs d’un groupe peuvent être stockés sur un partage de fichiers, en veillant à ce que chacun puisse les trouver et à ce qu’ils utilisent la même version.

- Les journaux de diagnostic, les mesures et les vidages sur incident sont trois exemples de données qui peuvent être écrites dans un partage de fichiers et traitées ou analysées ultérieurement.

À ce jour, les listes de contrôle d’accès (ACL) et l’authentification basée sur Active Directory ne sont pas prises en charge, mais elles le seront à l’avenir. Les informations d’identification de compte de stockage sont utilisées pour l’authentification d’accès au partage de fichiers. Cela signifie que toute personne avec le partage monté aura un accès complet en lecture/écriture au partage.

Pour plus d’informations sur Azure Files, consultez [Présentation d’Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Stockage de files d'attente

Le service de files d’attente Azure sert à stocker et à récupérer des messages. La taille maximale des messages de file d’attente est de 64 Ko et une file d’attente peut contenir des millions de messages. Les files d’attente servent en général à stocker des listes de messages qui seront traités de façon asynchrone.

Par exemple, vous souhaitez que vos clients soient en mesure de charger des images, et vous souhaitez créer des miniatures de chaque image. La procédure normale serait de faire patienter votre client, le temps que vous créiez les miniatures tout en chargeant les images. L’utilisation d’une file d’attente est une autre possibilité. Lorsque le client a terminé le chargement, écrivez un message dans la file d’attente. Utilisez ensuite une fonction Azure afin de récupérer le message dans la file d’attente pour créer les miniatures. Chaque étape de ce processus peut être développée séparément, vous offrant ainsi plus de contrôle lorsque vous le réglez.

Pour plus d’informations sur les files d’attentes Azure, consultez [Introduction to Queues](../queues/storage-queues-introduction.md) (Présentation des files d’attente).

## <a name="table-storage"></a>Stockage de tables

Stockage de tables Azure fait maintenant partie d’Azure Cosmos DB. Pour consulter la documentation Stockage de tables Azure, consultez [Vue d’ensemble du stockage de table Azure](../tables/table-storage-overview.md). En plus du service Stockage de tables Azure existant, il existe une nouvelle API de Table d’Azure Cosmos DB qui propose des tables optimisées pour le débit, la distribution globale et les index secondaires automatiques. Pour en savoir plus et essayer la nouvelle expérience premium, consultez [API Table d’Azure Cosmos DB](https://aka.ms/premiumtables).

Pour plus d’informations sur le stockage de table, consultez [Overview of Azure Table storage](../tables/table-storage-overview.md) (Vue d’ensemble du stockage de table Azure).

## <a name="disk-storage"></a>Stockage sur disque

Un disque managé Azure est un disque dur virtuel (VHD). Vous pouvez le voir comme un disque physique sur un serveur local mais virtualisé. Les disques managés Azure sont stockés en tant qu’objet blob de page, qui sont un objet de stockage d’E/S aléatoire dans Azure. Nous appelons un disque managé « managé » car il s’agit d’une abstraction sur les objets blob de pages, les conteneurs d’objets de couleur et les comptes de stockage Azure. Avec les disques managés, il vous suffit d’approvisionner le disque et Azure s’occupe du reste.

Pour plus d’informations sur les disques managés, voir [Présentation des disques managés Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Pour plus d’informations sur les types de comptes de stockage, voir [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Sécuriser l’accès aux comptes de stockage

Chaque demande adressée au Stockage Azure doit être autorisée. Stockage Azure prend en charge les méthodes d’autorisation suivantes :

- **Intégration d’Azure Active Directory (Azure AD) pour les données Blob et de files d’attente.** Le Stockage Azure prend en charge l’authentification et l’autorisation avec Azure AD pour les services Blob et File d’attente via un contrôle d’accès en fonction du rôle (RBAC). L’autorisation des demandes avec Azure AD est recommandée pour davantage de sécurité et de facilité d’utilisation. Pour plus d’informations, voir [Autoriser l’accès aux objets blob et files d’attente Azure à l’aide d’Azure Active Directory](storage-auth-aad.md).
- **Autorisation Azure AD sur SMB pour Azure Files (préversion).** Azure Files prend en charge l’authentification basée sur l’identité sur SMB (Server Message Block) avec Azure Active Directory Domain Services. Vos machines virtuelles Windows jointes à un domaine peuvent accéder aux partages de fichiers Azure en utilisant des informations d’identification Azure AD. Pour plus d’informations, voir [Vue d’ensemble de l’autorisation Azure Active Directory sur SMB pour Azure Files (préversion)](../files/storage-files-active-directory-overview.md).
- **Autorisation avec Clé partagée.** Les services Azure Storage Blob, File d’attente et Table, ainsi qu’Azure Files prennent en charge l’autorisation avec client partagée. Un client qui utilise une clé partagée transmet avec chaque demande un en-tête signé à l’aide de la clé d’accès au compte de stockage. Pour plus d’informations, consultez [Autoriser avec une clé partagée](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorisation à l’aide de signatures d’accès partagé (SAP).** Une signature d’accès partagé (SAP) est une chaîne contenant un jeton de sécurité pouvant être ajouté à l’URI pour une ressource de stockage. Le jeton de sécurité encapsule des contraintes telles que les autorisations et l’intervalle d’accès. Pour plus d’informations, voir [Utilisation des signatures d’accès partagé (SAP)](storage-sas-overview.md).
- **Accès anonyme aux conteneurs et objets blob.** Un conteneur et ses objets blob peuvent être publiquement disponibles. Lorsque vous spécifiez qu’un conteneur ou un objet blob est public, n’importe qui peut le lire de manière anonyme ; aucune authentification n’est requise. Pour en savoir plus, consultez la section [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Chiffrement

Il existe deux types de chiffrement de base pour les services de stockage. Pour en savoir plus sur la sécurité et le chiffrement, consultez le [guide de sécurité pour le stockage Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Chiffrement au repos

Le chiffrement de Stockage Azure protège et sauvegarde vos données afin de vous permettre de respecter les engagements de votre organisation en matière de sécurité et de conformité. Stockage Azure chiffre automatiquement vos données avant de les conserver sur le compte de stockage, et les déchiffre avant leur récupération. Les processus de gestion du chiffrement, du déchiffrement et des clés sont totalement transparents pour les utilisateurs. Les clients peuvent également choisir de gérer leurs propres clés à l’aide d’Azure Key Vault. Pour plus d'informations, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](storage-service-encryption.md).

### <a name="client-side-encryption"></a>chiffrement côté client

Les bibliothèques clientes de Stockage Azure fournissent des méthodes pour chiffrer les données à partir de la bibliothèque cliente avant de les envoyer sur le réseau et de déchiffrer la réponse. Les données chiffrées via le chiffrement côté client sont également chiffrées au repos par Stockage Azure. Pour plus d’informations sur le chiffrement côté client, voir [Chiffrement côté client avec .NET pour Stockage zure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redondance

Pour assurer la durabilité de vos données, Stockage Azure stocke plusieurs copies de vos données. Quand vous configurez votre compte de stockage, vous sélectionnez une option de redondance.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Transfert de données vers et depuis Azure Storage

Vous avez plusieurs options de déplacement des données dans ou en dehors de Stockage Azure. L’option que vous choisissez dépend de la taille de votre jeu de données et de votre bande passante réseau. Pour plus d’informations, consultez [Choisir une solution Azure pour le transfert de données](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Tarifs

Pour en savoir plus sur la tarification du stockage Azure, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Outils, bibliothèques et API de stockage

Les ressources Azure Storage sont accessibles par n’importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, Stockage Azure offre des bibliothèques de programmation pour plusieurs langages répandus. Ces bibliothèques simplifient l’utilisation du stockage Azure sous de nombreux aspects en gérant des détails tels que l’invocation synchrone et asynchrone, le traitement par lots des opérations, la gestion des exceptions, les nouvelles tentatives automatiques, le comportement opérationnel, etc. Des bibliothèques sont actuellement disponibles pour les langages et les plateformes suivants (d’autres sont à l’étude) :

### <a name="azure-storage-data-api-and-library-references"></a>Références d’API et de bibliothèque de données de stockage Azure

- [API REST d’Azure Storage](https://docs.microsoft.com/rest/api/storageservices/)
- [Bibliothèque cliente Stockage Azure pour .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Bibliothèque cliente Stockage Azure pour Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Bibliothèque cliente Stockage Azure pour Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Bibliothèque cliente Stockage Azure pour Python](https://github.com/Azure/azure-storage-python)
- [Bibliothèque cliente Stockage Azure pour PHP](https://github.com/Azure/azure-storage-php)
- [Bibliothèque cliente Stockage Azure pour Ruby](https://github.com/Azure/azure-storage-ruby)
- [Bibliothèque cliente Stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Références d’API et de bibliothèque de gestion du stockage Azure

- [API REST des fournisseurs de ressources de stockage](https://docs.microsoft.com/rest/api/storagerp/)
- [Bibliothèque cliente des fournisseurs de ressources de stockage pour .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST de gestion des services de stockage (classique)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Références d’API et de bibliothèque de déplacement de données de stockage Azure

- [API REST du service Import/Export Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Bibliothèque cliente de déplacement de données de stockage pour .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Outils et utilitaires

- [Applets de commande Azure PowerShell pour le stockage](https://docs.microsoft.com/powershell/module/az.storage)
- [Applets de commande Azure CLI pour le stockage](https://docs.microsoft.com/cli/azure/storage)
- [Utilitaire de ligne de commande AzCopy](https://aka.ms/downloadazcopy)
- L’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
- [Outils clients d’Azure Storage](../storage-explorers.md)
- [Outils de développement Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Étapes suivantes

Pour démarrer et exécuter avec le stockage Azure, consultez la section [Créer un compte de stockage](storage-account-create.md).
