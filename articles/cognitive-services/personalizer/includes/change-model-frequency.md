---
title: Fichier Include
description: inclure fichier
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 6473eeee350299ba98460f3f4f022e0514f90e69
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255362"
---
### <a name="change-the-model-update-frequency"></a>Changer la fréquence de mise à jour du modèle

Dans le portail Azure, dans la ressource Personalizer dans la page **Configuration**, affectez à la **fréquence de mise à jour du modèle** la valeur 30 secondes. Avec cette courte durée, le service est entraîné rapidement, ce qui vous permet de voir comment la première action change pour chaque itération.

![Changer la fréquence de mise à jour du modèle](../media/settings/configure-model-update-frequency-settings.png)

Quand une boucle Personalizer est instanciée pour la première fois, aucun modèle n’est disponible puisqu’il n’y a pas encore d’appels d’API Reward à partir desquels s’entraîner. Les appels Rank retourneront des probabilités égales pour chaque élément. Votre application doit toujours classer le contenu à l’aide de la sortie de RewardActionId.