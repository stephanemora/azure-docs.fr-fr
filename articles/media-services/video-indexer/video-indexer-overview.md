---
title: Qu’est-ce qu’Azure Media Services Video Indexer ?
titleSuffix: Azure Media Services
description: Cette rubrique offre une vue d’ensemble du service Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/23/2019
ms.author: juliako
ms.openlocfilehash: 3c8d0d3ec0f11592610363e9150be47112995853
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219858"
---
# <a name="what-is-video-indexer"></a>Qu’est-ce que Video Indexer ?

Video Indexer (VI) est la solution IA Azure Media Services. Elle fait partie de la marque Microsoft Cognitive Services. Video Indexer permet d’extraire des insights détaillés (sans avoir besoin de compétences en matière d’analyse des données ou de programmation) à l’aide de modèles Machine Learning basés sur plusieurs canaux (voix, visuel). Vous pouvez affiner la personnalisation et l’entraînement des modèles. Le service permet d’effectuer une recherche détaillée, réduit les coûts d’exploitation, offre de nouvelles opportunités de monétisation et de nouvelles expériences utilisateur sur un grand nombre de vidéos archivées (avec de faibles barrières d’entrée). 

Pour commencer à extraire des insights avec Video Indexer, vous devez créer un compte et charger des vidéos. Quand vous chargez vos vidéos dans Video Indexer, il analyse les éléments audio-visuels en exécutant différents modèles d’IA. À mesure que Video Indexer analyse votre vidéo, les insights sont extraits par les modèles.

Le diagramme suivant est une illustration et non une explication technique de la façon dont Video Indexer fonctionne dans le back-end.

![Diagramme de flux](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Que puis-je faire avec Video Indexer ?

Les insights de Video Indexer peuvent être appliqués à de nombreux scénarios, parmi lesquels :

* *Recherche approfondie* : utilisez les insights extraits de la vidéo pour améliorer l’expérience de recherche au sein d’une bibliothèque vidéo. Par exemple, l’indexation des visages et des mots prononcés peut permettre de trouver les moments spécifiques d’une vidéo où une personne a prononcé certains mots ou le moment où deux personnes se sont vues. La recherche basée sur de tels aperçus de vidéos s’applique aux agences de presse, aux instituts de formation, aux diffuseurs, aux propriétaires de contenu de divertissement, aux LOB d’entreprise et d’une façon générale à n’importe quel secteur d’activité possédant une bibliothèque vidéo au sein de laquelle les utilisateurs doivent faire des recherches.
* *Création de contenu* : créez des bandes-annonces, des séquences de meilleurs moments, du contenu de réseaux sociaux ou des clips d’actualité basés sur des insights Video Indexer extraits de votre contenu. Les images clés, les marqueurs de scènes et les horodatages pour les apparences des étiquettes et des personnes rendent le processus de création beaucoup plus lisse et plus simple, et vous permettent d’accéder directement aux parties intéressantes de la vidéo dont vous avez besoin pour le contenu que vous créez.
* *Accessibilité* : si vous souhaitez que votre contenu soit disponible pour les personnes handicapées ou qu’il soit distribué dans différentes régions en différentes langues, vous pouvez utiliser la transcription et la traduction fournies par Video Indexer dans plusieurs langues.
* *Monétisation* : Video Indexer peut aider à augmenter la valeur des vidéos. Par exemple, les secteurs d’activité s’appuyant sur le chiffre d’affaires publicitaire (tels que les médias d’information, les médias sociaux, et ainsi de suite), peuvent fournir des publicités pertinentes en utilisant les insights extraits en tant que signaux supplémentaires pour le serveur publicitaire.
* *Modération du contenu* : utilisez des modèles de modération de contenu textuel et visuel afin de protéger vos utilisateurs contre le contenu inapproprié et de vous assurer que le contenu que vous publiez correspond aux valeurs de votre organisation. Vous pouvez bloquer automatiquement certaines vidéos ou alerter vos utilisateurs au sujet du contenu. 
* *Suggestions* : les insights vidéo peuvent être utilisés pour améliorer l’engagement utilisateur en soulignant les moments intéressants d’une vidéo. En marquant chaque vidéo avec des métadonnées supplémentaires, vous pouvez recommander aux utilisateurs les vidéos les plus pertinentes et mettre en évidence la partie de la vidéo qui répondra à leurs besoins. 

## <a name="features"></a>Caractéristiques

Voici la liste des insights que vous pouvez récupérer à partir de vos vidéos à l’aide des modèles vidéo et audio de Video Indexer :

### <a name="video-insights"></a>Insights vidéo

* **Détection de visage** : Détecte et regroupe les visages qui apparaissent dans la vidéo.
* **Identification de célébrités** : Video Indexer identifie automatiquement plus de 1 million de célébrités : dirigeants, acteurs et actrices, athlètes, chercheurs, dirigeants d’entreprise et spécialistes techniques du monde entier. Les données relatives à ces célébrités figurent également sur plusieurs sites Web connus, par exemple, IMDB et Wikipedia.
* **Identification des visages basée sur le compte** : Video Indexer effectue l’apprentissage d’un modèle pour un compte spécifique. Il reconnaît ensuite les visages dans la vidéo en fonction du modèle formé. Pour plus d’informations, consultez [Personnaliser un modèle de personne depuis le site web Video Indexer](customize-person-model-with-website.md) et [Personnaliser un modèle de personne avec l’API Video Indexer](customize-person-model-with-api.md).
* **Extraction de miniatures pour les visages (« meilleurs visages »)**  : Identifie automatiquement le meilleur visage capturé dans chaque groupe de visages (en fonction de la qualité, de la taille et de la position frontale) et l’extrait en tant qu’actif d’image.
* **Reconnaissance visuelle du texte** (OCR) : Extrait le texte qui s’affiche visuellement dans la vidéo.
* **Modération du contenu visuel** : Détecte les contenus choquants et/ou destinés aux adultes.
* **Identification des étiquettes** : Identifie les objets visuels et les actions affichés.
* **Segmentation de la scène** : détermine quand une scène change dans la vidéo via des signaux visuels. Une scène représente un événement unique et est composée d’une série de captures consécutives, qui sont sémantiquement liées.
* **Détection de plan** : détermine quand un plan change dans la vidéo via des signaux visuels. Un plan est une série d’images prises par la même caméra. Pour plus d’informations, consultez Scènes, plans et images clés.
* **Détection de cadre noir** : Identifie les cadres noirs présents dans la vidéo.
* **Extraction d’images clés** : Détecte les images clés stables dans une vidéo.
* **Déploiement de crédits** : identifie le début et la fin des crédits à la fin des émissions de télévision et des films.
* **Détection de personnages animés** (préversion) : détection, regroupement et reconnaissance de personnages dans le contenu animé via l’intégration avec [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Pour plus d’informations, consultez [Détection de personnages animés](animated-characters-recognition.md).
* **Détection du type de plan éditorial** : marquage de plans en fonction de leur type (par exemple, plan large, plan moyen, gros plan, très gros plan, deux plans, plusieurs personnes, extérieur et intérieur, etc.). Pour plus d’informations, consultez [Détection du type de plan éditorial](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Insights audio

* **Détection de langue automatique** : Identifie automatiquement la langue parlée dominante. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, le russe et le portugais (Brésil). Si la langue ne peut pas être identifiée en toute confiance, Video Indexer suppose que la langue parlée est l’anglais. Pour plus d'informations, voir [Modèle d’identification de langues](language-identification-model.md).
* **Identification et transcription de discours en plusieurs langues** (préversion) : identifie automatiquement la langue parlée dans différents segments à partir de l’audio, en envoyant chaque segment du fichier multimédia à transcrire et en regroupant toutes les transcriptions en une seule. Pour plus d’informations, consultez [Identifier et transcrire automatiquement un contenu multilingue](multi-language-identification-transcription.md).
* **Transcription audio** : Convertit la parole en texte dans 12 langues et autorise les extensions. Les langues prises en charge incluent l’anglais, l’espagnol, le français, l’allemand, l’italien, le chinois (simplifié), le japonais, l’arabe, le russe, le portugais brésilien, l’hindi et le coréen.
* **Sous-titrage** : Crée un sous-titrage dans trois formats : VTT, TTML, SRT.
* **Traitement en deux canaux** : Détecte automatiquement, sépare la transcription et fusionne en une chronologie unique.
* **Réduction du bruit** : Nettoie les enregistrements contenant du bruit ou de l’audio de téléphonie (en fonction des filtres Skype).
* **Personnalisation de la transcription** (CRIS) : Entraîne des modèles de reconnaissance vocale personnalisés pour créer des transcriptions spécifiques au secteur. Pour plus d’informations, consultez [Personnaliser un modèle de langue depuis le site web Video Indexer](customize-language-model-with-website.md) et [Personnaliser un modèle de langue avec l’API Video Indexer](customize-language-model-with-api.md).
* **Énumération de l’orateur** : Mappe et comprend quel orateur a prononcé tels mots et à quel moment.
* **Statistiques de l’orateur** : Fournit des statistiques concernant les ratios de parole des orateurs.
* **Modération du contenu textuel** : Détecte le texte explicite dans la transcription audio.
* **Effets audio** : Identifie les effets audio tels que les applaudissements, la parole et le silence.
* **Détection d’émotions** : Identifie des émotions en fonction de la voix (ce qui est dit) et de la tonalité (la façon de parler). L’émotion peut être le bonheur, la tristesse, la colère ou la peur.
* **Traduction** : Crée des traductions de la transcription audio en 54 langues différentes.

### <a name="audio-and-video-insights-multi-channels"></a>Insights audio et vidéo (plusieurs canaux)

Lorsque l’indexation par le résultat partiel d’un canal pour ces modèles sera disponible

* **Extraction de mots-clés** : Extrait les mots clés du texte visuel ou vocal.
* **Extraction des entités nommées** : extrait des marques, des emplacements et des personnes à partir de la reconnaissance vocale et du texte visuel via le traitement en langage naturel (NLP).
* **Inférence de la rubrique** : Fait des inférences des principales rubriques à partir de transcriptions. La taxonomie IPTC de premier niveau est incluse.
* **Artefacts** : Extrait un ensemble complet d’artefacts d’un « niveau suivant de détails » pour chacun des modèles.
* **Analyse des sentiments** : Identifie les sentiments positifs, négatifs et neutres à partir de la reconnaissance vocale et visuelle du texte.

## <a name="how-can-i-get-started-with-video-indexer"></a>Comment bien démarrer avec Video Indexer ?

Vous pouvez accéder aux fonctionnalités de Video Indexer de trois façons différentes : 

* Portail Video Indexer : solution facile à utiliser qui vous permet d’évaluer le produit, de gérer le compte et de personnaliser les modèles. 

    Pour plus d’informations sur le portail, consultez le [site web Bien démarrer avec Video Indexer](video-indexer-get-started.md).  
* Intégration d’API : toutes les fonctionnalités de Video Indexer sont disponibles par le biais d’une API REST, afin de vous permettre d’intégrer la solution dans vos applications et votre infrastructure. 

    Pour bien démarrer en tant que développeur, consultez  [Utiliser l’API REST Video Indexer](video-indexer-use-apis.md). 
* Widget incorporable : vous permet d’incorporer les expériences d’insights, de lecteur et d’éditeur Video Indexer dans votre application. 

    Pour plus d’informations, consultez  [Incorporer des widgets Video Indexer dans vos applications](video-indexer-embed-widgets.md). 

Si vous utilisez le site web, les insights sont ajoutés en tant que métadonnées et sont visibles dans le portail. Si vous utilisez des API, les insights sont disponibles sous forme de fichier JSON. 

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à vous lancer avec Video Indexer. Pour plus d’informations, consultez les articles suivants :

- [Prise en main du site web Video Indexer](video-indexer-get-started.md)
- [Traiter du contenu avec l’API REST de Video Indexer](video-indexer-use-apis.md)
- [Incorporer des widgets visuels dans votre application](video-indexer-embed-widgets.md)
