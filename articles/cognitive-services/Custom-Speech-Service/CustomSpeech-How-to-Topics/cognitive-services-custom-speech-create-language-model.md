---
title: 'Didacticiel : Créer un modèle de langage - Custom Speech Service'
titlesuffix: Azure Cognitive Services
description: Dans ce didacticiel, découvrez comment créer un modèle de langage avec Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: b7216688efb6a2124748cd08111d57b122085143
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219311"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutoriel : Créer un modèle de langage personnalisé

Dans ce didacticiel, vous créez un modèle de langage personnalisé pour les requêtes de texte ou les énoncés que les utilisateurs sont susceptibles de formuler ou de saisir dans une application. Vous pouvez ensuite utiliser ce modèle de langage personnalisé avec des modèles vocaux existants de Microsoft pour ajouter l’interaction vocale à votre application.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Préparer les données
> * Importer le jeu de données de langage
> * Créer le modèle de langage personnalisé

Si vous n’avez pas de compte Cognitive Services, créez un [compte gratuit](https://cris.ai) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vérifiez que votre compte Cognitive Services est connecté à un abonnement en ouvrant la page [Cognitive Services Subscriptions](https://cris.ai/Subscriptions) (Abonnements Cognitive Services).

Si aucun abonnement n’est répertorié, un compte Cognitive Services peut être créé pour vous en cliquant sur **Get free subscription** (Obtenir un abonnement gratuit). Vous pouvez également vous connecter à un abonnement Custom Search Service créé dans le portail Azure en cliquant sur le bouton **Connect existing subscription** (Se connecter à un abonnement existant).

Pour plus d’informations sur la création d’un abonnement Custom Search Service dans le portail Azure, consultez [Créer un compte d’API Cognitive Services sur le Portail Azure](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Préparer les données

Pour créer un modèle de langage personnalisé pour votre application, vous devez fournir une liste d’exemples d’énoncés au système, par exemple :

*   « Il a eu de l’urticaire la dernière semaine.»
*   « Le patient avait une hernioplastie bien cicatrisée.»

Les phrases ne doivent pas être des phrases complètes ou être grammaticalement correctes, et doivent refléter précisément l’entrée vocale que le système est susceptible de rencontrer dans le déploiement. Ces exemples doivent refléter le style et le contenu de la tâche que les utilisateurs effectueront avec votre application.

Les données du modèle de langage doivent être écrites dans un fichier texte brut avec le code US-ASCII ou UTF-8, selon les paramètres régionaux. Pour en-US, les deux encodages sont pris en charge. Pour zh-CN, seul UTF-8 est pris en charge (la marque d’ordre d’octet est facultative). Le fichier texte doit contenir un exemple (phrase, énoncé ou requête) par ligne.

Si vous souhaitez que certaines phrases aient plus de poids (d’importance), vous pouvez les ajouter plusieurs fois à vos données. Un nombre approprié de répétitions est compris entre 10 et 100. Si vous le normalisez à 100, vous pouvez pondérer facilement la phrase par rapport à celui-ci.

Les principales conditions requises pour les données de langage sont résumées dans le tableau suivant.

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | en-US : US-ASCII ou UTF-8 ou zh-CN : UTF-8|
| Nb d’énoncés par ligne | 1 |
| Taille maximale du fichier | 200 Mo |
| Remarques | éviter de répéter des caractères plus de 4 fois, par exemple « aaaaa »|
| Remarques | aucun caractère spécial tel que « \t » ou tout autre caractère UTF-8 au-dessus de U+00A1 dans le [tableau de caractères Unicode](http://www.utf8-chartable.de/)|
| Remarques | les URI seront également rejetées, car il est impossible de prononcer une URI|

Lorsque le texte est importé, il est normalisé pour pouvoir être traité par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données. Consultez les [Instructions concernant la transcription](cognitive-services-custom-speech-transcription-guidelines.md) pour déterminer la langue appropriée lors de la préparation de vos données de langage.

## <a name="import-the-language-data-set"></a>Importer le jeu de données de langage

Cliquez sur le bouton « Importer » dans la ligne « Acoustic Datasets » (Jeux de données acoustiques), le site affiche une page de chargement d’un nouveau jeu de données.

Lorsque vous êtes prêt à importer votre jeu de données de langage, connectez-vous au [portail Custom Speech Service](https://cris.ai).  Cliquez ensuite sur le menu déroulant « Custom Speech » dans le ruban supérieur, puis sélectionnez « Adaptation Data » (Données d’adaptation). S’il s’agit de votre premier chargement de données vers Custom Speech Service, vous verrez un tableau vide appelé « Jeux de données ».

Pour importer un nouveau jeu de données, cliquez sur le bouton « Importer » dans la ligne « Language Datasets » (Jeux de données de langage), le site affiche une page de chargement d’un nouveau jeu de données. Entrez un nom et une description pour vous aider à identifier le jeu de données ultérieurement. Utilisez ensuite le bouton « Sélectionner un fichier» pour rechercher le fichier texte de données de langage. Puis cliquez sur « Importer », le jeu de données est chargé. Selon la taille du jeu de données, l’opération peut durer plusieurs minutes.

![Essayer](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

Lorsque l’importation est terminée, vous revenez à la table de données de langage et vous verrez une entrée qui correspond à votre jeu de données de langage. Notez qu’un ID unique (GUID) lui a été attribué. Les données contiennent également un état qui reflète leur état actuel. L’état sera « En attente » lorsqu’elles sont en file d’attente de traitement, « Traitement en cours » pendant la phase de validation et « Terminé » lorsque les données sont prêtes à être utilisées. La validation des données procède à une série de vérifications sur le texte du fichier et à une normalisation du texte des données.

Lorsque l’état est « Terminé », vous pouvez cliquer sur « Afficher le rapport » pour afficher le rapport de vérification des données de langage. Le nombre d’énoncés dont la vérification a réussi ou échoué s’affiche, ainsi que des détails sur les énoncés ayant échoué. Dans l’exemple ci-dessous, la vérification a échoué pour deux exemples en raison de caractères incorrects (dans ce jeu de données, le premier exemple contient deux émoticônes et le second plusieurs caractères en dehors du jeu de caractères imprimables ASCII).

![Essayer](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

Lorsque l’état du jeu de données de langage est « Terminé », il peut être utilisé pour créer un modèle de langage personnalisé.

![Essayer](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Créer un modèle linguistique personnalisé

Lorsque vos données de langage sont prêtes, cliquez sur « Language Models » (Modèles de langage) dans le menu déroulant « Menu » pour démarrer le processus de création de modèle de langage personnalisé. Cette page contient une table appelée « Language Models » (Modèles de langage) avec vos modèles de langage personnalisés actuels. Si vous n’avez pas encore créé de modèles de langage personnalisés, la table est vide. Les paramètres régionaux actuels sont indiqués dans le titre de la table. Si vous souhaitez créer un modèle de langage pour une autre langue, cliquez sur « Changer les paramètres régionaux ». D’autres informations sur les langues prises en charge sont disponibles dans la section relative au [changement des paramètres régionaux](cognitive-services-custom-speech-change-locale.md). Pour créer un nouveau modèle, cliquez sur le lien « Créer nouveau » sous le titre de la table.

Sur la page « Create Language Model» (Créer un modèle de langage), entrez un « Nom » et une « Description » pour vous aider à suivre les informations utiles sur ce modèle comme le jeu de données utilisé. Sélectionnez ensuite « Base Language Model » (Modèle de langage de base) dans le menu déroulant. Ce modèle sera le point de départ de votre personnalisation. Deux modèles de langage de base existants peuvent être sélectionnés. Le modèle _Microsoft Search and Dictation LM_ est approprié pour la voix dans une application comme les commandes, les requêtes de recherche ou la dictée. Le modèle _Microsoft Conversational LM_ est approprié pour la reconnaissance vocale dans un style conversationnel. Ce type de voix s’adresse généralement à une autre personne et se retrouve dans les centres d’appel ou les réunions.

Une fois que vous avez spécifié le modèle de langage de base, sélectionnez le jeu de données de langage à utiliser pour la personnalisation à l’aide du menu déroulant « Language Data » (Données de langage)

![Essayer](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Comme avec la création d’un modèle acoustique, vous pouvez choisir de tester votre nouveau modèle en mode hors connexion lorsque le traitement est terminé. S’agissant d’une évaluation de la reconnaissance vocale de texte, le test hors connexion requiert un jeu de données acoustiques.

Pour tester votre modèle de langage en mode hors connexion, cochez la case en regard de « Tests hors ligne ». Sélectionnez ensuite un modèle acoustique dans le menu déroulant. Si vous n’avez créé de modèle acoustique personnalisé, le modèle acoustique de base de Microsoft sera le seul modèle dans le menu. Si vous avez sélectionné un modèle de base LM conversationnel, vous devez utiliser un modèle AM conversationnel ici. Si vous utilisez une recherche et dictez le modèle LM, vous devez sélectionner une recherche et un modèle AM de dictée.

Enfin, sélectionnez le jeu de données acoustiques que vous souhaitez utiliser pour l’évaluation.

Lorsque vous êtes prêt à commencer le traitement, appuyez sur « Créer ». Vous revenez alors à la table des modèles de langage. Une nouvelle entrée correspondant à ce modèle apparaitra dans la table. L’état reflète l’état du modèle et passe par plusieurs états, notamment « En attente », « Traitement en cours » et « Terminé ».

Lorsque le modèle atteint l’état « Terminé », il peut être déployé sur un point de terminaison. Cliquez sur « View Result » (Afficher le résultat) pour afficher les résultats du test hors connexion, s’il est effectué.

Pour modifier à tout moment le « nom » ou la « description » du modèle, vous pouvez utiliser le lien « Modifier » de la ligne concernée dans la table de modèles de langage.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez développé un modèle de langage personnalisé à utiliser avec du texte. Pour créer un modèle acoustique personnalisé à utiliser avec des fichiers audio et des transcriptions, passez au didacticiel de création d’un modèle acoustique.

> [!div class="nextstepaction"]
> [Créer un modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md)
