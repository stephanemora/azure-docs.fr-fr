---
title: Traitement audio - Service de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble du traitement audio et des fonctionnalités de la pile audio Microsoft.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: f0bc80a9c248b9171a89bead1971cb7d5f4ce0fe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097028"
---
# <a name="audio-processing"></a>Traitement audio

Le traitement audio fait référence aux améliorations appliquées à un flux audio dans le but d’améliorer la qualité audio. Un ensemble d’améliorations combinées est souvent appelé une pile de traitement audio. L’objectif de l’amélioration de la qualité audio peut être segmenté dans différents scénarios, tels que le traitement vocal et les télécommunications. Parmi les exemples d’améliorations courantes, citons le contrôle de gain automatique (AGC), la suppression du bruit et l’annulation de l’écho acoustique (AEC).

Des scénarios/cas d’utilisation différents nécessitent des optimisations différentes qui influencent le comportement de la pile de traitement audio. Par exemple, dans les scénarios de télécommunications tels que les appels téléphoniques, il est acceptable d’avoir des distorsions mineures dans le signal audio après l’application du traitement. Cela est dû au fait que les êtres humains peuvent continuer à comprendre la parole avec une haute précision. Toutefois, il est inacceptable et gênant pour une personne d’entendre sa propre voix dans un écho. Cela contraste avec les scénarios de traitement vocal, où la distorsion du son peut nuire à la précision d’un modèle de reconnaissance vocale, mais il est acceptable d’avoir des niveaux mineurs d’écho résiduels.

## <a name="microsoft-audio-stack"></a>Pile audio Microsoft

La pile audio Microsoft est un ensemble d’améliorations optimisées pour les scénarios de traitement vocal. Cela comprend des exemples tels que la reconnaissance de mots clés et la reconnaissance vocale. Il se compose de diverses améliorations/composants qui fonctionnent sur le signal audio d’entrée :

* **Suppression du bruit** - Réduisez le niveau de bruit de fond.
* **Formation de faisceaux** - Localisez l’origine du son et optimisez le signal audio à l’aide de plusieurs microphones.
* **Dereverberation** - Réduisez les reflets du son des surfaces dans l’environnement.
* **Annulation de l’écho acoustique** - supprime le son lu hors de l’appareil lorsque l’entrée du microphone est active.
* **Contrôle automatique du gain** - Ajustez dynamiquement le niveau de voix de la personne pour tenir compte des haut-parleurs, des distances longues ou des microphones non calibrés.

[ ![Diagramme de bloc des améliorations de la pile audio Microsoft.](media/audio-processing/mas-block-diagram.png) ](media/audio-processing/mas-block-diagram.png#lightbox)

La pile audio Microsoft alimente un large éventail de produits Microsoft :
* **Windows** - La pile audio Microsoft est le pipeline de traitement vocal par défaut lors de l’utilisation de la catégorie audio vocal. 
* **Affichages Microsoft Teams et appareils de salle de Microsoft Teams** - Les affichages Microsoft Teams et les appareils de la salle de Teams utilisent la pile audio Microsoft pour activer des expériences de haute qualité, basées sur la voix avec Cortana.

### <a name="pricing"></a>Tarifs

Il n’y a aucun coût d’utilisation de la pile audio Microsoft avec le kit de développement logiciel (SDK) Speech.

### <a name="minimum-requirements-to-use-microsoft-audio-stack"></a>Configuration minimale requise pour utiliser la pile audio Microsoft

La pile audio Microsoft peut être utilisée par tout produit ou application pouvant répondre aux exigences suivantes :
* **Audio brut** - La pile audio Microsoft requiert un audio brut (c’est-à-dire non traité) comme entrée pour obtenir les meilleurs résultats. Le fait de fournir un son déjà traité limite la capacité de la pile audio à effectuer des améliorations de haute qualité.
* **Géométries du microphone** - les informations géométriques sur chaque microphone de l’appareil sont nécessaires pour effectuer correctement toutes les améliorations offertes par la pile audio Microsoft. Les informations incluent le nombre de micros, leur disposition physique et leurs coordonnées. Jusqu’à 16 canaux de microphone d’entrée sont pris en charge. 
* **Audio de bouclage ou de référence** - un canal audio qui représente le son lu hors de l’appareil est requis pour effectuer l’annulation de l’écho acoustique. 
* **Format d’entrée** - la pile audio Microsoft prend uniquement en charge le sous-échantillonnage des taux d’échantillonnage qui sont des multiples entiers de 16 kHz. Un taux d’échantillonnage minimal de 16 kHz est requis. En outre, les formats suivants sont pris en charge : 32 bits IEEE Little Endian float, 32 bits Little Endian signed int, 24 bits Little Endian signed int, 16 bits little endian signed int, et 8 bits signed int.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur l’intégration du kit de développement logiciel (SDK) Speech de la pile audio Microsoft.](audio-processing-speech-sdk.md)
