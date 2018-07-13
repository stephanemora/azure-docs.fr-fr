---
title: API Reconnaissance de l’orateur | Microsoft Docs
description: Utilisez les algorithmes avancés de vérification de l’orateur et d’identification de l’orateur avec l’API Reconnaissance de l’orateur dans Cognitive Services.
services: cognitive-services
author: dwlin
manager: zhang
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: article
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 6d5e4e4bbe0cb5e57d2556f680ffcf8d16ee1818
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368365"
---
# <a name="speaker-recognition-api"></a>API Reconnaissance de l’orateur

Bienvenue dans les API Reconnaissance de l’orateur de Microsoft. Les API Reconnaissance de l’orateur sont des API basées sur le cloud qui fournissent les algorithmes les plus avancés de vérification de l’orateur et d’identification de l’orateur. Les API Reconnaissance de l’orateur peuvent être divisées en deux catégories : vérification de l’orateur et identification de l’orateur.


## <a name="speaker-verification"></a>Vérification de l’orateur


La voix a des caractéristiques uniques qui peuvent être utilisées pour identifier une personne, à l’image d’une empreinte digitale.  L’utilisation de la voix comme signal pour les scénarios de contrôle d’accès et d’authentification fait figure d’outil innovant, ajoutant notamment une couche de sécurité qui simplifie l’expérience d’authentification pour les clients.

Les API de vérification de l’orateur peuvent automatiquement vérifier et authentifier les utilisateurs à l’aide de leur voix ou de leur manière de parler.

### <a name="enrollment"></a>Inscription

L’inscription à la vérification de l’orateur est basée sur le texte, ce qui signifie que les orateurs doivent choisir une phrase spécifique à utiliser lors des phases d’inscription et de vérification. 

Lors de l’inscription, la voix de l’orateur est enregistrée lorsqu’il récite une phrase spécifique, puis un certain nombre de caractéristiques est extrait et la phrase choisie est reconnue. Ensemble, les caractéristiques extraites et la phrase choisie constituent une signature vocale unique.

### <a name="verification"></a>Vérification
###
La vérification consiste à comparer une voix et une expression d’entrée à l’expression et à la signature vocale de l’inscription afin de vérifier si elles appartiennent à la même personne et si l’expression prononcée est correcte.

Pour plus d’informations sur la vérification de l’orateur, reportez-vous à l’API de [vérification de l’orateur](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identification de l’orateur

Les API d’identification de l’orateur peuvent automatiquement identifier une personne parlant dans un fichier audio, à partir d’un groupe d’orateurs potentiels. Le contenu audio d’entrée est comparé au groupe d’orateurs fourni. En cas de correspondance, l’identité de l’orateur est retournée.

Tous les orateurs doivent d’abord passer par un processus d’inscription pour que leur voix soit enregistrée dans le système et qu’une empreinte vocale soit créée.


### <a name="enrollment"></a>Inscription

L’inscription à l’identification de l’orateur est basée sur le texte, ce qui signifie qu’il n’existe aucune restriction sur le contenu récité par l’orateur dans le fichier audio. La voix de l’orateur est enregistrée et un certain nombre de caractéristiques est extrait pour former une signature vocale unique. 


### <a name="recognition"></a>Reconnaissance

Le contenu audio de l’orateur inconnu, conjointement avec le groupe d’orateurs potentiels, est fourni lors de la reconnaissance. La voix d’entrée est comparée à tous les orateurs afin de déterminer à qui appartient la voix et, en cas de correspondance, l’identité de l’orateur est retournée.


Pour plus d’informations sur l’identification de l’orateur, reportez-vous à l’API [d’identification de l’orateur](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
