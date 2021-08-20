---
title: 1 000 génomes
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données 1000 Genomes dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 52f948526b1575f43d722c856251eed83b2e66ef
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982768"
---
# <a name="1000-genomes"></a>1 000 génomes

Le projet 1000 Genomes s’est déroulé entre 2008 et 2015, créant ainsi le plus grand catalogue public de la variation humaine et des données de génotype. Le jeu de données final contient des données pour 2 504 individus de 26 populations et 84 millions de variantes identifiées. Pour plus d’informations, consultez le site web du projet 1000 Genomes et les publications suivantes :

Analyse du pilote : Carte de variation du génome humain à partir du séquençage à l’échelle de la population Nature 467, 1061-1073 (28 octobre 2010)

Analyse de la phase 1 : Carte intégrée des variations génétiques à partir de 1 092 génomes humains Nature 491, 56-65 (1er novembre 2012)

Analyse de la phase 3 : Référence mondiale pour la variation génétique humaine Nature 526, 68-74 (1er octobre 2015) et Carte intégrée de variation structurelle dans 2 504 génomes humains, Nature 526, 75-81 (1er octobre 2015)

Pour plus d’informations sur les formats de données, consultez http://www.internationalgenome.org/formats

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données est un miroir de ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

Ce jeu de données contient approximativement 815 To de données et est mis à jour quotidiennement.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. L’allocation de ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest est recommandée à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

USA Ouest 2 : « https://dataset1000genomes.blob.core.windows.net/dataset  »

USA Centre-Ouest : « https://dataset1000genomes-secondary.blob.core.windows.net/dataset  »

[Jeton SAS](../storage/common/storage-sas-overview.md) : sv=2019-10-10&si=prod&sr=c&sig=9nzcxaQn0NprMPlSh4RhFQHcXedLQIcFgbERiooHEqM%3D

## <a name="use-terms"></a>Conditions d’utilisation

Depuis les publications finales, les données du projet 1000 Genomes sont accessibles publiquement sans embargo pour une utilisation dans le cadre des termes fournis par la source du jeu de données ([http://www.internationalgenome.org/data](http://www.internationalgenome.org/data)). L’utilisation des données doit être citée conformément aux détails disponibles dans le [FAQ]() du projet 1 000 génomes.

## <a name="contact"></a>Contact

https://www.internationalgenome.org/contact

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
