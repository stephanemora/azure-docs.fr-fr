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
ms.openlocfilehash: 9c0fcd7923d03ed4086e424e3a8ec86fc74df4af
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111526953"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Conventions de dénomination des tailles de machines virtuelles Azure

Cette page met en avant les convention d’affectation de noms utilisées pour les machines virtuelles Azure. Les machines virtuelles utilisent ces conventions d’affection de noms pour désigner des fonctionnalités et des spécifications différentes.

## <a name="naming-convention-explanation"></a>Explication ce la convention d’affectation de noms

**[Famille]**  +  **[Sous-famille*]**  +  **[nombre de processeurs virtuels]**  +  **[Processeurs virtuels avec contraintes*]**  +  **[Fonctionnalités supplémentaires]**  +  **[Type d’accélérateur*]**  +  **[Version]**

|Valeur | Explication|
|---|---|
| Famille | Indique la série de la famille de machines virtuelles| 
| *Sous-famille | Utilisé uniquement pour différencier des machines virtuelles spécialisées|
| Nombre de processeurs virtuels| Indique le nombre de processeurs virtuels de la machine virtuelle |
| *Processeurs virtuels avec contraintes| Utilisé pour certaines tailles de machine virtuelle uniquement. Indique le nombre de processeurs virtuels pour la [taille des processeurs virtuels avec contraintes](./constrained-vcpu.md) |
| Fonctionnalités supplémentaires | Une ou plusieurs lettres minuscules indiquent des fonctionnalités supplémentaires, telles que : <br> a = processeur basé sur AMD <br> d = diskfull (disque temporaire local présent) ; ceci concerne les nouvelles machines virtuelles Azure, consultez [Séries Ddv4 et Ddsv4](./ddv4-ddsv4-series.md). <br> i = taille isolée <br> l = mémoire insuffisante ; une quantité de mémoire inférieure à la taille d’utilisation intensive de la mémoire <br> m = utilisation intensive de la mémoire ; la plus grande quantité de mémoire dans une taille particulière <br> t = très petite mémoire ; la plus petite quantité de mémoire dans une taille particulière <br> s = capacité de stockage Premium, y compris l’utilisation possible de [SSD Ultra](./disks-types.md#ultra-disk) (Remarque : certaines tailles plus récentes sans l’attribut de s peuvent toujours prendre en charge le stockage Premium, par exemple M128, M64, etc.)<br> |
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

### <a name="example-4-m8-2ms_v2-constrained-vcpu"></a>Exemple 4 : M8-2ms_v2 (processeur virtuel avec contraintes)

|Valeur | Explication|
|---|---|
| Famille | M | 
| Nombre de processeurs virtuels | 8 |
| Nombre de processeurs virtuels avec contraintes (réels) | 2 |
| Fonctionnalités supplémentaires | m = utilisation intensive de la mémoire <br> s = capacité de stockage Premium |
| Version | v2 |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [tailles des machines virtuelles](./sizes.md) disponibles dans Azure.
