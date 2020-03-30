---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122743"
---
## <a name="change-the-model-update-frequency"></a>Changer la fréquence de mise à jour du modèle

Dans le portail Azure, dans la ressource Personalizer dans la page **Configuration**, affectez à la **fréquence de mise à jour du modèle** la valeur 10 secondes. Avec cette courte durée, le service est entraîné rapidement, ce qui vous permet de voir comment la première action change pour chaque itération.

![Changer la fréquence de mise à jour du modèle](../media/settings/configure-model-update-frequency-settings.png)

Quand une boucle Personalizer est instanciée pour la première fois, aucun modèle n’est disponible puisqu’il n’y a pas encore d’appels d’API Reward à partir desquels s’entraîner. Les appels Rank retourneront des probabilités égales pour chaque élément. Votre application doit toujours classer le contenu à l’aide de la sortie de RewardActionId.