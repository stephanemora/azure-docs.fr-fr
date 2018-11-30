---
title: Qu’est-ce que Video Indexer ?
titlesuffix: Azure Media Services
description: Cette rubrique offre une vue d'ensemble du service Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ae8634fbfdaa250cbabda6189c6c2eeef8e5e4f1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291823"
---
# <a name="what-is-video-indexer"></a>Qu’est-ce que Video Indexer ?

Azure Video Indexer est une application cloud créée à l’aide d’Azure Media Analytics, Azure Search et Cognitive Services (par exemple, l’API Visage, Microsoft Translator, l’API Vision par ordinateur et Custom Speech Service). Elle vous permet d’extraire les insights de vos vidéos à l’aide de modèles Video Indexer décrits ci-dessous :
 
- **Détection de langue automatique** : identifie automatiquement la langue parlée dominante. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, le russe et le portugais (brésilien). L’anglais sera utilisé lorsque la langue n’est pas détectée.
- **Transcription audio** : convertit la parole en texte dans 10 langues et permet les extensions. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, l’arabe, le russe et le portugais (brésilien).
- **Sous-titrage** : crée un sous-titrage dans trois formats : VTT, TTML et SRT.
- **Traitement en deux canaux** : détecte automatiquement, sépare la transcription et fusionne en une chronologie unique.
- **Réduction du bruit** : nettoie les enregistrements contenant du bruit ou de l’audio de téléphonie (en fonction des filtres Skype).
- **Personnalisation de la transcription (CRIS)** : entraîne et exécute des modèles de reconnaissance vocale personnalisés étendus pour créer des transcriptions spécifiques au secteur.
- **Énumération de l’orateur** : mappe et comprend quel orateur a prononcé tels mots et à quel moment.
- **Statistiques de l’orateur** : fournit des statistiques pour les ratios de parole des orateurs.
- **Reconnaissance visuelle du texte (OCR)** : extrait le texte qui s’affiche visuellement dans la vidéo.
- **Extraction d’images clés** : détecte les images clés stables dans une vidéo.
- **Analyse des sentiments** : identifie les sentiments positifs, négatifs et neutres à partir de la reconnaissance vocale et visuelle du texte.
- **Modération du contenu visuel** : détecte les contenus choquants et/ou destinés aux adultes.
- **Extraction de mots clés** : extrait les mots clés du texte visuel ou vocal.
- **Identification des étiquettes** : identifie les objets visuels et les actions affichés.
- **Extraction de marques** : extrait les marques du texte visuel ou vocal.
- **Détection de visage** : détecte et regroupe les visages qui apparaissent dans la vidéo.
- **Extraction de miniatures pour les visages (« meilleurs visages »)** : identifie automatiquement le meilleur visage capturé dans chaque groupe de visages (en fonction de la qualité, de la taille et de la position frontale) et l’extrait en tant qu’actif d’image.
- **Identification de célébrités** : Video Indexer identifie automatiquement plus de 1 million de célébrités : dirigeants, acteurs et actrices, athlètes, chercheurs, dirigeants d’entreprise et spécialistes techniques du monde entier. Les données relatives à ces célébrités figurent également sur plusieurs sites Web connus, par exemple, IMDB et Wikipedia.
- **Identification des visages basée sur le compte** : Video Indexer effectue l’apprentissage d’un modèle pour un compte spécifique. Il reconnaît alors les visages dans la vidéo selon le modèle entraîné en particulier pour les vidéos de ce compte.
- **Modération du contenu textuel** : détecte le texte explicite dans la transcription audio.
- **Détection de plan** : détermine le moment où une scène change dans la vidéo.
- **Détection de cadre noir** : identifie les cadres noirs présents dans la vidéo.
- **Effets audio** : identifie les effets audio tels que les applaudissements, la parole et le silence.
- **Inférence de la rubrique** : fait des inférences des principales rubriques à partir de transcriptions. La taxonomie [IPTC](https://iptc.org/standards/media-topics/) de premier niveau est incluse.
- **Détection d’émotions** : identifie les émotions grâce à des signaux audio et vocaux. L’émotion peut être : le bonheur, la tristesse, la colère ou la peur.
- **Artefacts** : extrait un ensemble complet d’artefacts d’un « niveau suivant de détails » pour chacun des modèles.
- **Traduction** : crée des traductions de la transcription audio en 54 langues différentes.

Une fois le traitement et l’analyse terminés par Video Indexer, vous pouvez consulter, organiser, rechercher et publier des aperçus de la vidéo.

Si votre rôle est gestionnaire de contenu ou développeur, le service Video Indexer peut répondre à vos besoins. Les gestionnaires de contenu peuvent utiliser le portail web de Video Indexer pour utiliser le service sans avoir à écrire une seule ligne de code, voir [Prise en main à l’aide du site web Video Indexer](video-indexer-get-started.md). Les développeurs peuvent tirer parti des API pour traiter le contenu à l’échelle, voir [Utiliser l’API REST Video Indexer](video-indexer-use-apis.md). Le service permet également aux clients d’utiliser des widgets pour publier des flux vidéo et des aperçus extraits dans leurs propres applications, voir [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md).

Vous pouvez vous inscrire à ce service à l’aide d’un compte AAD, LinkedIn, Facebook, Google ou MSA. Pour plus d’informations, consultez [Prise en main](video-indexer-get-started.md).

## <a name="scenarios"></a>Scénarios

Voici quelques scénarios où Video Indexer peut être utile

- Recherche : les aperçus extraits de la vidéo peuvent être utilisés pour améliorer l’expérience de recherche au sein d’une bibliothèque vidéo. Par exemple, l’indexation des visages et des mots prononcés peut permettre de trouver les moments spécifiques d’une vidéo où une personne particulière a prononcé certains mots ou le moment où deux personnes se sont vues. La recherche basée sur de tels aperçus de vidéos s’applique aux agences de presse, aux instituts de formation, aux diffuseurs, aux propriétaires de contenu de divertissement, aux LOB d’entreprise et d’une façon générale à n’importe quel secteur d’activité possédant une bibliothèque vidéo au sein de laquelle les utilisateurs doivent faire des recherches.

- Monétisation : Video Indexer peut aider à améliorer la valeur des vidéos. Par exemple, les secteurs d’activité s’appuyant sur le chiffre d’affaires publicitaire (par exemple, les médias d’information, les médias sociaux, etc.), peuvent fournir des publicités plus pertinentes en utilisant les informations extraites comme des signaux supplémentaires pour le serveur publicitaire (le fait de présenter des chaussures de sport est plus pertinent au milieu d’un match de football que lors d’une compétition de natation).

- Engagement utilisateur : les aperçus de vidéo peuvent être utilisés pour améliorer l’engagement utilisateur en leur présentant les moments pertinents d’une vidéo. Par exemple, imaginez une vidéo de formation donnant des explications sur des sphères au cours des 30 premières minutes, puis sur les pyramides au cours des 30 minutes suivantes. Le fait que la vidéo soit positionnée automatiquement pour démarrer au début des 30 dernières minutes pourrait bénéficier à un étudiant se documentant sur les pyramides.

Pour plus d’informations, consultez ce [blog](https://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à vous lancer avec Video Indexer. Pour plus d’informations, consultez les articles suivants :

- [Prise en main du site web Video Indexer](video-indexer-get-started.md)
- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)
