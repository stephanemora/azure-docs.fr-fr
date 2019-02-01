---
title: Prise en main de l’API Custom Speech Service sur Azure | Microsoft Docs
description: Abonnez-vous au service Discours personnalisé et liez les activités de service à un abonnement Azure pour former un modèle et effectuer un déploiement.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 7392459f0b80558aac22bd585c0d30bf4105d76f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224443"
---
# <a name="get-started-with-custom-speech-service"></a>Prise en main du service Discours personnalisé

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Découvrez les principales fonctionnalités du service Discours personnalisé et apprenez à créer, déployer et utiliser des modèles de langage et acoustiques pour vos besoins en application. Vous pouvez trouver plus de documentations et guides étape par étape une fois inscrit sur le portail du service Discours personnalisé.

## <a name="samples"></a>Exemples  
Nous vous fournissons un bon exemple à [cette adresse](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Prérequis  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>S’abonner au service Discours personnalisé et obtenir une clé d’abonnement
Avant d’expérimenter avec l’exemple ci-dessus, vous devez vous abonner au service Discours personnalisé et obtenir une clé d’abonnement. Consultez [Abonnements](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) ou suivez les instructions à [cette adresse](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Les clés primaire et secondaire peuvent toutes deux être utilisées dans ce tutoriel. Assurez-vous de suivre les meilleures pratiques pour garder votre clé API secrète et sûre.

### <a name="get-the-client-library-and-example"></a>Obtenir la bibliothèque cliente et l’exemple
Vous pouvez télécharger une bibliothèque de client et un exemple via [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Le fichier zip téléchargé doit être extrait dans un dossier de votre choix, de nombreux utilisateurs choisissent le dossier de Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Création d’un modèle acoustique personnalisé
Pour personnaliser un modèle acoustique pour un domaine particulier, une collection de données vocales est nécessaire. Cette collection se compose d’un ensemble de fichiers audio de données vocales et d’un fichier texte de transcriptions pour chaque fichier audio. Les données audio doivent être représentatives du scénario où vous voulez utiliser le module de reconnaissance.

Par exemple :  Si vous voulez mieux reconnaître la parole dans un environnement bruyant comme une usine, les fichiers audio doivent être constitués d’enregistrements de personnes parlant dans une usine bruyante.
Si vous vous intéressez à l’optimisation des performances pour un seul locuteur, vous pouvez par exemple transcrire tous les Fireside Chats de Franklin D. Roosevelt. Les fichiers audio doivent dans ce cas être constitués de nombreux exemples exclusivement de ce locuteur.

Vous pouvez trouver une description détaillée de la marche à suivre pour créer un modèle acoustique personnalisé à [cette adresse](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Création d’un modèle de langage personnalisé
La procédure de création d’un modèle de langage personnalisé est similaire à la création d’un modèle acoustique personnalisé, sauf qu’il n’y aucune donnée vocale, seulement du texte. Le texte doit être composé de plusieurs exemples de requêtes et d’énoncés susceptibles d’être soumis par les utilisateurs, ou déjà soumis et renseignés par ces derniers dans votre application.

Vous pouvez trouver une description détaillée de la marche à suivre pour créer un modèle de langage personnalisé à [cette adresse](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Créer un point de terminaison de reconnaissance vocale personnalisé
Une fois que vous avez créé des modèles acoustiques et/ou des modèles de langage personnalisés, vous pouvez déployer ces derniers dans un point de terminaison de reconnaissance vocale personnalisé. Pour créer un nouveau point de terminaison personnalisé, cliquez sur « Déploiements » dans le menu « CRIS » en haut de la page. Vous accédez ainsi à un tableau nommé « Déploiements » de points de terminaison personnalisés actuels. Si vous n’avez pas encore créé de point de terminaison, le tableau est vide. Les paramètres régionaux actuels sont indiqués dans le titre de la table. Si vous souhaitez créer un déploiement pour une autre langue, cliquez sur « Changer les paramètres régionaux ». D’autres informations sur les langues prises en charge sont disponibles dans la section relative au changement des paramètres régionaux.

Vous pouvez trouver une description détaillée de la marche à suivre pour créer un point de terminaison de reconnaissance vocale personnalisé à [cette adresse](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Utilisation d’un point de terminaison de reconnaissance vocale personnalisé
Les requêtes peuvent être envoyées au point de terminaison de reconnaissance vocale CRIS d’une façon similaire au point de terminaison de reconnaissance vocale par défaut de Microsoft Cognitive Services. Remarque : ces points de terminaison sont identiques en termes de fonction aux points de terminaison par défaut de l’API Microsoft Speech. Ainsi, cette même fonctionnalité, disponible via la bibliothèque de client ou l’API REST pour l’API Microsoft Speech, est également disponible pour votre point de terminaison personnalisé.

Vous pouvez trouver une description détaillée de la marche à suivre pour utiliser un point de terminaison de reconnaissance vocale personnalisé à [cette adresse](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Remarque : les points de terminaison créés via CRIS peuvent traiter un nombre varié de requêtes simultanées, en fonction du niveau de l’abonnement associé. Si un nombre supérieur de reconnaissances est demandé, le code d’erreur 429 (Requêtes trop nombreuses) sera renvoyé. Pour plus d’informations, consultez les [informations sur la tarification](https://www.microsoft.com/cognitive-services/en-us/pricing). En outre, le niveau gratuit dispose d’un quota de requête mensuel. Si vous accédez à votre point de terminaison au niveau gratuit et que vous dépassez le quota mensuel, le service renvoie le code d’erreur 403 Interdit.

Le service suppose que les données audio sont transmises en temps réel. Si elles sont envoyées plus rapidement, la requête sera considérée en exécution jusqu’à la fin de sa durée en temps réel.

* [Vue d'ensemble](cognitive-services-custom-speech-home.md)
* [FORUM AUX QUESTIONS](cognitive-services-custom-speech-faq.md)
* [Glossaire](cognitive-services-custom-speech-glossary.md)
