---
title: Quand utiliser des objets BLOB Azure, des fichiers Azure ou des disques Azure
description: Découvrez les différentes façons de stocker les données dans Azure et d’y accéder pour choisir la technologie la mieux adaptée.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671042"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Quand utiliser des objets BLOB Azure, des fichiers Azure ou des disques Azure

Microsoft Azure propose plusieurs fonctionnalités dans le stockage Azure pour stocker vos données dans le cloud et y accéder. Cet article traite des fichiers, des objets BLOB et des disques Azure, afin de vous aider à choisir entre ces fonctionnalités.

## <a name="scenarios"></a>Scénarios

Le tableau suivant compare les fichiers, les objets blob et les disques, et il présente des exemples de scénarios appropriés pour chacun.

| Fonctionnalité | Description | Quand l’utiliser |
|--------------|-------------|-------------|
| **Azure Files** | Fournit une interface SMB, des bibliothèques clientes et une [interface REST](/rest/api/storageservices/file-service-rest-api) qui permet d’accéder en tout lieu aux fichiers stockés. | Vous souhaitez développer et transférer une application dans le cloud qui utilise déjà les API du système de fichiers natif pour partager des données avec d’autres applications s’exécutant dans Azure.<br/><br/>Vous souhaitez stocker les outils de développement et de débogage qui doivent être accessibles à partir de nombreuses machines virtuelles. |
| **Objets blob Azure** | Fournit des bibliothèques de client et une [interface REST](/rest/api/storageservices/blob-service-rest-api) qui permet de stocker des données non structurées et d’y accéder à grande échelle dans des objets blob de blocs.<br/><br/>Prend également en charge [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pour les solutions d’analytique d’entreprise Big Data. | Vous souhaitez que votre application prenne en charge le streaming et l’accès aléatoire.<br/><br/>Vous souhaitez être en mesure d’accéder aux données d’application à partir de n’importe quel endroit.<br/><br/>Vous souhaitez générer un Data Lake d’entreprise sur Azure et effectuer une analytique de Big Data. |
| **Disques Azure** | Fournit des bibliothèques clientes et une [interface REST](/rest/api/compute/manageddisks/disks/disks-rest-api) qui permet de stocker de manière permanente les données et d’y accéder à partir d’un disque dur virtuel joint. | Vous souhaitez développer et transférer des applications qui utilisent les API du système de fichiers natif pour lire et écrire des données sur des disques persistants.<br/><br/>Vous souhaitez stocker des données dont l’accès n’est pas requis à l’extérieur de la machine virtuelle à laquelle le disque est joint. |


## <a name="next-steps"></a>Étapes suivantes

Lors des décisions concernant le stockage et l’accès de vos données, vous devez également prendre en considération les coûts impliqués. Pour plus d’informations, consultez [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Certaines fonctionnalités SMB ne sont pas applicables au cloud. Pour plus d’informations, consultez [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Fonctionnalités non prises en charge par le service Azure File).
 
Pour plus d’informations sur les blobs Azure, consultez notre article [Qu’est-ce qu’un stockage blob Azure ?](../blobs/storage-blobs-overview.md).

Pour plus d'informations sur le stockage sur disque, consultez [Présentation des disques managés](../../virtual-machines/windows/managed-disks-overview.md).

Pour plus d’informations sur Azure Files, consultez notre article [Planification d’un déploiement Azure Files](../files/storage-files-planning.md).