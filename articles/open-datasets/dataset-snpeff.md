---
title: SnpEff
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données SnpEff dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 8072d3a48e0bcc0fea040a964aac579dc2061deb
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982381"
---
# <a name="snpeff-genomic-variant-annotations-and-functional-effect-prediction-toolbox"></a>SnpEff : Outils de prédiction d’effet fonctionnel et d’annotation de variantes génomiques

[SnpEff](https://pcingola.github.io/SnpEff/)Outils de prédiction d’effet fonctionnel et d’annotation de variantes génomiques. Il annote et prédit les effets des variantes génétiques sur les gènes et les protéines (par exemple les modifications d’acides aminés).

Pour plus d’informations sur les données, consultez le [Manuel de l’utilisateur](https://pcingola.github.io/SnpEff/se_introduction/).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données est une copie de http://downloads.sourceforge.net/project/snpeff/databases/

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

Ce jeu de données contient approximativement 2 To de données et est mis à jour mensuellement.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. L’allocation de ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest est recommandée à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

USA Ouest 2 : « https://datasetsnpeff.blob.core.windows.net/dataset  »

USA Centre-Ouest : « https://datasetsnpeff-secondary.blob.core.windows.net/dataset »

[Jeton SAS](../storage/common/storage-sas-overview.md) : sv=2019-10-10&st=2020-09-01T00%3A00%3A00Z&se=2050-09-01T00%3A00%3A00Z&si=prod&sr=c&sig=isafOa9tGnYBAvsXFUMDGMTbsG2z%2FShaihzp7JE5dHw%3D

## <a name="use-terms"></a>Conditions d’utilisation
Les données sont disponibles sans restrictions. Plus d’informations, notamment sur les citations, consultez [Consultation et utilisation des données dans ClinVar](https://pcingola.github.io/SnpEff/se_introduction/).

## <a name="contact"></a>Contact

Si vous avez des questions ou des commentaires sur ce jeu de données, contactez [Pablo Cingolani](http://www.linkedin.com/in/pablocingolani).


## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
