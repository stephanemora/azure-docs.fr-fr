---
title: Paramètres régionaux et langues pris en charge dans Custom Speech Service sur Azure | Microsoft Docs
description: Présentation des langues prises en charge dans Custom Speech Service de Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: e5c1f7d9648f404512f366be4f4d16ea0cb0f778
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217116"
---
# <a name="supported-locales-in-custom-speech-service"></a>Paramètres régionaux pris en charge dans Custom Speech Service

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

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
