---
title: 'Tutoriel : Modérer les images de produits e-commerce - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Configurez une application pour analyser et classer des images de produits avec des étiquettes spécifiées (à l’aide de l’API Azure Vision par ordinateur et du service Azure Vision personnalisée), et marquez les images répréhensibles à des fins de vérification (à l’aide d’Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5c4d2320ffd54054eb8a5bb26ef14c8e99dabb33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855952"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Didacticiel : Modérer les images de produits e-commerce avec Azure Content Moderator

Dans ce tutoriel, vous allez apprendre à utiliser les services cognitifs Azure, notamment Content Moderator, pour classer et modérer efficacement des images de produits dans le cadre d’un scénario e-commerce. Vous allez utiliser Vision par ordinateur et Vision personnalisée pour appliquer différentes étiquettes aux images, puis créer une révision d’équipe, qui associe les technologies de Content Moderator basées sur le machine learning à des équipes de révision humaine pour fournir un système de modération intelligente.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> * Inscription à Content Moderator et création d’une équipe de révision.
> * Utilisation de l’API image Content Moderator pour analyser du contenu pour adultes ou osé.
> * Utilisation du service Vision par ordinateur pour rechercher du contenu sur les célébrités (ou d’autres étiquettes détectables par Vision par ordinateur).
> * Utilisation du service Vision personnalisée pour rechercher la présence de drapeaux, jouets et stylos (ou autres étiquettes personnalisées).
> * Présentation des résultats d’analyses combinées pour révision par un opérateur humain et prise de décision finale.

L’exemple de code complet est disponible dans le dépôt [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) sur GitHub.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Une clé d’abonnement Content Moderator. Suivez les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service Content Moderator et obtenir votre clé.
- Une clé d’abonnement Vision par ordinateur (mêmes instructions que ci-dessus).
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- Un ensemble d’images pour chaque étiquette que le classifieur Vision personnalisée utilisera (en l’occurrence, jouets, stylos et drapeaux américains).

## <a name="create-a-review-team"></a>Créer une équipe de révision

Reportez-vous au guide de démarrage rapide [Essayer Content Moderator sur le web](quick-start.md) pour obtenir des instructions sur la façon de vous inscrire à l’[Outil de révision Content Moderator](https://contentmoderator.cognitive.microsoft.com/) et de créer une équipe de révision. Prenez note de la valeur de l’**ID de l’équipe** dans la page **Informations d’identification**.

## <a name="create-custom-moderation-tags"></a>Créer des étiquettes de modération personnalisées

Ensuite, créez des étiquettes personnalisées dans l’outil de révision (reportez-vous à l’article [Étiquettes](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) si vous avez besoin d’aide). En l’occurrence, nous allons ajouter les étiquettes suivantes : **célébrité**, **USA**, **drapeau**, **jouet** et **stylo**. Notez que toutes les étiquettes doivent être des catégories détectables dans Vision par ordinateur (comme **célébrité**) ; vous pouvez ajouter vos propres étiquettes personnalisées, à condition que vous entraîniez le classifieur Vision personnalisée à leur détection par la suite.

![Configurer les balises personnalisées](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Création d’un projet Visual Studio

1. Dans Visual Studio, ouvrez la boîte de dialogue Nouveau projet. Développez **Installé** et **Visual C#** , puis sélectionnez **Application console (.NET Framework)**.
1. Nommez l’application **EcommerceModeration**, puis cliquez sur **OK**.
1. Si vous ajoutez ce projet à une solution existante, sélectionnez-le comme projet de démarrage unique.

Ce tutoriel met en lumière le code qui est essentiel pour le projet, mais ne couvre pas chaque ligne de code nécessaire. Copiez le contenu complet de _Program.cs_ à partir de l’exemple de projet ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) dans le fichier _Program.cs_ de votre nouveau projet. Ensuite, parcourez les sections suivantes pour en savoir plus sur le fonctionnement du projet et son utilisation.

## <a name="define-api-keys-and-endpoints"></a>Définir les clés et points de terminaison API

Comme mentionné plus haut, ce tutoriel utilise trois services cognitifs ; ainsi, il requiert trois clés et points de terminaison API correspondants. Observez les champs suivants dans la classe **Program** : 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Vous devez mettre à jour les champs `___Key` avec les valeurs de vos clés d’abonnement (vous obtiendrez la `CustomVisionKey` plus tard), et éventuellement changer les champs `___Uri` pour qu’ils contiennent les identificateurs de région appropriés. Renseignez la partie `YOURTEAMID` du champ `ReviewUri` avec l’ID de l’équipe de révision que vous avez créée précédemment. Vous renseignerez la dernière partie du champ `CustomVisionUri` plus tard.

## <a name="primary-method-calls"></a>Appels de la méthode principale

Observez le code suivant dans la méthode **Main**, qui effectue une itération sur une liste d’URL d’images. Il analyse chaque image avec les trois services différents, enregistre les étiquettes appliquées dans le tableau **ReviewTags**, puis crée une révision pour des modérateurs humains (envoi des images à l’outil de révision Content Moderator). Vous allez explorer ces méthodes dans les sections suivantes. Notez qu’ici, si vous le souhaitez, vous pouvez contrôler quelles images sont envoyées à des fins de révision, en utilisant le tableau **ReviewTags** dans une instruction conditionnelle pour vérifier les étiquettes qui ont été appliquées.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Méthode EvaluateAdultRacy

Observez la méthode **EvaluateAdultRacy** dans la classe **Program**. Cette méthode prend l’URL d’une image et un tableau de paires clé/valeur comme paramètres. Elle appelle l’API Image Content Moderator (en utilisant REST) pour obtenir les scores de l’image pour Adultes et Osé. Si le score de l’une de ces catégories est supérieur à 0,4 (la plage est de 0 à 1), elle définit la valeur correspondante dans le tableau **ReviewTags** sur **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>Méthode EvaluateCustomVisionTags

La méthode suivante prend une URL d’image et vos informations d’abonnement Vision par ordinateur et recherche la présence de célébrités dans l’image. Si une ou plusieurs célébrités sont trouvées, elle définit la valeur correspondante dans le tableau **ReviewTags** sur **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Méthode EvaluateCustomVisionTags

Ensuite, observez la méthode **EvaluateCustomVisionTags**, qui classifie les produits proprement dits&mdash;en l’occurrence, les drapeaux, jouets et stylos. Suivez les instructions du guide [Comment créer un classifieur](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) pour créer votre propre classifieur d’image personnalisé afin de détecter la présence de drapeaux, jouets et stylos (ou de ce que vous avez choisi comme étiquettes personnalisées) dans les images.

![Page web Vision personnalisée avec images d’entraînement de stylos, jouets et drapeaux](images/tutorial-ecommerce-custom-vision.PNG)

Après avoir entraîné votre classifieur, obtenez la clé de prédiction et l’URL de point de terminaison de prédiction (consultez [Obtenir l’URL et la clé de prédiction](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) si vous avez besoin d’aide pour les récupérer) et affectez ces valeurs à vos champs `CustomVisionKey` et `CustomVisionUri`, respectivement. La méthode utilise ces valeurs pour interroger le classifieur. Si le classifieur détecte une ou plusieurs étiquettes personnalisées dans l’image, cette méthode définit la ou les valeurs correspondantes dans le tableau **ReviewTags** sur **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Créer des révisions pour l’outil de révision

Les sections précédentes vous ont montré les méthodes d’analyse d’images entrantes à la recherche de contenus pour adultes et osé (Content Moderator), sur des célébrités (Vision par ordinateur) et autres objets (Vision personnalisée). À présent, observez la méthode **CreateReview**, qui charge les images ainsi que toutes leurs étiquettes appliquées (transmises en tant que _Metadata_), dans l’Outil de révision Content Moderator afin qu’elles puissent faire l’objet d’une révision humaine. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Les images apparaissent sous l’onglet Review de l’[outil de révision Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Capture d’écran de l’outil de révision Content Moderator avec plusieurs images et étiquettes correspondantes en surbrillance](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Envoyer une liste d’images de test

Comme vous pouvez le voir dans la méthode **Main**, ce programme recherche un répertoire « C:Test » avec un fichier _Urls.txt_ qui contient une liste d’URL d’images. Créez un fichier et un répertoire de ce type ou changez le chemin d’accès afin qu’il pointe vers votre fichier texte et remplissez ce fichier avec les URL des images que vous souhaitez tester.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Exécuter le programme

Si vous avez suivi toutes les étapes ci-dessus, le programme doit traiter chaque image (en interrogeant les trois services en fonction des étiquettes appropriées), puis charger les images avec les informations d’étiquettes dans l’outil de révision Content Moderator.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré un programme pour analyser des images de produits afin de les étiqueter par type de produit et de permettre à une équipe de révision de prendre les décisions appropriées du point de vue de la modération du contenu. À présent, découvrez les détails de la modération des images.

> [!div class="nextstepaction"]
> [Réviser les images modérées](./review-tool-user-guide/review-moderated-images.md)
