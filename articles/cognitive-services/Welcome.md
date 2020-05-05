---
title: Présentation d’Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: La solution Azure Cognitive Services est une famille d’API, de kits de développement logiciel (SDK) et de services que vous pouvez utiliser avec Microsoft Azure pour générer des applications intelligentes.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 12/19/2019
ms.author: nitinme
ms.openlocfilehash: 332f33bb4046a9ca9d6abf9bec75f60bb4ca9e32
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169090"
---
# <a name="what-are-azure-cognitive-services"></a>Présentation d’Azure Cognitive Services

La solution Azure Cognitive Services est une famille d’API, de kits de développement logiciel (SDK) et de services destinés à aider les développeurs à générer des applications intelligentes sans disposer de connaissances ou compétences directes en matière d’intelligence artificielle ou de science des données. Azure Cognitive Services permet aux développeurs d’ajouter facilement des fonctionnalités cognitives à leurs applications. L’objectif de l’équipe Azure Cognitive Services est d’aider les développeurs à créer des applications capables de voir, d’entendre, de parler, de comprendre et même de commencer à raisonner. Le catalogue des services disponibles au sein de la solution Azure Cognitive Services peut être classé en cinq grands piliers : Vision, Parole (Speech), Langage, Recherche et Décision.

## <a name="vision-apis"></a>Les API Vision

|Nom du service|Description du service|
|:-----------|:------------------|
|[Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Vision par ordinateur")|Le service Vision par ordinateur vous donne accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations.|
|[Service Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Service Vision personnalisée")|Le Service Vision personnalisée vous permet de créer des classifieurs d’images personnalisés.|
|[Visage](https://docs.microsoft.com/azure/cognitive-services/face/ "Face")| Le service Visage donne accès à des algorithmes d’analyse du visage permettant la détection et la reconnaissance d’attributs faciaux.|
|[Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer") (préversion)|Form Recognizer identifie et extrait les données de paires clé-valeur et de table des documents du formulaire ; il sort ensuite des données structurées, notamment les relations dans le fichier d’origine.|
|[Ink Recognizer](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer") (préversion)|Ink Recognizer vous permet de reconnaître et d’analyser les données de trait d’encre numérique, les formes et le contenu manuscrit, et sort une structure de document avec toutes les entités reconnues.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|Video Indexer vous permet d’extraire des informations de vos vidéos.|

## <a name="speech-apis"></a>Les API Speech

|Nom du service|Description du service|
|:-----------|:------------------|
|[Service Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Service Speech")|Le service Speech ajoute des fonctionnalités vocales aux applications.|
|[API Reconnaissance de l’orateur](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "API Speaker Recognition") (préversion)|L’API Reconnaissance de l’orateur fournit des algorithmes pour l’identification et la vérification de l’orateur.|
|[Reconnaissance vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home "Reconnaissance vocale Bing") (retrait)|L’API Reconnaissance vocale Bing vous offre un moyen facile de créer des fonctionnalités vocales dans vos applications.|
|[Traduction de conversation Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (retrait)|La Traduction de conversation Translator Speech est un service de traduction automatique.|

> [!NOTE]
> Où trouver la [Recherche cognitive Azure](https://docs.microsoft.com/azure/search/) ? Bien qu’elle ait recours à Cognitive Services pour certaines tâches, il s’agit d’une technologie de recherche différente qui prend en charge d’autres scénarios.


## <a name="language-apis"></a>Les API Langage

|Nom du service|Description du service|
|:-----------|:------------------|
|[Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Le service Language Understanding (LUIS) permet à votre application de comprendre le sens des propos d’une personne.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker vous permet de générer un service de questions et réponses à partir de votre contenu semi-structuré.|
|[Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Analyse de texte")|L’Analyse de texte effectue un traitement en langage naturel sur du texte brut pour l’analyse des sentiments, l’extraction de phrases clés et la détection de langue.|
|[Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/ "Translator Text")|La Traduction de texte Translator Text fournit une traduction de texte automatique en quasi temps réel.|


## <a name="search-apis"></a>API Recherche

|Nom du service|Description du service|
|:-----------|:------------------|
|[Recherche d’actualités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Recherche Bing Actualités")|La Recherche d’actualités Bing retourne une liste d’articles d’actualités jugés pertinents en réponse à la requête de l’utilisateur.|
|[Recherche de vidéos Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Recherche de vidéos Bing")|La Recherche de vidéos Bing retourne une liste de vidéos jugées pertinentes en réponse à la requête de l’utilisateur.|
|[Recherche Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Recherche web Bing")|La Recherche Web Bing retourne une liste de résultats de recherche jugés pertinentes en réponse à la requête de l’utilisateur.|
|[Suggestion automatique Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Suggestion automatique Bing")|La Suggestion automatique Bing vous permet d’envoyer à Bing un terme de requête de recherche partielle et d’obtenir une liste de requêtes suggérées.|
|[Recherche personnalisée Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Recherche personnalisée Bing")|La Recherche personnalisée Bing vous permet de créer des expériences de recherche sur mesure pour des sujets qui vous intéressent.|
|[Recherche d’entités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Recherche d’entité Bing")|La Recherche d’entités Bing retourne des informations sur des entités que Bing juge pertinentes en lien avec la requête d’un utilisateur.|
|[Recherche d’images Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Recherche d’images Bing")|La Recherche d’images Bing affiche des images jugées pertinentes en lien avec la requête d’un utilisateur.|
|[Recherche visuelle Bing](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Recherche visuelle Bing")|La Recherche visuelle Bing retourne des informations en relation avec une image, telles que des images visuellement similaires, des sources d’approvisionnement où acheter des produits illustrés dans l’image, et des recherches associées.|
|[Recherche d’entreprises locales Bing](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "La Recherche d’entreprises locales Bing")| L’API Recherche d’entreprises locales Bing permet à vos applications de rechercher les coordonnées d’entreprises à proximité à l’aide de requêtes.|
|[Vérification orthographique Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Vérification orthographique Bing")|La Vérification orthographique Bing vous permet d’effectuer une vérification grammaticale et orthographique contextuelle.|

## <a name="decision-apis"></a>API de décision

|Nom du service|Description du service|
|:-----------|:------------------|
|[Détecteur d’anomalies](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Le détecteur d’anomalies") (préversion)|Le Détecteur d’anomalies vous permet de surveiller et de détecter des anomalies dans vos données de série chronologique.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator assure la surveillance de contenu potentiellement choquant, indésirable ou à risque.|
|[Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")|Personalizer vous permet de choisir la meilleure expérience à montrer à vos utilisateurs, en apprenant de leur comportement en temps réel.|

## <a name="use-free-trials"></a>Utilisation des essais gratuits

L’[inscription aux essais gratuits](https://azure.microsoft.com/try/cognitive-services/ "Aide à l’inscription") ne nécessite qu’un e-mail et quelques étapes simples. Vous devez créer un compte Microsoft si vous n’en avez pas. Vous recevrez une paire unique de clés pour chaque API demandée. La seconde est une simple clé de secours. Ne partagez les clés secrètes avec personne. Les essais ont un débit limité en termes à la fois de transactions par seconde ou minute et de plafond d’utilisation mensuel. Une transaction est un simple appel d’API. Vous pouvez effectuer une mise à niveau vers des niveaux payants pour débloquer ces restrictions.

## <a name="subscription-management"></a>Gestion des abonnements

Une fois connecté avec votre compte Microsoft, vous pouvez accéder à [Mes abonnements](https://www.microsoft.com/cognitive-services/subscriptions "Mes abonnements") pour afficher les produits que vous utilisez, les quotas restants et la possibilité d’ajouter des produits à votre abonnement.

## <a name="upgrade-to-unlock-limits"></a>Mettre à niveau pour débloquer les limites

Toutes les API ont un plan d’évaluation gratuite qui présentent des limites en ce qui concerne l’utilisation et le débit.  Vous pouvez augmenter ces limites en utilisant une offre payante et en sélectionnant l’option de niveau de tarification approprié lors du déploiement du service dans le portail Azure. [En savoir plus sur les offres et la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/ "offres et tarifs"). Vous devez créer un compte d’abonné Azure avec une carte de crédit et un numéro de téléphone. Si vous avez des exigences particulières ou souhaitez simplement communiquer avec le service commercial, cliquez sur le bouton « Nous contacter » en haut de la page de tarification.

## <a name="regional-availability"></a>Disponibilité régionale

Les API figurant dans Cognitive Services sont hébergées sur un réseau croissant de centres de données Azure gérés par Microsoft. Vous trouverez la disponibilité régionale de chaque API dans la [liste des régions Azure](https://azure.microsoft.com/regions).

À la recherche d’une région encore non prise en charge ? Faites-le nous savoir en créant une demande de fonctionnalité sur notre [forum UserVoice](https://cognitive.uservoice.com/).

## <a name="supported-cultural-languages"></a>Langues prises en charge

 Cognitive Services prend en charge un large éventail de langues au niveau du service. Vous trouverez les langues disponibles pour chaque API dans la [liste des langues prises en charge](language-support.md).

## <a name="securing-resources"></a>Sécurisation des ressources

Azure Cognitive Services propose un modèle de sécurité en couches, notamment une [authentification](authentication.md) via les informations d’identification Azure Active Directory, une clé de ressource valide et les [réseaux virtuels Azure](cognitive-services-virtual-networks.md).

## <a name="container-support"></a>Support pour les conteneurs

 Cognitive Services fournit des conteneurs à déployer dans le cloud Azure ou localement. En savoir plus sur les [Conteneurs Cognitive Services](cognitive-services-container-support.md).

## <a name="certifications-and-compliance"></a>Certifications et conformité

Cognitive Services a reçu des certifications telles que la Certification de CSA STAR, FedRAMP Moderate et HIPAA BAA.

Vous pouvez [télécharger](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) des certifications pour vos propres audits et révisions de sécurité.

Pour comprendre la gestion de la confidentialité et des données, accédez au [Centre de gestion de la confidentialité](https://servicetrust.microsoft.com/).

## <a name="support"></a>Support

Cognitive Services offre plusieurs [options de support](cognitive-services-support-options.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte Cognitive Services](cognitive-services-apis-create-account.md)
