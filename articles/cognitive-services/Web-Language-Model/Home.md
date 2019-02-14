---
title: Vue d’ensemble de l’API Web Language Model
titleSuffix: Azure Cognitive Services
description: L’API Web Language Model dans Microsoft Cognitive Services fournit des outils de pointe pour le traitement en langage naturel.
services: cognitive-services
author: piyushbehre
manager: nitinme
ms.service: cognitive-services
ms.subservice: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 4228692d707f0cff8be0d76bc70794f48d944143
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878763"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Qu’est-ce que l’API Modèle de langage web ? (Préversion)

> [!IMPORTANT]
> La préversion de Web Language Model a été désactivée le 9 août 2018. Nous vous recommandons d’utiliser les [modules d’analyse de texte Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) pour le traitement et l’analyse de texte.

L’API Web Language Model de Microsoft est un service cloud basé sur REST, fournissant des outils de pointe pour le traitement en langage naturel. À l’aide de cette API, votre application peut tirer parti de la puissance de Big Data par le biais de modèles de langage entraînés sur les corpus issus du web et collectés par Bing sur le marché en-US.

Ces modèles linguistiques de n-gramme à interruptions discrètes, prenant en charge des chaînes de Markov jusqu’au cinquième ordre, sont entraînés avec les corpus suivants :

- Texte de corps de page web
- Texte de titre de page web
- Texte d’ancrage de page web
- Texte de requête de recherche web

L’API Web Language Model prend en charge quatre opérations de recherche :

1. Probabilité (log10) conjointe d’une séquence de mots.
2. Probabilité (log10) conditionnelle d’un mot donné par une séquence de mots le précédant.
3. Liste des mots (saisies) dont la probabilité de suivre une séquence de mots donnée est la plus élevée.
4. Coupure de mots dans des chaînes ne contenant pas d’espace.

## <a name="getting-started"></a>Mise en route

1. Abonnez-vous au service.
2. Téléchargez le [kit SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Exécutez l’exemple de code du kit SDK.
4. Reportez-vous à la [documentation de référence de l’API](http://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51) pour obtenir des informations complètes sur les points de terminaison, y compris des extraits de code dans une variété de langages.

## <a name="underlying-technology"></a>Technologie sous-jacente

Le document suivant fournit des détails sur le développement de ces modèles de langage ; il doit être cité dans les publications de recherche qui utilisent ce service :

- [Une vue d’ensemble des corpus de N-gramme web de Microsoft et leurs applications](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL-HLT 2010

Cliquez [ici](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) pour obtenir la liste actuelle des publications citant ces travaux.
