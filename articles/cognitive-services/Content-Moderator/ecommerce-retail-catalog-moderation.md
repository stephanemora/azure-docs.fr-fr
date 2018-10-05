---
title: 'Tutoriel : Modération des catalogues eCommerce - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Modérez automatiquement les catalogues eCommerce avec le machine learning et l’intelligence artificielle.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 2405ba9087e63b57c7bd6bc6f290cdafacaf7a49
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227348"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Tutoriel : Modération des catalogues eCommerce avec le machine learning

Dans ce didacticiel, nous apprenons à implémenter une modération intelligente de catalogue eCommerce avec machine learning en combinant des technologies IA assistées par ordinateur avec modération humaine pour fournir un système intelligent de catalogue.

![Images de produits classés](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Scénario d'entreprise

Utilisez des technologies assistées par ordinateur pour classer et modérer des images de produits dans ces catégories :

1. Adulte (nudité)
2. Osé (suggestif)
3. Célébrités
4. Signes américains
5. Jouets
6. Stylos

## <a name="tutorial-steps"></a>Étapes du didacticiel

Ce tutoriel vous guide tout au long des étapes suivantes :

1. Inscription et création d’une équipe Content Moderator.
2. Configuration des balises (étiquettes) de modération pour contenu potentiel sur des signes et des célébrités.
3. Utilisation de l’API image Content Moderator pour analyser du contenu pour adultes ou osé.
4. Utilisation de l’API Vision par ordinateur pour rechercher des célébrités potentielles.
5. Utilisation du service Vision personnalisée pour analyser la présence éventuelle de signes.
6. Présentation des résultats d’analyses nuancées pour révision par un opérateur humain et prise de décision finale.

## <a name="create-a-team"></a>Créer une équipe

Reportez-vous à la page [Démarrage rapide](quick-start.md) pour vous inscrire à Content Moderator et créer une équipe. Notez l’**ID de l’équipe** présent sur la page **Informations d'identification**.


## <a name="define-custom-tags"></a>Définir des balises personnalisées

Consultez l’article [Balises](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) pour savoir comment ajouter des balises personnalisées. Outre les balises intégrées **Adultes** et **Osé**, les nouvelles balises permettent à l’outil de vérification d’afficher les noms descriptifs des balises.

Dans notre cas, nous définissons ces balises personnalisées (**célébrité**, **signe**, **us**, **jouet**, **stylo**) :

![Configurer les balises personnalisées](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Répertorier vos clés et points de terminaison API

1. Le didacticiel utilise trois API et leurs clés correspondantes ainsi que des points de terminaison d’API.
2. Vos points de terminaison d’API sont différents selon les régions de vos abonnements ainsi que l’ID de l’équipe d’analyse Content Moderator.

> [!NOTE]
> Le didacticiel est conçu pour utiliser des clés d’abonnement dans les régions visibles dans les points de terminaison suivants. Veuillez à faire correspondre vos clés API avec la région Uris, autrement vos clés risquent de ne pas fonctionner avec les points de terminaison suivants :

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Analyser du contenu pour adultes et osé

1. La fonction prend l’URL d’une image et un tableau de paires clé/valeur comme paramètres.
2. Elle appelle l’API Image Content Moderator pour obtenir les scores pour Adultes et Osé.
3. Si le score est supérieur à 0,4 (la plage est de 0 à 1), elle définit la valeur dans le tableau **ReviewTags** sur **True**.
4. Le tableau **ReviewTags** sert à mettre en surbrillance la balise correspondante dans l’outil de vérification.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Analyser du contenu sur les célébrités

1. Inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) de l’[API Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Cliquez sur le bouton **Obtenir la clé API**.
3. Acceptez les conditions.
4. Pour vous connecter, choisissez un des comptes dans la liste des comptes Internet disponibles.
5. Notez les clés API affichées sur la page de votre service.
    
   ![Clés API Vision par ordinateur](images/tutorial-computer-vision-keys.PNG)
    
6. Consultez le code source du projet de la fonction qui analyse l’image avec l’API Vision par ordinateur.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Classer en signes, jouets et stylos

1. [Connectez-vous](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/) à la [préversion de l’API Vision personnalisée](https://www.customvision.ai/).
2. Utilisez le [guide de démarrage rapide](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) pour créer un classifieur personnalisé afin de détecter la présence potentielle de signes, jouets et stylos.
   ![Image de formation Vision personnalisée](images/tutorial-ecommerce-custom-vision.PNG)
3. [Obtenez l’URL du point de terminaison de prédiction](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) de votre classifieur personnalisé.
4. Consultez le code source du projet pour voir la fonction qui appelle le point de terminaison de prédiction de votre classifieur personnalisé afin d’analyser votre image.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Révision pour opérateur humain

1. Dans les sections précédentes, vous avez analysé les images entrantes à la recherche de contenus pour adultes et osé (Content Moderator), sur des célébrités (Vision par ordinateur) et signes (Vision personnalisée).
2. Selon nos seuils de correspondance pour chaque analyse, réalisez des études nuancées disponibles pour une révision par un opérateur humain dans l’outil de révision.
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Soumettre le lot d’images

1. Ce didacticiel se base sur un répertoire « C:Test » avec un fichier texte disposant d’une liste d’URL d’images.
2. Le code suivant vérifie l’existence du fichier et lit toutes les URL dans la mémoire.
            // Check for a test directory for a text file with the list of Image URLs to scan var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Démarrer toutes les analyses

1. Cette fonction de niveau supérieur fait le tour de toutes les URL d’images dans le fichier texte dont nous parlions plus tôt.
2. Elle les analyse avec chaque API et si le score de fiabilité correspondance entre dans nos critères, elle crée une révision pour les modérateurs humains.
             // for each image URL in the file... foreach (var Url in Urls) { // Initiatize a new review tags array ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licence

Tous les SDK Microsoft Cognitive Services et les exemples sont fournis sous licence MIT. Pour plus d’informations, consultez [LICENCE](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Code de conduite du développeur

Les développeurs qui utilisent Cognitive Services, notamment cette bibliothèque cliente et cet exemple, sont supposés suivre le « Code de conduite du développeur pour Microsoft Cognitive Services », disponible à l’adresse http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Étapes suivantes

Création et extension du didacticiel en utilisant les [fichiers source du projet](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) sur Github.
