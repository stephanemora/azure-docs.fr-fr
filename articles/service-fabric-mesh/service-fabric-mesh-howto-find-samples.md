---
title: Rechercher des exemples Azure Service Fabric Mesh | Microsoft Docs
description: Découvrez comment rechercher différents exemples d’applications Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: db8c68bf5f9aeb8069044c1344be9f69e498b1b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419558"
---
# <a name="find-service-fabric-mesh-samples"></a>Rechercher des exemples Service Fabric Mesh

Ce tableau décrit les exemples d’applications Service Fabric Mesh disponibles. Le code source de ces exemples montre comment réaliser un scénario particulier à l’aide du modèle de ressource Service Fabric.

Pour plus d’informations sur le déploiement des modèles directement dans Azure, consultez la [page GitHub concernant les exemples de modèles](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md).


|Exemple de modèle|Description du scénario|Code source|Outils de développement|
|------------|--------------------|----------|----------------------|
| [Application Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Page web statique hébergée dans un conteneur. Elle utilise nginx pour Linux et IIS pour Windows. | [Code source](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Aucune exigence |
| [Application de compteur pour les volumes Azure Files](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | État du stockage après le montage d’un volume Azure Files dans le conteneur. <br><br> **Remarque :** Ce modèle nécessite un partage de fichiers Azure Files pour être préprovisionné [Instructions](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Code source](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Créez une application avec un service front-end et back-end qui utilise la résolution DNS. Utilisée pour un tutoriel [ici](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Code source](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh Tooling |
| [Application Visual Objects](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Effectuez la mise à l’échelle et la mise à niveau de microservices au sein d’une application. | [Code source](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh Tooling |
| [Application Voting](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Créez une application avec un service front-end et back-end qui utilise la résolution DNS. | [Code source](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio Mesh Tooling pour la version Windows, Visual Studio Code. L’interface CLI dotnet peut être utilisée pour la version Linux |
| [Application de compteur pour des volumes Service Fabric fiables](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| État du stockage après le montage d’un volume Service Fabric Reliable Disk dans le conteneur.| [Code source](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
