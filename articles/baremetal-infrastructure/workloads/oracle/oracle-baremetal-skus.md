---
title: Références SKU BareMetal pour les charges de travail Oracle
description: En savoir plus sur les références SKU pour les charges de travail de BareMetal Infrastructure pour Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588851"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>Références SKU BareMetal pour les charges de travail Oracle

Dans cet article, nous allons examiner les références SKU de BareMetal Infrastructure spécialisées pour les charges de travail Oracle.

Les références SKU BareMetal Infrastructure pour Oracle vont de deux à quatre sockets. Vous pouvez également choisir parmi différents nombres de cœurs d’UC et tailles de mémoire pour répondre aux besoins de votre charge de travail. Voici un tableau résumant les fonctionnalités des références SKU disponibles.
 
| **Matériel** **certifié Oracle** | **Modèle** | **Mémoire totale** | **Stockage** | **Disponibilité** |
| --- | --- | --- | --- | --- |
| YES | SAP HANA sur Azure S32m- 2 processeurs Intel® Xeon® I6234 16 cœurs de processeur et 32 threads de processeur | 1,5 To | --- | Disponible |
| YES | SAP HANA sur Azure S64m – 4 processeurs Intel® Xeon® I6234 à 32 cœurs de processeur et 64 threads de processeur | 3 TO | --- | Disponible |
| YES | SAP HANA sur Azure S96 – 2 x processeurs Intel® Xeon® E7-8890 v4 à 48 cœurs de processeur et 96 threads de processeur | 768 Go | 3 TO | Disponible |
| YES | SAP HANA sur Azure S224 – 4 processeurs Intel® Xeon® Platinum 8276 à 112 cœurs de processeur et 224 threads de processeur | 3 TO | 6,3 To | Disponible |
| YES | SAP HANA sur Azure S224m – 4 processeurs Intel® Xeon® Platinum 8276 à 112 cœurs de processeur et 224 threads de processeur | 6,0 To | 10,5 To | Disponible |

- Cœurs de processeur = somme des cœurs de processeur hors hyper-thread (le nombre total de processeurs physiques) de l’unité de serveur. 
- Threads d’UC = somme des threads de calcul fournis par les cœurs d’UC multithreads (le nombre total de processeurs logiques) de l’unité de serveur. La plupart des unités sont configurées par défaut pour utiliser la technologie Hyper-Threading.
- Les serveurs sont dédiés aux clients.
- Le client dispose d’un accès racine (pas d’hyperviseur).
- Les serveurs ne sont pas directement sur des réseaux virtuels Azure.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le stockage proposé par BareMetal Infrastructure pour Oracle.

> [!div class="nextstepaction"]
> [Stockage sur BareMetal pour les charges de travail Oracle](oracle-baremetal-storage.md)
