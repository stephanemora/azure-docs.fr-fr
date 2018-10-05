---
title: Paramètres régionaux et langues pris en charge - Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Présentation des langues prises en charge dans Custom Speech Service de Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223953"
---
# <a name="supported-locales-in-custom-speech-service"></a>Paramètres régionaux pris en charge dans Custom Speech Service
À l’heure actuelle, le service Custom Speech Service prend en charge la personnalisation de modèles utilisant les paramètres régionaux suivants :

| Type de modèle | Prise en charge des langages |
|----|-----|
| Modèles acoustiques | Anglais (États-Unis, en-US) |
| Modèles de langage | Anglais (États-Unis, en-US), chinois (zh-CN) |

Bien que la personnalisation de modèles acoustiques soit uniquement prise en charge en anglais (États-Unis), l’importation de données acoustiques en chinois est prise en charge afin de tester (en mode hors connexion) les modèles de langage personnalisés en chinois.

Les paramètres régionaux appropriés doivent être sélectionnés avant d’entreprendre une action. Les paramètres régionaux actuels sont indiqués dans le titre du tableau sur toutes les pages de données, de modèles et de déploiements. Pour modifier les paramètres régionaux, cliquez sur le bouton « Changer les paramètres régionaux » situé sous le titre du tableau. Vous êtes ensuite dirigé vers une page de confirmation des paramètres régionaux. Cliquez sur « OK » pour revenir au tableau.

Poursuivez avec les étapes suivantes :
* Découvrez [comment créer un modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md) pour améliorer la précision de la reconnaissance
* Découvrez [comment créer un modèle de langage personnalisé](cognitive-services-custom-speech-create-language-model.md) pour améliorer le taux de reconnaissance
* Suivez les [instructions concernant la transcription](cognitive-services-custom-speech-transcription-guidelines.md) pour préparer vos données
