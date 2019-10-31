---
title: Stockage cloud pour créer des applications hautement sécurisées, durables et scalables avec Stockage Azure
description: Découvrez les services permettant de stocker de gros volumes de données d’application mobile structurées et non structurées dans le cloud.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795133"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Stockage cloud pour des applications hautement sécurisées, durables et scalables avec Stockage Azure
Le service [Stockage Azure](https://azure.microsoft.com/services/storage/) est la solution de stockage cloud de Microsoft pour les applications récentes qui offre un magasin d’objets hautement scalable pour des objets de données, un service de système de fichiers pour le cloud, un magasin de messagerie pour une messagerie fiable et un magasin NoSQL. Le Stockage Azure est :
- **Durable et hautement disponible.** La redondance garantit que vos données sont sécurisées en cas de défaillance matérielle temporaire. Vous pouvez également choisir de répliquer des données entre des centres de données ou des régions géographiques pour une protection supplémentaire contre les catastrophes locales ou les catastrophes naturelles. Les données ainsi répliquées restent hautement disponibles en cas de panne inattendue.
- **Sécurisé.** Toutes les données écrites dans le Stockage Azure sont chiffrées par le service. Le Stockage Azure vous permet de contrôler de manière plus précise qui a accès à vos données.
- **Scalable.** Les services sont conçus pour être hautement scalables afin de répondre aux besoins de stockage de données et de performances des applications actuelles.
- **Géré**. Azure gère la maintenance du matériel, les mises à jour et les problèmes critiques pour vous.
- Les données sont **accessibles** n’importe où dans le monde via HTTP ou HTTPS. Microsoft fournit des bibliothèques clientes dans plusieurs langages, dont .NET, Java, Node.js, Python, PHP, Ruby, Go et d’autres encore, ainsi qu’une API REST avancée. Les scripts sont pris en charge dans Azure PowerShell ou Azure CLI. De plus, le portail Azure et l’Explorateur Stockage Azure offrent des solutions visuelles simples pour utiliser vos données.

Utilisez les services suivants pour activer le stockage cloud dans vos applications mobiles.

## <a name="azure-blob-storage"></a>un stockage Azure Blob
Le service [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) offre une solution de stockage d’objets pour le cloud et est optimisé pour stocker de grandes quantités de données non structurées qui n’obéissent pas à un modèle ou une définition de données en particulier, comme des données texte ou binaires. Il prend en charge les bibliothèques clientes dans plusieurs langages et est conçu pour :
- Mise à disposition d’images ou de documents directement dans un navigateur.
- Stockage de fichiers pour un accès distribué.
- Diffusion en continu de vidéo et d’audio.
- Écriture dans les fichiers journaux.
- Stockage de données pour la sauvegarde et la restauration, la récupération d’urgence et l’archivage.
- Stockage des données pour l’analyse par un service local ou hébergé par Azure.

**Informations de référence**
- [Portail Azure](https://portal.azure.com)
- [Documentation](/azure/storage/blobs/storage-blobs-introduction)
- [Démarrages rapides](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Exemples](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Stockage de table Azure
Le service [Stockage Table Azure](https://azure.microsoft.com/services/storage/tables/) est un service qui stocke des données NoSQL structurées dans le cloud, en fournissant un magasin de clés/d’attributs reposant sur une conception sans schéma. Le stockage de table Azure permet de stocker de grandes quantités de données structurées. Il s’agit d’un magasin de données NoSQL qui accepte les appels authentifiés provenant de l’intérieur et de l’extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Voici quelques utilisations courantes du stockage de table :
- Stockage de téraoctets de données structurées capables de servir des applications web.
- Stockage de jeux de données ne nécessitant pas de jonctions complexes, de clés étrangères ni de procédures stockées, et pouvant être dénormalisés pour un accès rapide.
- Interrogation rapide des données à l’aide d’un index cluster.
- Accès aux données avec le protocole OData et requêtes LINQ avec les bibliothèques .NET du service de données WCF.

Vous pouvez utiliser le stockage de table pour stocker et interroger de grands ensembles de données non relationnelles structurées. Vos tables évoluent en même temps que la demande.

**Informations de référence**
- [Portail Azure](https://portal.azure.com)
- [Documentation](/azure/storage/tables/table-storage-overview)
- [Exemples](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Démarrages rapides](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
[Azure Files](https://azure.microsoft.com/services/storage/files/) vous permet de configurer les partages de fichiers réseau hautement disponibles qui sont accessibles à l’aide du protocole Server Message Block (SMB) standard. Plusieurs machines virtuelles peuvent partager les mêmes fichiers avec accès en lecture et en écriture. Vous pouvez également consulter les fichiers à l’aide de l’interface REST ou des bibliothèques clientes de stockage. Vous pouvez accéder aux fichiers depuis n’importe où dans le monde à l’aide d’une URL qui pointe vers le fichier et inclut un jeton de signature d’accès partagé (SAS). Vous pouvez générer des jetons SAP, qui autorisent un accès spécifique à une ressource privée pour une durée définie.

Les partages de fichiers Azure peuvent être utilisés pour :
- Remplacer ou compléter les serveurs de fichiers locaux : Les systèmes d’exploitation courants tels que Windows, Mac OS et Linux peuvent monter directement des partages Azure Files, n’importe où dans le monde. Les partages de fichiers Azure peuvent également être répliqués avec Azure File Sync vers des serveurs Windows, localement ou dans le cloud, pour une mise en cache performante et distribuée des données là où elles sont utilisées.
- **Développer et transférer des applications** dans le cloud qui exigent un partage de fichiers pour stocker les données utilisateur ou d’application de fichier.
- **Simplifier le développement cloud** : Azure Files permet aussi de simplifier de plusieurs façons les nouveaux projets de développement cloud. Par exemple :
    - Paramètres d’application partagés : Pour les applications distribuées, les fichiers de configuration sont souvent centralisés à un emplacement accessible par différentes instances d’application. Les instances de l’application peuvent charger leur configuration via l’API REST File et l’utilisateur peut y accéder en fonction des besoins en montant le partage SMB localement.
    - Partage de diagnostic : Un partage de fichiers Azure est un emplacement pratique où les applications cloud peuvent écrire leurs journaux d’activité, mesures et images mémoire. Les journaux d’activité peuvent être écrits par les instances de l’application via l’API REST File, et les développeurs peuvent y accéder en montant le partage de fichiers sur leur ordinateur local. Cela permet une grande souplesse, car les développeurs peuvent adopter le développement cloud sans abandonner les outils existants qu’ils apprécient.

**Informations de référence**
- [Portail Azure](https://portal.azure.com)
- [Documentation](/azure/storage/files/storage-files-introduction)
- [Démarrages rapides](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Files d'attente Azure
Le service [Stockage File d’attente Azure](https://azure.microsoft.com/services/storage/queues/) est un service permettant de stocker un grand nombre de messages. Vous accédez aux messages depuis n’importe où dans le monde par le biais d’appels authentifiés à l’aide du protocole HTTP ou HTTPS. Un message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Les files d’attente sont couramment utilisées pour créer un backlog de travail à traiter de façon asynchrone.

**Informations de référence**
- [Portail Azure](https://portal.azure.com)
- [Documentation](/azure/storage/queues/)
- [Démarrages rapides](/azure/storage/queues/storage-quickstart-queues-portal)
- [Exemples](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
