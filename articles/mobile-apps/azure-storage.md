---
title: Stockage cloud pour créer des applications hautement sécurisées, durables et scalables avec Stockage Azure
description: Découvrez les services permettant de stocker de gros volumes de données d’application mobile structurées et non structurées dans le cloud.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240973"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Stockage cloud pour des applications hautement sécurisées, durables et scalables avec Stockage Azure
Le service [Stockage Azure](https://azure.microsoft.com/services/storage/) est la solution de stockage cloud de Microsoft pour les applications récentes qui offre un magasin d’objets hautement scalable pour des objets de données, un service de système de fichiers pour le cloud, un magasin de messagerie pour une messagerie fiable et un magasin NoSQL. Le Stockage Azure est :
- **Durable et hautement disponible :** La redondance garantit que vos données sont sécurisées en cas de défaillance matérielle temporaire. Vous pouvez également choisir de répliquer des données entre des centres de données ou des régions géographiques pour une protection supplémentaire contre les catastrophes locales ou les catastrophes naturelles. Les données ainsi répliquées restent hautement disponibles en cas de panne inattendue.
- **Sécurisé :** Toutes les données écrites dans le Stockage Azure sont chiffrées par le service. Le Stockage Azure vous permet de contrôler de manière plus précise qui a accès à vos données.
- **Évolutifs :** Les services sont conçus pour être hautement scalables afin de répondre aux besoins de stockage de données et de performances des applications actuelles.
- **Managé :** Azure gère la maintenance du matériel, les mises à jour et les problèmes critiques pour vous.
- **Accessible:** Les données sont accessibles n’importe où dans le monde via HTTP ou HTTPS. Microsoft fournit des bibliothèques clientes dans plusieurs langages, comme .NET, Java, Node.js, Python, PHP, Ruby et Go, ainsi qu’une API REST avancée. Les scripts sont pris en charge dans Azure PowerShell et Azure CLI. Le portail Azure et l’Explorateur Stockage Azure offrent des solutions visuelles simples pour utiliser vos données.

Utilisez les services suivants pour activer le stockage cloud dans vos applications mobiles.

## <a name="azure-blob-storage"></a>Stockage Blob Azure
Le [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) est une solution de stockage d’objets pour le cloud. Le stockage d’objets Blob est optimisé pour stocker de grandes quantités de données non structurées qui n’obéissent pas à un modèle ou une définition de données en particulier, comme des données texte ou binaires. Il prend en charge la diversité des langues utilisées par les bibliothèques clientes. Le stockage Blob est conçu pour :
- Mettre à disposition des images ou documents directement dans un navigateur.
- Stocker des fichiers pour un accès distribué.
- Diffuser du contenu audio et vidéo.
- Écrire dans les fichiers journaux.
- Stockage de données pour la sauvegarde et la restauration, la récupération d’urgence et l’archivage.
- Stockage des données pour l’analyse par un service local ou hébergé par Azure.

**Informations de référence**
- [Azure portal](https://portal.azure.com)
- [Documentation du stockage Blob Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Démarrages rapides](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Exemples](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Stockage de tables Azure
[Stockage Table Azure](https://azure.microsoft.com/services/storage/tables/) est un service qui stocke des données NoSQL structurées dans le cloud et fournit un magasin de clés ou d’attributs reposant sur une conception sans schéma. Le stockage de table Azure permet de stocker de grandes quantités de données structurées. Il s’agit d’un magasin de données NoSQL qui accepte les appels authentifiés provenant de l’intérieur et de l’extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Le stockage de table est généralement utilisé pour :
- Stocker des téraoctets de données structurées capables de servir des applications web.
- Stocker des jeux de données ne nécessitant pas de jonctions complexes, de clés étrangères ni de procédures stockées, et pouvant être dénormalisés pour un accès rapide.
- L’interrogation rapide des données à l’aide d’un index cluster.
- Accéder aux données à l’aide du protocole OData et de requêtes LINQ avec les bibliothèques .NET Windows Communication Foundation (WCF) Data Services.

Vous pouvez utiliser le stockage Table Azure pour stocker et interroger de grands ensembles de données non relationnelles et structurées. Vos tableaux évoluent à mesure que la demande augmente.

**Informations de référence**
- [Azure portal](https://portal.azure.com)
- [Documentation Stockage Table Azure](/azure/storage/tables/table-storage-overview)
- [Exemples](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Démarrages rapides](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Avec [Azure Files](https://azure.microsoft.com/services/storage/files/), vous pouvez configurer les partages de fichiers réseau hautement disponibles qui sont accessibles à l’aide du protocole Server Message Block (SMB) standard. Plusieurs machines virtuelles peuvent partager les mêmes fichiers avec accès en lecture et en écriture. Vous pouvez également consulter les fichiers à l’aide de l’interface REST ou des bibliothèques clientes de stockage. Vous pouvez accéder aux fichiers depuis n’importe où dans le monde à l’aide d’une URL qui pointe vers le fichier et inclut un jeton de signature d’accès partagé (SAS). Vous pouvez générer des jetons SAS. Ils permettent un accès spécifique à une ressource privée pour une durée définie.

Les partages de fichiers Azure peuvent être utilisés pour :
- **Remplacer ou compléter les serveurs de fichiers locaux :** Les systèmes d’exploitation courants tels que Windows, Mac OS et Linux peuvent monter directement des partages Azure Files, n’importe où dans le monde. Les partages de fichiers Azure peuvent également être répliqués avec Azure File Sync vers des serveurs Windows, localement ou dans le cloud, pour une mise en cache performante et distribuée des données là où elles sont utilisées.
- **Migration lift-and-shift des applications :** Migrez vers le cloud des applications qui exigent un partage de fichiers pour stocker les données utilisateur ou d’application de fichier.
- **Simplifier le développement cloud :** Azure Files permet aussi de simplifier de plusieurs façons les nouveaux projets de développement cloud. Par exemple :
    - **Paramètres d’application partagés :** Pour les applications distribuées, les fichiers de configuration sont souvent centralisés à un emplacement accessible par différentes instances d’application. Les instances d’application peuvent charger leur configuration par le biais de l’API REST File. Les utilisateurs peuvent y accéder en fonction de leurs besoins en montant le partage SMB localement.
    - **Partage de diagnostic :** Un partage de fichiers Azure est un emplacement pratique où les applications cloud peuvent écrire leurs journaux d’activité, mesures et images mémoire. Les journaux peuvent être écrits par les instances d’application par le biais de l’API REST File. Les développeurs peuvent y accéder en montant le partage de fichiers sur leur machine locale. Cette fonctionnalité offre une grande souplesse. Les développeurs peuvent adopter le développement cloud sans abandonner les outils existants qu’ils connaissent.

**Informations de référence**
- [Azure portal](https://portal.azure.com)
- [Documentation Azure Files](/azure/storage/files/storage-files-introduction)
- [Démarrages rapides](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Stockage File d’attente Azure
Le service [Stockage File d’attente Azure](https://azure.microsoft.com/services/storage/queues/) est un service permettant de stocker un grand nombre de messages. Vous accédez aux messages depuis n’importe où dans le monde par le biais d’appels authentifiés à l’aide du protocole HTTP ou HTTPS. La taille maximale d’un message de file d’attente est de 64 Ko. Une file d’attente peut contenir des millions de messages, dans la limite de la capacité totale d’un compte de stockage. Les files d’attente sont couramment utilisées pour créer un backlog de travail à traiter de façon asynchrone.

**Informations de référence**
- [Azure portal](https://portal.azure.com)
- [Documentation de Stockage File d’attente Azure](/azure/storage/queues/)
- [Démarrages rapides](/azure/storage/queues/storage-quickstart-queues-portal)
- [Exemples](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
