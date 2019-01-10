---
title: Limites des objets et ressources Azure Analysis Services | Microsoft Docs
description: Décrit les limites des objets et ressources Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: beb0c1f9d6b54c029af48684fd81b38699dd2720
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630646"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limites des objets et ressources Analysis Services

Cet article décrit les limites des ressources et objets de modèles.

## <a name="tier-limits"></a>Limites des niveaux

### <a name="developer-tier"></a>Niveau Développeur

Ce niveau est recommandé pour les scénarios d’évaluation, de développement et de test. Un plan inclut les mêmes fonctionnalités que le niveau Standard, mais est limité en termes de puissance de traitement, de nombre d’unités de traitement des requêtes (QPU) et de taille de mémoire. L'évolutivité du réplica de requête n'est pas disponible pour ce niveau. Ce niveau ne propose pas de contrat SLA.

|Planification  |QPU (unités de traitement des requêtes)  |Mémoire (Go)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Niveau de base

Le niveau est recommandé pour les solutions de production avec de petits modèles tabulaires, une simultanéité limitée des utilisateurs et des besoins simples en matière d’actualisation des données. L'évolutivité du réplica de requête *n'est pas disponible* pour ce niveau. Les fonctionnalités de perspectives, de partitions multiples et de modèle tabulaire DirectQuery ne sont pas prises en charge dans ce niveau.  

|Planification  |QPU (unités de traitement des requêtes)  |Mémoire (Go)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Niveau standard

Ce niveau est destiné aux applications de production stratégiques qui nécessitent une simultanéité élastique des utilisateurs et ont des modèles de données à la croissance rapide. Il prend en charge l’actualisation des données avancée pour des mises à jour du modèle de données quasiment en temps réel, et prend en charge toutes les fonctionnalités de modélisation tabulaire.

|Planification  |QPU (unités de traitement des requêtes)  |Mémoire (Go)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Non disponible dans toutes les régions.  

## <a name="object-limits"></a>Limites des objets

Ces limites sont théoriques. Les performances réelles sont moindres.

|Object|Tailles/Nombres max.|  
|------------|----------------------------|  
|Bases de données d’une instance|16 000|  
|Nombre total de tables et de colonnes d’une base de données|16 000|  
|Lignes d’une table|Illimité<br /><br /> **Avertissement :** avec la restriction selon laquelle aucune colonne de la table ne peut contenir plus de 1 999 999 997 valeurs distinctes.|  
|Hiérarchies d’une table|15 999|  
|Niveaux d’une hiérarchie|15 999|  
|Relations|8 000|  
|Colonnes clés dans toutes les tables|15 999|  
|Mesures contenues dans les tables|2^31-1 = 2 147 483 647|  
|Cellules retournées par une requête|2^31-1 = 2 147 483 647|  
|Taille d’enregistrement de la requête source|64 K|  
|Longueur des noms d’objet|512 caractères|  


