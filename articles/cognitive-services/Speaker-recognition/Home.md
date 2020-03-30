---
title: Qu’est-ce que l’API Speaker Recognition ?
titleSuffix: Azure Cognitive Services
description: Vérification de l’orateur et d’identification de l’orateur avec l’API Speaker Recognition dans Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464923"
---
# <a name="speaker-recognition-api---preview"></a>API Vérification de l’orateur (Préversion)

Les API Vérification de l’orateur sont des API basées sur le cloud qui fournissent les algorithmes IA les plus avancés de vérification de l’orateur et d’identification de l’orateur. Les API Speaker Recognition sont divisées en deux catégories : vérification de l’orateur et identification de l’orateur.

## <a name="speaker-verification"></a>Vérification de l’orateur

La voix présente des caractéristiques uniques qui peuvent être associées à un individu.  Les applications peuvent utiliser la voix comme facteur d’authentification supplémentaire, dans des scénarios applicables aux centres d’appels et aux services Web.

Les API Vérification de l’orateur servent d’outil intelligent pour vérifier que les utilisateurs utilisent à la fois leurs phrases secrètes vocales et vocales.

### <a name="enrollment"></a>Inscription

Lorsque les utilisateurs s’inscrivent au service Vérification de l’orateur, ils doivent choisir une phrase, qu’ils prononcent lors de leur inscription, puis de leur vérification.

Lors de l’inscription, la voix de chaque utilisateur est enregistrée, tandis qu’il prononce une phrase spécifique. Les caractéristiques de sa voix sont alors détectées et extraites pour constituer une signature vocale unique, activée dès que la phrase choisie par l’utilisateur est reconnue. Ces données d’inscription permettent ensuite de vérifier l’identité de l’utilisateur. Ces données d’inscription sont stockées dans un système sécurisé. Le client contrôle la durée leur conservation. Les clients peuvent créer, mettre à jour et supprimer des données d’inscription pour les utilisateurs via des appels d’API.  Lorsqu’une inscription est supprimée, toutes les données d’inscription de l’utilisateur lui étant associées sont aussi supprimées.

Les clients doivent s’assurer qu’ils ont reçu les autorisations appropriées de la part des utilisateurs du service Vérification de l’orateur.

### <a name="verification"></a>Vérification

Lors de la vérification, le client doit appeler l’API Vérification de l’orateur avec l’ID associé à la personne dont l’identité doit être vérifiée.  Le service extrait alors les caractéristiques vocales et la phrase prononcée lors de l’inscription. Il compare ensuite les caractéristiques aux éléments correspondants dans les données d’inscription de l’utilisateur que dont l’identité doit être vérifiée, puis détermine la présence de correspondances.  Ensuite, le service répond par « Accepté » ou « Rejecté » en précisant un niveau de confiance.  Le client détermine alors si les résultats sont suffisant pour affirmer que la personne contrôlée est bien l’utilisateur inscrit.

Le niveau de confiance doit être défini en fonction du scénario et des autres facteurs de vérification utilisés. Nous vous recommandons d’essayer différents niveaux de confiance et d’utiliser des réglages appropriés à chaque application. Les API ne permettent pas de déterminer si la phrase examinée est prononcée par une personne en direct ou s’il s’agit d’une imitation ou de l’enregistrement de la voix d’un utilisateur inscrit.

Ce service ne conserve ni l’enregistrement vocal ni les caractéristiques vocales qu’il utilise lors de la vérification. Elles lui sont transmises depuis le système sécurisé.

Pour plus d’informations sur la vérification de l’orateur, reportez-vous à l’API de [vérification de l’orateur](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identification de l’orateur

Les applications peuvent se baser sur les voix d’un groupe d’utilisateurs inscrits pour déterminer « qui parle ». Les API Identification de l’orateur peuvent être utilisées dans des scénarios tels que la détermination de la productivité d’une réunion, la personnalisation et la transcription pour un centre d’appels.

### <a name="enrollment"></a>Inscription

L’inscription à l’identification de l’orateur est basée sur le texte, ce qui signifie qu’il n’existe aucune restriction sur le contenu récité par l’orateur dans le fichier audio. Aucune phrase secrète n’est requise.

Lors de l’inscription, la voix de l’orateur est enregistrée et des caractéristiques vocales sont extraites pour former une signature vocale unique. L’audio et les fonctionnalités vocales extraites sont stockées dans un système sécurisé. Le client contrôle la durée leur conservation. Les clients peuvent créer, mettre à jour et supprimer ces données d’inscription d’utilisateurs via des appels d’API. Lorsqu’une inscription est supprimée, toutes les données d’inscription de l’utilisateur lui étant associées sont aussi supprimées.

Les clients doivent s’assurer qu’ils ont reçu les autorisations appropriées de la part des utilisateurs du service Identification de l'orateur.

### <a name="identification"></a>Identification

Lors de l’identification, le service Identification de l'orateur extrait les caractéristiques vocales de l’enregistrement vocal fourni. Il compare ensuite ces caractéristiques aux données d’inscription d’une liste d’utilisateurs déterminée. Lorsqu’une correspondance avec un utilisateur inscrit est établie, le serveur retourne l’ID de l’intervenant, ainsi qu’un niveau de confiance.  Dans le cas contraire, le service retourne la réponse « Rejeté » si aucune correspondance avec le moindre utilisateur inscrit n’est établie.

Le niveau de confiance doit être défini en fonction du scénario. Nous vous recommandons d’essayer différents niveaux de confiance et d’utiliser des réglages appropriés à chaque application. Les API ne permettent pas de déterminer si la phrase examinée est prononcée par une personne en direct ou s’il s’agit d’une imitation ou de l’enregistrement de la voix d’un utilisateur inscrit.

Ce service ne conserve ni l’enregistrement vocal ni les caractéristiques vocales qu’il utilise lors de la vérification. Elles sont transmises au service depuis le système sécurisé durant l’identification.

Pour plus d’informations sur l’identification de l’orateur, reportez-vous à l’API  [Identification de l’orateur](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
