---
title: GATK Resource Bundle
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données GATK Resource Bundle dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 6ee4987e21acc280a9835bec3457b567f689433f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285121"
---
# <a name="gatk-resource-bundle"></a>GATK Resource Bundle

[GATK Resource Bundle](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle) est un ensemble de fichiers standard permettant d’utiliser des données de reséquençage humain avec GATK.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données est une copie du magasin de données sur https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

1. datasetgatkbestpractices : 542 Go
1. datasetgatklegacybundles : 61 Go
1. datasetgatktestdata : 2 To
1. datasetpublicbroadref : 477 Go
1. datasetbroadpublic : 3 To

Les jeux de données sont mis à jour tous les mois durant la première semaine de chaque mois.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. L’allocation de ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest est recommandée à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

1. datasetgatkbestpractices

    USA Ouest 2 : « https://datasetgatkbestpractices.blob.core.windows.net/dataset  »
    
    USA Centre-Ouest : « https://datasetgatkbestpractices-secondary.blob.core.windows.net/dataset »
    
    [Jeton SAS](../storage/common/storage-sas-overview.md) : ?sv=2020-04-08&si=prod&sr=c&sig=6SaDfKtXAIfdpO%2BkvNA%2FsTNmNij%2Byh%2F%2F%2Bf98WAUqs7I%3D

2. datasetgatklegacybundles

    USA Ouest 2 : « https://datasetgatklegacybundles.blob.core.windows.net/dataset  »
    
    USA Centre-Ouest : « https://datasetgatklegacybundles-secondary.blob.core.windows.net/dataset »
    
    [Jeton SAS](../storage/common/storage-sas-overview.md) : ?sv=2020-04-08&si=prod&sr=c&sig=xBfxOPBqHKUCszzwbNCBYF0k9osTQjKnZbEjXCW7gU0%3D

3. datasetgatktestdata

    USA Ouest 2 : « https://datasetgatktestdata.blob.core.windows.net/dataset  »
    
    USA Centre-Ouest : « https://datasetgatktestdata-secondary.blob.core.windows.net/dataset »
    
    [Jeton SAS](../storage/common/storage-sas-overview.md) : ?sv=2020-04-08&si=prod&sr=c&sig=fzLts1Q2vKjuvR7g50vE4HteEHBxTcJbNvf%2FZCeDMO4%3D

4. datasetpublicbroadref
    
    USA Ouest 2 : « https://datasetpublicbroadref.blob.core.windows.net/dataset  »
    
    USA Centre-Ouest : « https://datasetpublicbroadref-secondary.blob.core.windows.net/dataset »
    
    [Jeton SAS](../storage/common/storage-sas-overview.md) : ?sv=2020-04-08&si=prod&sr=c&sig=DQxmjB4D1lAfOW9AxIWbXwZx6ksbwjlNkixw597JnvQ%3D

5. datasetbroadpublic

    USA Ouest 2 : « https://datasetpublicbroadpublic.blob.core.windows.net/dataset  »
    
    USA Centre-Ouest : « https://datasetpublicbroadpublic-secondary.blob.core.windows.net/dataset »
    
    [Jeton SAS](../storage/common/storage-sas-overview.md) : ?sv=2020-04-08&si=prod&sr=c&sig=u%2Bg2Ab7WKZEGiAkwlj6nKiEeZ5wdoJb10Az7uUwis%2Fg%3D

## <a name="use-terms"></a>Conditions d’utilisation

Consultez le [site officiel GATK Resource Bundle](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle).

## <a name="contact"></a>Contact

Consultez le [site officiel GATK Resource Bundle](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle).

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).