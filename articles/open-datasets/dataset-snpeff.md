---
title: SnpEff
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données SnpEff dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 3cc23edff82dce0c14a795e1b3b0a29975cb96e4
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038585"
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

USA Ouest 2 : https://datasetsnpeff.blob.core.windows.net/dataset

USA Centre-Ouest : https://datasetsnpeff-secondary.blob.core.windows.net/dataset

[Jeton SAS](../storage/common/storage-sas-overview.md) : sv=2019-10-10&st=2020-09-01T00%3A00%3A00Z&se=2050-09-01T00%3A00%3A00Z&si=prod&sr=c&sig=isafOa9tGnYBAvsXFUMDGMTbsG2z%2FShaihzp7JE5dHw%3D

## <a name="use-terms"></a>Conditions d’utilisation
Les données sont disponibles sans restrictions. Pour plus d’informations et de détails sur les citations, consultez [Accès et utilisation des données dans ClinVar](https://pcingola.github.io/SnpEff/SnpEff_manual.html#intro).

## <a name="contact"></a>Contact

Si vous avez des questions ou des commentaires sur ce jeu de données, contactez [Pablo Cingolani](http://www.linkedin.com/in/pablocingolani).


## <a name="next-steps"></a>Étapes suivantes

Affichez les autres jeux de données dans le [Catalogue Open Datasets](dataset-catalog.md).