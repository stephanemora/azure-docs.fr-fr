---
title: Forum aux questions - Traduction de documents
titleSuffix: Azure Cognitive Services
description: Recevez des réponses aux questions fréquemment posées sur la traduction de documents dans le service Traducteur d’Azure Cognitive Services.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: lajanuar
ms.openlocfilehash: 18ce8c8032ca56ff0fa4dc039e862686c5a33d88
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363546"
---
# <a name="document-translation-faq"></a>FAQ de la traduction de documents

Cet article contient des réponses aux questions fréquemment posées sur la traduction de documents.

|Forum aux questions|
|:--|
|**Quand dois-je spécifier la langue source du document dans la requête ?**<br/>Si la langue du contenu du document source est connue, il est recommandé de spécifier la langue source dans la requête pour obtenir une meilleure traduction. Si le document a du contenu en plusieurs langues ou si la langue est inconnue, ne spécifiez pas de langue source dans la requête. La traduction de documents identifie automatiquement la langue pour chaque segment de texte, puis le traduit.|
|**Dans quelle mesure la disposition, la structure et la mise en forme sont-elles conservées ?**<br/>Lors de la traduction du texte de la langue source vers la langue cible, la longueur globale du texte traduit peut différer de la source.  Cela peut entraîner un déplacement du texte entre les pages. Les mêmes polices peuvent ne pas être disponibles à la fois pour les langues source et cible. En général, le même style de police est appliqué dans la langue cible afin de conserver la mise en forme la plus proche possible de la source.|
|**Le texte incorporé dans une image d’un document est-il traduit ?**<br/>Non. Le texte incorporé dans des images au sein d’un document n’est pas traduit.|
|**La traduction de documents traduit-t-elle le contenu des documents analysés ?**<br/>Non. La traduction de documents ne traduit pas le contenu des documents numérisés.|



