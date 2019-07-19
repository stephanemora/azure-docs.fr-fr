---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/01/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e878ca23b9187fe3175ad0af1b4f27e59e1deef6
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509843"
---
### <a name="premium-performance-block-blob-storage"></a>Stockage d’objets blob de blocs de performances Premium

Un compte de stockage Premium d’objets blob de bloc est optimisé pour les applications qui utilisent des objets plus petits, de l’ordre du kilo-octet. C’est l’idéal pour les applications nécessitant des taux de transaction élevés ou un stockage à faible latence constante. Le stockage Premium d’objets blob par blocs est conçu pour évoluer avec vos applications. Si vous envisagez de déployer des applications qui nécessitent des centaines de milliers de requêtes par seconde ou plusieurs pétaoctets de capacité de stockage, veuillez nous contacter en envoyant une demande de support dans le [Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage"></a>Performances Premium FileStorage

[!INCLUDE [azure-storage-limits-filestorage](azure-storage-limits-filestorage.md)]

 Pour les objectifs Premium de mise à l’échelle de partage de fichiers, reportez-vous à la section [Premium files scale targets (Objectifs Premium de mise à l’échelle de fichiers)](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets).

### <a name="premium-performance-page-blob-storage"></a>Stockage Premium d’objets blob de pages

Les comptes de stockage Premium GPv1 ou GPv2 présentent les objectifs d’extensibilité suivants :

| Capacité totale des comptes                            | Bande passante totale pour un compte de stockage localement redondant                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacité du disque : 35 To <br>Capacité d’instantané : 10 To | Jusqu’à 50 Go par seconde pour les données entrantes <sup>1</sup> et sortantes <sup>2</sup> |

<sup>1</sup> Toutes les données (demandes) envoyées à un compte de stockage

<sup>2</sup> Toutes les données (réponses) reçues d’un compte de stockage

Si vous utilisez des comptes de stockage Premium pour des disques non managés et si votre application dépasse les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez envisager la migration vers des disques managés. Si vous ne souhaitez pas migrer vers des disques managés, générez votre application pour utiliser plusieurs comptes de stockage. Ensuite, partitionnez vos données sur ces comptes de stockage. Par exemple, si vous souhaitez attacher des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage. La limite pour un compte de stockage Premium unique est de 35 To. Vérifiez qu’un compte de stockage Premium n’a jamais plus de 35 To de disques configurés.