---
title: Conventions de nommage pour les tailles de machine virtuelle Azure
description: Explique les convention d’affectation de noms utilisées pour les tailles de machines virtuelles Azure
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131716"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Conventions de dénomination des tailles de machines virtuelles Azure

Cette page met en avant les convention d’affectation de noms utilisées pour les machines virtuelles Azure. Les machines virtuelles utilisent ces conventions d’affection de noms pour désigner des fonctionnalités et des spécifications différentes.

## <a name="naming-convention-explanation"></a>Explication ce la convention d’affectation de noms

**[Famille]**  +  **[Sous-famille*]**  +  **[nombre de processeurs virtuels]**  +  **[Fonctionnalités supplémentaires]**  +  **[Type d’accélérateur*]**  +  **[Version]**

|Valeur | Explication|
|---|---|
| Standard, de base ou expérimental | « Standard » est la valeur par défaut affectée à toutes les tailles de machines virtuelles à disponibilité générale | 
| Famille | Indique la série de la famille de machines virtuelles| 
| *Sous-famille | Utilisé uniquement pour différencier des machines virtuelles spécialisées|
| Nombre de processeurs virtuels| Indique le nombre de processeurs virtuels de la machine virtuelle |
| Fonctionnalités supplémentaires | Une ou plusieurs lettres minuscules indiquent des fonctionnalités supplémentaires, telles que : <br> a = processeur basé sur AMD <br> d = disque (disque temporaire local présent) ; ceci concerne les nouvelles machines virtuelles Azure, consultez [Séries Ddv4 et Ddsv4](./ddv4-ddsv4-series.md) <br> h = compatible avec la mise en veille prolongée <br> i = isolé <br> l = mémoire insuffisante <br> m = utilisation intensive de la mémoire <br> t = très petite mémoire <br> r = RDMA <br> s = capacité de stockage Premium, y compris l’utilisation possible de [SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Remarque : certaines tailles plus récentes sans l’attribut de s peuvent toujours prendre en charge le stockage Premium, par exemple M128, M64, etc.)<br> |
| *Type d’accélérateur | Indique le type d’accélérateur matériel dans les références (SKU) spécialisées/GPU. Seules les nouvelles références (SKU) spécialisées/GPU lancées à partir du troisième trimestre 2020 auront l’accélérateur matériel dans leur nom. |
| Version | Indique la version de la série de machines virtuelles |

## <a name="example-breakdown"></a>Exemple de répartition

**[Famille]**  +  **[Sous-famille*]**  +  **[nombre de processeurs virtuels]**  +  **[Fonctionnalités supplémentaires]**  +  **[Type d’accélérateur*]**  +  **[Version]**

### <a name="example-1-m416ms_v2"></a>Exemple 1 : M416ms_v2

|Valeur | Explication|
|---|---|
| Famille | M | 
| Nombre de processeurs virtuels | 416 |
| Fonctionnalités supplémentaires | m = utilisation intensive de la mémoire <br> s = capacité de stockage Premium |
| Version | v2 |

### <a name="example-2-nv16as_v4"></a>Exemple 2 : NV16as_v4

|Valeur | Explication|
|---|---|
| Famille | N | 
| Sous-famille | V |
| Nombre de processeurs virtuels | 16 |
| Fonctionnalités supplémentaires | a = processeur basé sur AMD <br> s = capacité de stockage Premium |
| Version | v4 |

### <a name="example-3-nc4as_t4_v3"></a>Exemple 3 : NC4as_T4_v3

|Valeur | Explication|
|---|---|
| Famille | N | 
| Sous-famille | C |
| Nombre de processeurs virtuels | 4 |
| Fonctionnalités supplémentaires | a = processeur basé sur AMD <br> s = capacité de stockage Premium |
| Type d’accélérateur | T4 |
| Version | v3 |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [tailles des machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) disponibles dans Azure. 
