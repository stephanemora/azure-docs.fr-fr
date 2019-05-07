---
title: Présentation de Stockage Azure - Stockage cloud sur Azure | Microsoft Docs
description: Le Stockage Azure est une solution de stockage cloud de Microsoft. Le Stockage Azure offre un stockage des objets de données hautement disponible, sécurisé, fiable, évolutif et redondant.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 175cc03dac21280a5e3ef3ec130cb94bc800f540
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153901"
---
# <a name="introduction-to-azure-storage"></a>Présentation de Stockage Azure

Le Stockage Azure est une solution de stockage cloud de Microsoft pour les scénarios de stockage de données actuels. Le Stockage Azure offre un magasin d’objets hautement évolutif pour des objets de données, un service de système de fichiers pour le cloud, un magasin de messagerie pour une messagerie fiable et un magasin NoSQL. Le Stockage Azure est :

- **Durable et hautement disponible.** La redondance garantit que vos données sont sécurisées en cas de défaillance matérielle temporaire. Vous pouvez également choisir de répliquer des données entre des centres de données ou des régions géographiques pour une protection supplémentaire contre les catastrophes locales ou les catastrophes naturelles. Les données ainsi répliquées restent hautement disponibles en cas de panne inattendue. 
- **Sécurisé.** Toutes les données écrites dans le Stockage Azure sont chiffrées par le service. Le Stockage Azure vous permet de contrôler de manière plus précise qui a accès à vos données.
- **Évolutif.** Le Stockage Azure est conçu pour être hautement évolutif afin de répondre aux besoins de stockage de données et de performances des applications actuelles. 
- **Géré.** Microsoft Azure gère la maintenance du matériel, les mises à jour et les problèmes critiques pour vous.
- **Accessible.** Les données dans le Stockage Azure sont accessibles n’importe où dans le monde via HTTP ou HTTPS. Microsoft propose des Kits de développement logiciel (DSK) pour le Stockage Azure dans une variété de langages : .NET, Java, Node.js, Python, PHP, Ruby, Go et autres encore, ainsi qu’API REST éprouvée. Le stockage Azure prend en charge l’écriture de scripts Azure PowerShell ou l’interface de ligne de commande Azure. De plus, le portail Azure et l’Explorateur Stockage Azure offrent des solutions visuelles simples pour utiliser vos données.  

## <a name="azure-storage-services"></a>Services de stockage Azure

Le Stockage Azure inclut les services de données suivants : 

- [Objets blob Azure](../blobs/storage-blobs-introduction.md) : magasin d’objets hautement scalable pour les données texte ou binaires.
- [Azure Files](../files/storage-files-introduction.md) : partages de fichiers gérés pour les déploiements sur le cloud ou locaux.
- [Files d’attente Azure](../queues/storage-queues-introduction.md) : magasin de messagerie pour une messagerie fiable entre les composants d’application. 
- [Tables Azure](../tables/table-storage-overview.md) : magasin NoSQL pour le stockage sans schéma de données structurées.

Chaque service est accessible via un compte de stockage. Pour commencer, consultez [Créer un compte de stockage](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Stockage d'objets blob

Le stockage Blob Azure est la solution de stockage d’objet de Microsoft pour le cloud. Le stockage Blob est optimisé pour stocker de grandes quantités de données non structurées, telles que des données texte ou binaires. 

Le stockage Blob est idéal pour :

* Mise à disposition d’images ou de documents directement dans un navigateur.
* Stockage de fichiers pour un accès distribué.
* Diffusion en continu de vidéo et d’audio.
* Stockage de données pour la sauvegarde et la restauration, la récupération d’urgence et l’archivage.
* Stockage des données pour l’analyse par un service local ou hébergé par Azure.

Les objets du stockage Blob sont accessibles n’importe où dans le monde via HTTP ou HTTPS. Les utilisateurs ou applications clientes peuvent accéder aux objets blob via des URL, l’[API REST Stockage Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) ou une bibliothèque de client Stockage Azure. Les bibliothèques de client de stockage sont disponibles dans plusieurs langages, tels que [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) et [Ruby](https://azure.github.io/azure-storage-ruby).

Pour plus d’informations sur le stockage Blob, consultez [Présentation du Stockage Blob](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files
[Azure Files](../files/storage-files-introduction.md) vous permet de configurer les partages de fichiers réseau hautement disponibles qui sont accessibles à l’aide du protocole Server Message Block (SMB) standard. Cela signifie que plusieurs machines virtuelles peuvent partager les mêmes fichiers avec accès en lecture et en écriture. Vous pouvez également consulter les fichiers à l’aide de l’interface REST ou des bibliothèques clientes de stockage.

Ce qui distingue Azure Files des fichiers sur un partage de fichiers d’entreprise est le fait de pouvoir accéder aux fichiers depuis n’importe où dans le monde, à l’aide d’une URL qui pointe vers le fichier et inclut un jeton de signature (SAP) d’accès partagé. Vous pouvez générer des jetons SAP, qui autorisent un accès spécifique à une ressource privée pour une durée définie.

Les partages de fichiers peuvent être utilisés dans de nombreux scénarios courants :

* Par exemple, de nombreuses applications locales utilisent les partages de fichiers. Cette fonctionnalité simplifie la migration de ces applications qui partagent des données vers Azure. Si vous montez le partage de fichiers sur la même lettre de lecteur que celle utilisée par l’application locale, la partie de votre application qui accède au partage de fichiers doit fonctionner, avec très peu de modifications à apporter (voire aucune).

* Les fichiers de configuration peuvent être stockés sur un partage de fichiers et sont accessibles à partir de plusieurs machines virtuelles. Les outils et utilitaires utilisés par plusieurs développeurs d’un groupe peuvent être stockés sur un partage de fichiers, en veillant à ce que chacun puisse les trouver et à ce qu’ils utilisent la même version.

* Les journaux de diagnostic, les mesures et les vidages sur incident sont trois exemples de données qui peuvent être écrites dans un partage de fichiers et traitées ou analysées ultérieurement.

À ce jour, les listes de contrôle d’accès (ACL) et l’authentification basée sur Active Directory ne sont pas prises en charge, mais elles le seront à l’avenir. Les informations d’identification de compte de stockage sont utilisées pour l’authentification d’accès au partage de fichiers. Cela signifie que toute personne avec le partage monté aura un accès complet en lecture/écriture au partage.

Pour plus d’informations sur Azure Files, consultez [Présentation d’Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Stockage de files d'attente

Le service de files d’attente Azure sert à stocker et à récupérer des messages. La taille maximale des messages de file d’attente est de 64 Ko et une file d’attente peut contenir des millions de messages. Les files d’attente servent en général à stocker des listes de messages qui seront traités de façon asynchrone.

Par exemple, vous souhaitez que vos clients soient en mesure de charger des images, et vous souhaitez créer des miniatures de chaque image. La procédure normale serait de faire patienter votre client, le temps que vous créiez les miniatures tout en chargeant les images. L’utilisation d’une file d’attente est une autre possibilité. Lorsque le client a terminé le chargement d’images, écrivez un message dans la file d’attente. Utilisez ensuite une fonction Azure afin de récupérer le message dans la file d’attente pour créer les miniatures. Chaque étape de ce processus peut être développée séparément, vous offrant ainsi plus de contrôle lorsque vous le réglez.

Pour plus d’informations sur les files d’attentes Azure, consultez [Introduction to Queues](../queues/storage-queues-introduction.md) (Présentation des files d’attente).

## <a name="table-storage"></a>Stockage de tables

Stockage de tables Azure fait maintenant partie d’Azure Cosmos DB. Pour consulter la documentation Stockage de tables Azure, consultez [Vue d’ensemble du stockage de table Azure](../tables/table-storage-overview.md). En plus du service Stockage de tables Azure existant, il existe une nouvelle API de Table d’Azure Cosmos DB qui propose des tables optimisées pour le débit, la distribution globale et les index secondaires automatiques. Pour en savoir plus et essayer la nouvelle expérience premium, consultez [API Table d’Azure Cosmos DB](https://aka.ms/premiumtables).

Pour plus d’informations sur le stockage de table, consultez [Overview of Azure Table storage](../tables/table-storage-overview.md) (Vue d’ensemble du stockage de table Azure).

## <a name="disk-storage"></a>Stockage sur disque

Le stockage Azure comprend également des fonctionnalités de disque géré et non géré utilisées par des machines virtuelles. Pour en savoir plus sur ces fonctionnalités, consultez la [documentation Compute Service](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Pour plus d’informations sur les types de comptes de stockage, voir [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md). 

## <a name="securing-access-to-storage-accounts"></a>Sécurisation de l’accès aux comptes de stockage

Chaque demande vers le stockage Azure doit être autorisé. Stockage Azure prend en charge les méthodes d’autorisation suivantes :

- **Intégration d’Azure Active Directory (Azure AD) pour les données d’objet blob et file d’attente.** Stockage Azure prend en charge l’authentification et autorisation avec les informations d’identification Azure AD pour les services Blob et file d’attente par le biais de contrôle d’accès en fonction du rôle (RBAC). Autorisation des requêtes avec Azure AD est recommandée pour la sécurité et facilité d’utilisation. Pour plus d’informations, consultez [authentifier l’accès à Azure d’objets BLOB et files d’attente à l’aide d’Azure Active Directory](storage-auth-aad.md).
- **Autorisation AD Azure sur SMB pour les fichiers Azure (version préliminaire).** Azure Files prend en charge l’autorisation basée sur l’identité sur SMB (Server Message Block) via Azure Active Directory Domain Services. Votre domaine Windows virtual machines virtuelles peuvent accéder aux partages de fichiers Azure à l’aide des informations d’identification Azure AD. Pour plus d’informations, consultez [autorisation de vue d’ensemble d’Azure Active Directory sur SMB pour les fichiers Azure (aperçu)](../files/storage-files-active-directory-overview.md).
- **Autorisation avec la clé partagée.** Les services Azure Storage Blob, file d’attente et Table Azure Files prend en charge d’autorisation avec client Key.A partagé à l’aide de la clé partagée autorisation transmet un en-tête avec chaque requête qui est signé à l’aide de la clé d’accès de compte stockage. Pour plus d’informations, consultez [Autoriser avec une clé partagée](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **À l’aide de l’autorisation accès signatures partagé (SAP).** Une signature d’accès partagé (SAP) est une chaîne contenant un jeton de sécurité qui peut être ajouté à l’URI pour une ressource de stockage. Le jeton de sécurité encapsule les contraintes telles que les autorisations et l’intervalle d’accès. Pour plus d’informations, consultez [à l’aide de l’accès Signatures partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).
- **Accès anonyme aux conteneurs et objets BLOB.** Un conteneur et ses objets BLOB peut-être être publiquement disponibles. Lorsque vous spécifiez qu’un conteneur ou un objet blob est public, tout le monde peut lire de manière anonyme ; Aucune authentification n’est requise. Pour en savoir plus, consultez la section [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Chiffrement

Il existe deux types de chiffrement de base pour les services de stockage. Pour en savoir plus sur la sécurité et le chiffrement, consultez le [guide de sécurité pour le stockage Azure](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Chiffrement au repos

Le Chiffrement du service de stockage Azure au repos permet de protéger vos données pour garantir le respect des engagements de votre organisation en matière de sécurité et de conformité. Avec cette fonctionnalité, Azure Storage chiffre automatiquement vos données avant de les rendre persistantes dans le stockage et les déchiffre avant la récupération. La gestion du chiffrement, du déchiffrement et des clés est totalement transparente pour les utilisateurs.


Le chiffrement du service de stockage chiffre automatiquement les données pour tous les niveaux de performance (Standard ou Premium), tous les modèles de déploiement (Azure Resource Manager et Classic) et tous les services de Stockage Azure (blob, file d’attente, table et fichier). Le chiffrement du service de stockage n’affecte pas les performances de Stockage Azure.

Pour plus d’informations sur le chiffrement du service de stockage, consultez [Azure Storage Service Encryption pour les données au repos](storage-service-encryption.md).

### <a name="client-side-encryption"></a>chiffrement côté client

Les bibliothèques clientes de stockage ont des méthodes que vous pouvez appeler pour chiffrer des données par programme avant de les envoyer du client vers Azure. Les données sont chiffrées et stockées, ce qui signifie qu’elles sont également chiffrées au repos. À la lecture des données au retour, vous déchiffrez les informations après réception.

Pour plus d’informations sur le chiffrement côté client, consultez [Chiffrement côté client avec .NET pour Stockage Microsoft Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redondance

Pour assurer la durabilité de vos données, le Stockage Azure réplique plusieurs copies de vos données. Quand vous configurez votre compte de stockage, vous sélectionnez une option de redondance. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Pour plus d’informations sur la reprise d’activité, consultez [Reprise d’activité et basculement de compte de stockage (préversion) dans Stockage Azure](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transfert de données vers et depuis Azure Storage

Vous avez plusieurs options de déplacement des données dans ou en dehors de Stockage Azure. L’option que vous choisissez dépend de la taille de votre jeu de données et de votre bande passante réseau. Pour plus d’informations, consultez [Choisir une solution Azure pour le transfert de données](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Tarifs

Pour en savoir plus sur la tarification du stockage Azure, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Outils, bibliothèques et API de stockage
Les ressources Azure Storage sont accessibles par n’importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, Stockage Azure offre des bibliothèques de programmation pour plusieurs langages répandus. Ces bibliothèques simplifient l’utilisation du stockage Azure sous de nombreux aspects en gérant des détails tels que l’invocation synchrone et asynchrone, le traitement par lots des opérations, la gestion des exceptions, les nouvelles tentatives automatiques, le comportement opérationnel, etc. Des bibliothèques sont actuellement disponibles pour les langages et les plateformes suivants (d’autres sont à l’étude) :

### <a name="azure-storage-data-api-and-library-references"></a>Références d’API et de bibliothèque de données de stockage Azure
* [API REST des services de stockage](https://docs.microsoft.com/rest/api/storageservices/)
* [Bibliothèque cliente de stockage pour .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Bibliothèque cliente de stockage pour Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Bibliothèque cliente de stockage pour Node.js](https://docs.microsoft.com/javascript/api/azure-storage)
* [Bibliothèque cliente de stockage pour Python](https://github.com/Azure/azure-storage-python)
* [Bibliothèque cliente de stockage pour PHP](https://github.com/Azure/azure-storage-php)
* [Bibliothèque cliente de stockage pour Ruby](https://github.com/Azure/azure-storage-ruby)
* [Bibliothèque cliente de stockage pour C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Références d’API et de bibliothèque de gestion du stockage Azure
* [API REST des fournisseurs de ressources de stockage](https://docs.microsoft.com/rest/api/storagerp/)
* [Bibliothèque cliente des fournisseurs de ressources de stockage pour .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [API REST de gestion des services de stockage (classique)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Références d’API et de bibliothèque de déplacement de données de stockage Azure
* [API REST du service Import/Export Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Bibliothèque cliente de déplacement de données de stockage pour .NET](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Outils et utilitaires
* [Applets de commande Azure PowerShell pour le stockage](https://docs.microsoft.com/powershell/module/az.storage)
* [Applets de commande Azure CLI pour le stockage](https://docs.microsoft.com/cli/azure/storage)
* [Utilitaire de ligne de commande AzCopy](https://aka.ms/downloadazcopy)
* L’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
* [Outils clients d’Azure Storage](../storage-explorers.md)
* [Outils de développement Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Étapes suivantes

Pour démarrer et exécuter avec le stockage Azure, consultez la section [Créer un compte de stockage](storage-quickstart-create-account.md).
