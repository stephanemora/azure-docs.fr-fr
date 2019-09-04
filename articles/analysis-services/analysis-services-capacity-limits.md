---
title: Limites des objets et ressources Azure Analysis Services | Microsoft Docs
description: Décrit les limites des objets et ressources Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 40a5b68a12724f2574af19bb10c276c54c5afba0
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997599"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limites des objets et ressources Analysis Services

Cet article décrit les limites des ressources et objets de modèles.

## <a name="tier-limits"></a>Limites des niveaux

Pour QPU et les limites de mémoire des niveaux développeur, de base et standard, consultez la [page de tarification Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

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


