---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67177275"
---
| Ressource | Cible        |
|----------|---------------|
| Taille maximale du conteneur d’objets blob unique | Identique à la capacité maximale du compte de stockage |
| Nombre maximal de blocs dans un objet blob de blocs ou ajouter des objets blob | 50 000 blocs |
| Taille maximale d’un bloc dans un objet blob de blocs | 100 Mio |
| Taille maximale d’un objet blob de blocs | 50 000 x 100 Mio (environ 4,75 Tio) |
| Taille maximale d’un bloc dans un objet blob d’ajout | 4 Mio |
| Taille maximale d’un objet blob d’ajout | 50 000 x 4 Mio (environ 195 Gio) |
| Taille maximale d’un objet blob de pages | 8 Tio |
| Nombre maximal de stratégies d’accès stockées par conteneur d’objets blob | 5\. |
|Débit cible pour un objet blob unique |Jusqu’à la limite d’entrée/sortie du compte de stockage<sup>1</sup> |

<sup>1</sup> Le débit cible d’un objet unique dépend de plusieurs facteurs, dont et sans s’y limiter : concurrence, taille de la demande, niveau de performance, vitesse des chargements à la source et des téléchargements à la destination. Pour tirer parti des améliorations de performances des [objets blob de bloc à haut débit](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), utilisez une taille de demande Put Blob ou Put Block inférieure à 4 MiB (inférieure à 256 Kio pour le stockage d’objets blob de bloc ou le stockage Data Lake Gen2 avec performances premium).
