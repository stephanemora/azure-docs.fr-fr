---
title: Exigences et suggestions relatives au conteneur.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034484"
---
Le tableau suivant décrit la quantité minimale et recommandée de cœurs de processeur et de mémoire à allouer pour chaque conteneur Reconnaître le texte.

| Conteneur | Minimale | Recommandé |TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Reconnaître le texte|1 cœur, 8 Go de mémoire, 0,5 TPS|2 cœurs, 8 Go de mémoire, 1 TPS|0,5, 1|

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* TPS - transactions par seconde

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.