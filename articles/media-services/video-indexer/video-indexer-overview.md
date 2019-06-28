---
title: Qu’est-ce que Video Indexer ?
titlesuffix: Azure Media Services
description: Cette rubrique offre une vue d'ensemble du service Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2c72c7c493c0a887adab147054c725a2e1c0659f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799135"
---
# <a name="what-is-video-indexer"></a>Qu’est-ce que Video Indexer ?

Azure Video Indexer est une application cloud créée à l’aide d’Azure Media Analytics, Azure Search et Cognitive Services (par exemple, l’API Visage, Microsoft Translator, l’API Vision par ordinateur et Custom Speech Service). Elle vous permet d’extraire les insights de vos vidéos à l’aide de modèles vidéo et audio Video Indexer décrits ci-dessous :
  
## <a name="video-insights"></a>Insights vidéo

- **Détection de visage** : Détecte et regroupe les visages qui apparaissent dans la vidéo.
- **Identification de célébrités** : Video Indexer identifie automatiquement plus de 1 million de célébrités : dirigeants, acteurs et actrices, athlètes, chercheurs, dirigeants d’entreprise et spécialistes techniques du monde entier. Les données relatives à ces célébrités figurent également sur plusieurs sites Web connus, par exemple, IMDB et Wikipedia.
- **Identification des visages basée sur le compte** : Video Indexer effectue l’apprentissage d’un modèle pour un compte spécifique. Il reconnaît ensuite les visages dans la vidéo en fonction du modèle formé. Pour plus d’informations, consultez [Personnaliser un modèle de personne depuis le site web Video Indexer](customize-person-model-with-website.md) et [Personnaliser un modèle de personne avec l’API Video Indexer](customize-person-model-with-api.md).
- **Extraction de miniatures pour les visages (« meilleurs visages »)**  : Identifie automatiquement le meilleur visage capturé dans chaque groupe de visages (en fonction de la qualité, de la taille et de la position frontale) et l’extrait en tant qu’actif d’image.
- **Reconnaissance visuelle du texte** (OCR) : Extrait le texte qui s’affiche visuellement dans la vidéo.
- **Modération du contenu visuel** : Détecte les contenus choquants et/ou destinés aux adultes.
- **Identification des étiquettes** : Identifie les objets visuels et les actions affichés.
- **Segmentation de la scène** : détermine quand une scène change dans la vidéo via des signaux visuels. Une scène représente un événement unique et est composée d’une série de captures consécutives, qui sont sémantiquement liées. 
- **Détection de plan** : détermine quand un plan change dans la vidéo via des signaux visuels. Un plan est une série d’images prises par la même caméra. Pour plus d’informations, consultez [Scènes, plans et images clés](scenes-shots-keyframes.md).
- **Détection de cadre noir** : Identifie les cadres noirs présents dans la vidéo.
- **Extraction d’images clés** : Détecte les images clés stables dans une vidéo.
- **Déploiement de crédits** : identifie le début et la fin des crédits à la fin des émissions de télévision et des films.

## <a name="audio-insights"></a>Insights audio

- **Détection de langue automatique** : Identifie automatiquement la langue parlée dominante. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, le russe et le portugais brésilien. L’anglais sera utilisé lorsque la langue n’est pas détectée.
- **Transcription audio** : Convertit la parole en texte dans 12 langues et autorise les extensions. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, l’arabe, le russe, le portugais brésilien, l’hindi et le coréen.
- **Sous-titrage** : Crée un sous-titrage dans trois formats : VTT, TTML, SRT.
- **Traitement en deux canaux** : Détecte automatiquement, sépare la transcription et fusionne en une chronologie unique.
- **Réduction du bruit** : Nettoie les enregistrements contenant du bruit ou de l’audio de téléphonie (en fonction des filtres Skype).
- **Personnalisation de la transcription** (CRIS) : Entraîne des modèles de reconnaissance vocale personnalisés pour créer des transcriptions spécifiques au secteur. Pour plus d’informations, consultez [Personnaliser un modèle de langue depuis le site web Video Indexer](customize-language-model-with-website.md) et [Personnaliser un modèle de langue avec l’API Video Indexer](customize-language-model-with-api.md).
- **Énumération de l’orateur** : Mappe et comprend quel orateur a prononcé tels mots et à quel moment.
- **Statistiques de l’orateur** : Fournit des statistiques pour les ratios de parole des orateurs.
- **Modération du contenu textuel** : Détecte le texte explicite dans la transcription audio.
- **Effets audio** : Identifie les effets audio tels que les applaudissements, la parole et le silence.
- **Détection d’émotions** : Identifie des émotions en fonction de la voix (ce qui est dit) et de la tonalité (la façon de parler).  L’émotion peut être : le bonheur, la tristesse, la colère ou la peur.
- **Traduction** : Crée des traductions de la transcription audio en 54 langues différentes.

## <a name="audio-and-video-insights-multi-channels"></a>Insights audio et vidéo (plusieurs canaux)

Lorsque l’indexation par le résultat partiel d’un canal pour ces modèles sera disponible

- **Extraction de mots-clés** : Extrait les mots clés du texte visuel ou vocal.
- **Extraction de marques** : Extrait les marques du texte visuel ou vocal.
- **Inférence de la rubrique** : Fait des inférences des principales rubriques à partir de transcriptions. La taxonomie IPTC de premier niveau est incluse.
- **Artefacts** : Extrait un ensemble complet d’artefacts d’un « niveau suivant de détails » pour chacun des modèles.
- **Analyse des sentiments** : Identifie les sentiments positifs, négatifs et neutres à partir de la reconnaissance vocale et visuelle du texte.
 
Une fois le traitement et l’analyse terminés par Video Indexer, vous pouvez consulter, organiser, rechercher et publier des aperçus de la vidéo.

Si votre rôle est gestionnaire de contenu ou développeur, le service Video Indexer peut répondre à vos besoins. Les gestionnaires de contenu peuvent utiliser le portail web de Video Indexer pour utiliser le service sans avoir à écrire une seule ligne de code, voir [Prise en main à l’aide du site web Video Indexer](video-indexer-get-started.md). Les développeurs peuvent tirer parti des API pour traiter le contenu à l’échelle, voir [Utiliser l’API REST Video Indexer](video-indexer-use-apis.md). Le service permet également aux clients d’utiliser des widgets pour publier des flux vidéo et des aperçus extraits dans leurs propres applications, voir [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md).

Vous pouvez vous inscrire à ce service à l’aide d’un compte AAD, LinkedIn, Facebook, Google ou MSA. Pour plus d’informations, consultez [Prise en main](video-indexer-get-started.md).

## <a name="scenarios"></a>Scénarios

Voici quelques scénarios où Video Indexer peut être utile

- Recherche : les aperçus extraits de la vidéo peuvent être utilisés pour améliorer l’expérience de recherche au sein d’une bibliothèque vidéo. Par exemple, l’indexation des visages et des mots prononcés peut permettre de trouver les moments spécifiques d’une vidéo où une personne particulière a prononcé certains mots ou le moment où deux personnes se sont vues. La recherche basée sur de tels aperçus de vidéos s’applique aux agences de presse, aux instituts de formation, aux diffuseurs, aux propriétaires de contenu de divertissement, aux LOB d’entreprise et d’une façon générale à n’importe quel secteur d’activité possédant une bibliothèque vidéo au sein de laquelle les utilisateurs doivent faire des recherches.
- Création de contenu : les informations extraites des vidéos et l’aide permettent de créer efficacement du contenu tel que des amorces vidéo de fin, du contenu multimédia social, des extraits de nouvelles, etc., à partir de contenu existant dans les archives de l’organisation 
- Monétisation : Video Indexer peut aider à améliorer la valeur des vidéos. Par exemple, les secteurs d’activité s’appuyant sur le chiffre d’affaires publicitaire (par exemple, les médias d’information, les médias sociaux, etc.), peuvent fournir des publicités plus pertinentes en utilisant les informations extraites comme des signaux supplémentaires pour le serveur publicitaire (le fait de présenter des chaussures de sport est plus pertinent au milieu d’un match de football que lors d’une compétition de natation).
- Engagement utilisateur : les aperçus de vidéo peuvent être utilisés pour améliorer l’engagement utilisateur en leur présentant les moments pertinents d’une vidéo. Par exemple, imaginez une vidéo de formation donnant des explications sur des sphères au cours des 30 premières minutes, puis sur les pyramides au cours des 30 minutes suivantes. Le fait que la vidéo soit positionnée automatiquement pour démarrer au début des 30 dernières minutes pourrait bénéficier à un étudiant se documentant sur les pyramides.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à vous lancer avec Video Indexer. Pour plus d’informations, consultez les articles suivants :

- [Prise en main du site web Video Indexer](video-indexer-get-started.md)
- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)
