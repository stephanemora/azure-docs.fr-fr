---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1cb353d22e39447d7aeb723b5506ac0209cf74d8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287100"
---
### <a name="premium-performance-block-blob-storage"></a>Stockage blob par blocs performances Premium

Un compte stockage blob de bloc de performances premium est optimisé pour les applications qui utilisent plus petits en kilo-octets, plage d’objets. Elle est idéale pour les applications nécessitant des taux de transaction élevés ou constante du stockage à faible latence. Stockage blob par blocs performances Premium est conçu pour évoluer avec vos applications. Si vous envisagez de déployer des applications qui nécessitent des centaines de milliers de demandes par seconde ou de plusieurs pétaoctets de capacité de stockage, veuillez nous contacter en envoyant une demande de prise en charge dans les [Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage-preview"></a>Performances Premium FileStorage (version préliminaire)

Fichiers Premium utilisent un compte de stockage unique appelé **FileStorage (version préliminaire)**. Ce type de compte est conçu pour les charges de travail avec un nombre élevé d’IOPS, des débits élevés avec une latence faible homogène. Stockage de fichiers Premium s’adapte à la taille du partage configuré.

|Domaine  |Cible  |
|---------|---------|
|Taille maximale     |5 To (version préliminaire publique), 100 TIO (version préliminaire publique limitée)     |
|Partages   |Illimité  |
|E/S par seconde     |100 000 (version préliminaire publique limitée)    |
|Bande passante|5 Go/s     |

 Pour premium partage de fichiers cibles de mise à l’échelle, consultez le [fichiers Premium mettre à l’échelle cibles](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) section.

### <a name="premium-performance-page-blob-storage"></a>Stockage blob de page de performances Premium

Performances Premium, à usage général v1 ou v2 les comptes de stockage ont les objectifs d’évolutivité suivants :

| Capacité totale des comptes                            | Bande passante totale pour un compte de stockage localement redondant                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacité du disque : 35 To <br>Capacité d’instantané : 10 To | Jusqu’à 50 Go par seconde pour les données entrantes <sup>1</sup> et sortantes <sup>2</sup> |

<sup>1</sup> Toutes les données (demandes) envoyées à un compte de stockage

<sup>2</sup> Toutes les données (réponses) reçues d’un compte de stockage

Si vous utilisez des comptes de stockage de performances premium pour disques non gérés et que votre application dépasse les objectifs d’évolutivité d’un compte de stockage unique, vous pouvez choisir de migrer vers des disques gérés. Si vous ne souhaitez pas migrer vers des disques gérés, générez votre application pour utiliser plusieurs comptes de stockage. Ensuite, partitionnez vos données sur ces comptes de stockage. Par exemple, si vous souhaitez attacher des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage. La limite pour un compte de stockage Premium unique est de 35 To. Assurez-vous qu’un compte de stockage de performances premium n’a jamais plus de 35 To de disques alloués.