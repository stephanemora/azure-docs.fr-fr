---
title: Appairage et alignement des phrases - Custom Translator
titleSuffix: Azure Cognitive Services
description: Pendant l’exécution de l’apprentissage, les phrases présentes dans les documents parallèles sont appariées ou alignées. Custom Translator apprend à traduire une phrase à la fois, en lisant une phrase, la traduction de cette phrase. Ensuite, il aligne les mots et les phrases de ces deux phrases les uns avec les autres.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595462"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Appairage et alignement des phrases dans des documents parallèles

Pendant l’apprentissage, les phrases présentes dans les documents parallèles sont appariées ou alignées. Custom Translator indique le nombre de phrases qu’il a pu apparier comme Phrases alignées dans chacun des jeux de données.

## <a name="pairing-and-alignment-process"></a>Processus d’appairage et d’alignement

Custom Translator apprend la traduction de phrases à raison d’une phrase à la fois. Il lit une phrase de la source, puis la traduction de cette phrase de la cible. Ensuite, il aligne les mots et les phrases de ces deux phrases les uns avec les autres. Ce processus lui permet de créer un mappage des mots et des phrases en une phrase avec les mots et les phrases équivalents dans la traduction de cette phrase. L’alignement vise à garantir que le système effectue son apprentissage sur des phrases qui sont des traductions les unes des autres.

## <a name="pre-aligned-documents"></a>Documents préalignés

Si vous savez que vous avez des documents parallèles, vous pouvez remplacer l’alignement des phrases en fournissant des fichiers texte préalignés. Vous pouvez extraire toutes les phrases des deux documents dans un fichier texte, organiser une phrase par ligne et le charger avec une extension `.align`. L’extension `.align` signale à Custom Translator qu’il doit ignorer l’alignement des phrases.

Pour de meilleurs résultats, assurez-vous d’avoir une phrase par ligne dans vos fichiers. Évitez d’avoir des caractères de saut de ligne au sein d’une phrase, car cela entraîne des alignements médiocres.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Nombre minimum suggéré de phrases extraites et alignées

Pour qu’un apprentissage réussisse, le tableau ci-dessous indique le nombre minimum de phrases extraites et de phrases alignées nécessaires dans chaque jeu de données. Le nombre minimum suggéré de phrases extraites est beaucoup plus élevé que le nombre minimum suggéré de phrases alignées pour tenir compte du fait que l’alignement des phrases risque de ne pas pouvoir aligner toutes les phrases extraites avec succès.

| Jeu de données   | Nombre minimum de phrases extraites suggéré | Nombre minimum de phrases alignées suggéré | Nombre maximum de phrases alignées |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Formation   | 10 000                                     | 2 000                                    | Pas de limite supérieure                 |
| Réglage     | 2 000                                      | 500                                      | 2 500                          |
| Test    | 2 000                                      | 500                                      | 2 500                          |
| Dictionnaire | 0                                          | 0                                        | Pas de limite supérieure                 |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser un [dictionnaire](what-is-dictionary.md) dans Custom Translator.
