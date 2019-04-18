---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737070"
---
| Ressource | Cible        |
|----------|---------------|
| Taille maximale du conteneur d’objets blob unique | Identique à la capacité du compte de stockage maximale |
| Nombre maximal de blocs dans un bloc d’objets blob ou ajouter des objets blob | 50 000 blocs |
| Taille maximale d’un bloc dans un objet blob de blocs | 100 Mio |
| Taille maximale d’un objet blob de blocs | 50 000 x 100 Mio (environ 4,75 TIO) |
| Taille maximale d’un bloc dans un objet blob d’ajout | 4 Mio |
| Taille maximale d’un objet blob d’ajout | 50 000 x 4 Mio (environ 195 Gio) |
| Taille maximale d’un objet blob de pages | 8 Tio |
| Nombre maximal de stratégies d’accès stockées par conteneur d’objets blob | 5. |
|Débit cible pour un objet blob unique |Jusqu'à des limites d’entrées/sorties de compte de stockage<sup>1</sup> |

<sup>1</sup> débit d’objet unique dépend de plusieurs facteurs, y compris, mais sans limitation : d’accès concurrentiel, taille de l’opération, niveau de performance, la vitesse de la source pour les téléchargements et de destination pour les téléchargements.