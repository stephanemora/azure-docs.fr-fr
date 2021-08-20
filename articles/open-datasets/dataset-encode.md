---
title: ENCODE
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données ENCODE dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0dc10b580d1a7c6fed8559f15a11e7c54737692d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285155"
---
# <a name="encode-encyclopedia-of-dna-elements"></a>ENCODE : Encyclopedia of DNA Elements

Le [consortium ENCODE (Encyclopedia of DNA Elements)](https://www.encodeproject.org/help/project-overview/) est une collaboration internationale permanente entre des groupes de recherche financée par le NHGRI (National Human Genome Research Institute). L’objectif d’ENCODE est de créer une liste complète des éléments fonctionnels du génome humain, notamment des éléments qui agissent à l’échelle de la protéine et de l’ARN, et des éléments réglementaires qui contrôlent les cellules et les circonstances dans lesquelles un gène est actif.

Les enquêteurs d’ENCODE utilisent différentes analyses et méthodes pour identifier des éléments fonctionnels. La découverte et l’annotation des éléments génétiques s’effectuent principalement en classant une gamme diversifiée de sources d’ARN, d’études de génomique comparative, de méthodes bio-informatiques intégratives et de curation de contenu réalisée par des êtres humains. Les éléments réglementaires sont généralement étudiés par le biais d’analyses de l’hypersensibilité de l’ADN, d’analyses de méthylation de l’ADN et de l’immunoprécipitation (IP) de protéines qui interagissent avec l’ADN et l’ARN, c’est-à-dire des histones modifiées, des facteurs de transcription, des régulateurs de la chromatine et des protéines de liaison de l’ARN, suivis du séquençage.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données est une copie du magasin de données sur https://www.encodeproject.org/

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

Ce jeu de données comprend environ 756 To de données et est mis à jour mensuellement pendant la première semaine de chaque mois.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. Nous recommandons de placer les ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

USA Ouest 2 : « https://datasetencode.blob.core.windows.net/dataset  »

USA Centre-Ouest : « https://datasetencode-secondary.blob.core.windows.net/dataset  »

[Jeton SAS](../storage/common/storage-sas-overview.md): ?sv=2019-10-10&si=prod&sr=c&sig=9qSQZo4ggrCNpybBExU8SypuUZV33igI11xw0P7rB3c%3D

## <a name="use-terms"></a>Conditions d’utilisation

Les utilisateurs de données externes peuvent télécharger, analyser et publier les résultats concernant n’importe quelles données ENCODE, et ce, sans restrictions, indépendamment du type ou de la taille des données. Il n’existe pas de période de grâce pour les producteurs de données ENCODE, que ce soit individuellement ou dans le cadre du Consortium. Les chercheurs qui utilisent des données ENCODE non publiées sont encouragés à contacter les producteurs de données pour discuter des possibles publications. Le consortium continuera à publier les résultats de ses propres efforts d’analyse dans des publications indépendantes.

ENCODE exige que les chercheurs qui utilisent des jeux de données ENCODE (publiés ou non) dans des publications et des présentations citent le Consortium ENCODE de toutes les façons suivantes reprises dans [https://www.encodeproject.org/help/citing-encode/](https://www.encodeproject.org/help/citing-encode/).

## <a name="contact"></a>Contact

Si vous avez des questions, des problèmes ou des commentaires, envoyez un e-mail à notre support technique à l’adresse suivante : encode-help@lists.stanford.edu.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).