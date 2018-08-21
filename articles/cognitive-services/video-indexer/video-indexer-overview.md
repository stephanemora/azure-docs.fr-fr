---
title: Vue d’ensemble de Azure Video Indexer | Microsoft Docs
description: Cette rubrique offre une vue d'ensemble du service Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397889"
---
# <a name="what-is-video-indexer-preview"></a>Qu’est-ce que Video Indexer ? (préversion)

Video Indexer est une application cloud créée à l’aide d’Azure Media Analytics, Cognitive Services (par exemple, l’API Visage, Microsoft Translator, l’API Vision par ordinateur et Custom Speech Service) et Recherche Azure. Elle permet d’extraire les informations suivantes de vos vidéos à l’aide de technologies d’intelligence artificielle :

- **Détection automatique de la langue** : Video Indexer peut détecter automatiquement la langue de la vidéo. La détection automatique de la langue prend actuellement en charge l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, la russe. L’anglais sera utilisé lorsque la langue n’est pas détectée.
- **Transcription audio**: Video Indexer comporte des fonctionnalités de reconnaissance vocale permettant aux clients d’obtenir une transcription des mots prononcés. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le portugais (brésilien), le japonais, et le russe (avec beaucoup plus à venir). 
- **Suivi et identification du visage** : les technologies du visage permettent la détection des visages dans une vidéo. Les visages détectés sont comparés à une base de données de célébrités pour évaluer les célébrités présentes dans la vidéo. Les clients peuvent également étiqueter les visages qui ne correspondent pas à une célébrité. Video Indexer génère un modèle de visage basé sur ces étiquettes et peut reconnaître les visages dans des vidéos envoyées à l’avenir.
- **Indexation des orateurs** : Video Indexer a la possibilité de mapper et de reconnaître l’orateur, les mots qu’il a prononcés et le moment.
- **Reconnaissance visuelle du texte** : avec cette technologie, le service Video Indexer extrait le texte qui s’affiche dans les vidéos.  
- **Détection d’activité vocale** : la détection permet à Video Indexer de séparer le bruit de fond et les voix. 
- **Détection de scène**: Video Indexer a la possibilité d’effectuer une analyse visuelle sur la vidéo pour détecter les changements de scène au cours d’une vidéo.
- **Extraction d’images clés** : Video Indexer détecte automatiquement les images clés dans une vidéo. 
- **Analyse des sentiments**: Video Indexer effectue une analyse des sentiments à partir du texte extrait à l’aide d’une reconnaissance vocale et optique des caractères et il fournit cette information sous la forme de sentiments positifs, négatifs ou neutres, accompagnés de codes temporels.
- **Traduction** : Video Indexer a la capacité de traduire la transcription audio d’une langue à l’autre. Les langues suivantes sont prises en charge : anglais, espagnol, français, allemand, italien, chinois (simplifié), portugais (brésilien), japonais, et russe. Une fois traduit, l’utilisateur peut même obtenir un sous-titrage dans d’autres langues au sein du lecteur vidéo.
- **Modération du contenu visuel** : cette technologie permet de détecter les contenus adultes et/ou osés au sein de la vidéo et elle peut être utilisée pour filtrer le contenu. 
- **Extraction de mots clés**: Video Indexer extrait des mots clés à partir de la transcription des mots et du texte reconnus par le module de reconnaissance du texte visuel.
- **Étiquettes** : Video Indexer fournit des étiquettes pour les objets visuels tels que les chats, les chiens, les tables, les voitures, ainsi que des actions comme le fait d’être debout, de courir ou de voler.
- **Extraction des marques** : Video Indexer extrait les marques commerciales à partir de la transcription des mots et du texte reconnus par le module de reconnaissance du texte visuel.

Une fois le traitement et l’analyse terminés par Video Indexer, vous pouvez consulter, organiser, rechercher et publier des aperçus de la vidéo.

Si votre rôle est gestionnaire de contenu ou développeur, le service Video Indexer peut répondre à vos besoins. Les gestionnaires de contenu peuvent utiliser le portail web de Video Indexer pour utiliser le service sans avoir à écrire une seule ligne de code, voir [Prise en main à l’aide du portail de Video Indexer](video-indexer-get-started.md). Les développeurs peuvent tirer parti des API pour traiter le contenu à l’échelle, voir [Utiliser l’API REST Video Indexer](video-indexer-use-apis.md). Le service permet également aux clients d’utiliser des widgets pour publier des flux vidéo et des aperçus extraits dans leurs propres applications, voir [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md).

Vous pouvez vous inscrire à ce service à l’aide d’un compte AAD, LinkedIn, Facebook, Google ou MSA. Pour plus d’informations, consultez [Prise en main](video-indexer-get-started.md).

## <a name="scenarios"></a>Scénarios

Voici quelques scénarios où Video Indexer peut être utile

- Recherche : les aperçus extraits de la vidéo peuvent être utilisés pour améliorer l’expérience de recherche au sein d’une bibliothèque vidéo. Par exemple, l’indexation des visages et des mots prononcés peut permettre de trouver les moments spécifiques d’une vidéo où une personne particulière a prononcé certains mots ou le moment où deux personnes se sont vues. La recherche basée sur de tels aperçus de vidéos s’applique aux agences de presse, aux instituts de formation, aux diffuseurs, aux propriétaires de contenu de divertissement, aux LOB d’entreprise et d’une façon générale à n’importe quel secteur d’activité possédant une bibliothèque vidéo au sein de laquelle les utilisateurs doivent faire des recherches.

- Monétisation : Video Indexer peut aider à améliorer la valeur des vidéos. Par exemple, les secteurs d’activité s’appuyant sur le chiffre d’affaires publicitaire (par exemple, les médias d’information, les médias sociaux, etc.), peuvent fournir des publicités plus pertinentes en utilisant les informations extraites comme des signaux supplémentaires pour le serveur publicitaire (le fait de présenter des chaussures de sport est plus pertinent au milieu d’un match de football que lors d’une compétition de natation).

- Engagement utilisateur : les aperçus de vidéo peuvent être utilisés pour améliorer l’engagement utilisateur en leur présentant les moments pertinents d’une vidéo. Par exemple, imaginez une vidéo de formation donnant des explications sur des sphères au cours des 30 premières minutes, puis sur les pyramides au cours des 30 minutes suivantes. Le fait que la vidéo soit positionnée automatiquement pour démarrer au début des 30 dernières minutes pourrait bénéficier à un étudiant se documentant sur les pyramides.

Pour plus d’informations, consultez ce [blog](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à vous lancer avec Video Indexer. Pour plus d’informations, consultez les articles suivants :

- [Prise en main du portail Video Indexer](video-indexer-get-started.md)
- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)
