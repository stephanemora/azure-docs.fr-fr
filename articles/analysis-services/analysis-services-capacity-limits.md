---
title: Limites des objets et ressources Azure Analysis Services | Microsoft Docs
description: Cet article décrit les limites des objets et ressources pour un serveur Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573210"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limites des objets et ressources Analysis Services

Cet article décrit les limites des ressources et objets de modèles.

## <a name="tier-limits"></a>Limites des niveaux

Pour QPU et les limites de mémoire des niveaux développeur, de base et standard, consultez la [page de tarification Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limites des objets

Ces limites sont théoriques. Les performances réelles sont moindres.

|Object|Tailles maximales/nombres maximaux|  
|------------|----------------------------|  
|Bases de données dans une instance|16 000|  
|Nombre total de tables et de colonnes d’une base de données|16 000|  
|Lignes dans une table|Illimité<br /><br /> **Avertissement :** avec la restriction selon laquelle aucune colonne de la table ne peut contenir plus de 1 999 999 997 valeurs distinctes.|  
|Hiérarchies dans une table|15 999|  
|Niveaux dans une hiérarchie|15 999|  
|Relations|8,000|  
|Colonnes clés dans toutes les tables|15 999|  
|Mesures contenues dans les tables|2^31-1 = 2 147 483 647|  
|Cellules renvoyées par une requête|2^31-1 = 2 147 483 647|  
|Taille d’enregistrement de la requête source|64 K|  
|Longueur des noms d’objet|512 caractères|  


