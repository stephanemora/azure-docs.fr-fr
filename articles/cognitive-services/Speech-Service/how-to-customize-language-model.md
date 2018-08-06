---
title: Comment créer un modèle de langage avec Speech Service - Microsoft Cognitive Services
description: Découvrez comment créer un modèle de langage avec Custom Speech Service dans Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 0161a691cbec45a87ade218d1707a2784d7f1cfc
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283882"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutoriel : Créer un modèle de langage personnalisé

Dans ce document, vous créez un modèle de langage personnalisé, que vous pouvez ensuite utiliser avec les modèles vocaux de pointe de Microsoft pour ajouter une interaction vocale à votre application.

Le document explique comment :
> [!div class="checklist"]
> * Préparer les données
> * Importer le jeu de données de langage
> * Créer le modèle de langage personnalisé

Si vous n’avez pas de compte Cognitive Services, créez un [compte gratuit](https://azure.microsoft.com/try/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vérifiez que votre compte Cognitive Services est connecté à un abonnement en ouvrant la page [Cognitive Services Subscriptions](https://customspeech.ai/Subscriptions) (Abonnements Cognitive Services).

Vous pouvez vous connecter à un abonnement au service Speech créé dans le portail Azure en cliquant sur le bouton **Se connecter à un abonnement existant**.

Pour plus d’informations sur la création d’un abonnement au service Speech dans le portail Azure, consultez la page [Bien démarrer](get-started.md).

## <a name="prepare-the-data"></a>Préparer les données

Pour créer un modèle de langage personnalisé pour votre application, vous devez fournir une liste d’exemples d’énoncés au système, par exemple :

*   « Le patient a eu de l’urticaire la semaine dernière. »
*   « Le patient avait une hernioplastie bien cicatrisée.»

Les phrases ne doivent pas être des phrases complètes ou être grammaticalement correctes, mais elles doivent refléter avec exactitude l’entrée vocale que le système est susceptible de rencontrer dans le déploiement. Ces exemples doivent refléter le style et le contenu de la tâche que les utilisateurs emploieront avec votre application.

Les données du modèle de langage doivent être écrites au format UTF-8 BOM. Le fichier texte doit contenir un exemple (phrase, énoncé ou requête) par ligne.

Si vous voulez que certains termes aient plus de poids (plus d’importance), vous pouvez ajouter plusieurs énoncés qui incluent ce terme à vos données. 

Les principales conditions requises pour les données de langage sont résumées dans le tableau suivant.

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM|
| Nb d’énoncés par ligne | 1 |
| Taille maximale du fichier | 1,5 Go |
| Remarques | Évitez de répéter des caractères plus de 4 fois, par exemple « aaaaa »|
| Remarques | aucun caractère spécial tel que « \t » ou tout autre caractère UTF-8 au-dessus de U+00A1 dans le [tableau de caractères Unicode](http://www.utf8-chartable.de/)|
| Remarques | les URI seront également rejetées, car il est impossible de prononcer une URI|

Lorsque le texte est importé, il est normalisé pour pouvoir être traité par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données. Consultez les [Instructions concernant la transcription](prepare-transcription.md) pour déterminer la langue appropriée lors de la préparation de vos données de langage.

## <a name="language-support"></a>Support multilingue

Les langues prises en charge pour les modèles de langage de **reconnaissance vocale** personnalisés sont répertoriées ci-après.

Cliquez pour obtenir une liste complète des [langues prises en charge](supported-languages.md)

## <a name="import-the-language-data-set"></a>Importer le jeu de données de langage

Cliquez sur le bouton « Import » (Importer) dans la ligne « Language Datasets » (Jeux de données de langage) ; le site affiche une page de chargement d’un nouveau jeu de données.

Quand vous êtes prêt à importer votre jeu de données de langage, connectez-vous au [portail du service Speech](https://customspeech.ai).  Cliquez ensuite sur le menu déroulant « Custom Speech » dans le ruban supérieur, puis sélectionnez « Adaptation Data » (Données d’adaptation). La première fois que vous tentez de charger des données dans le service Speech, vous voyez un tableau vide nommé « Datasets » (Jeux de données).

Pour importer un nouveau jeu de données, cliquez sur le bouton « Importer » dans la ligne « Language Datasets » (Jeux de données de langage), le site affiche une page de chargement d’un nouveau jeu de données. Entrez un nom et une description pour vous aider à identifier le jeu de données ultérieurement, et choisissez les paramètres régionaux. Utilisez ensuite le bouton « Choose File » (Choisir un fichier) pour rechercher le fichier texte de données de langage. Puis cliquez sur « Import » (Importer), le jeu de données est chargé. Selon la taille du jeu de données, l’importation peut durer plusieurs minutes.

![Essayer](media/stt/speech-language-datasets-import.png)

Lorsque l’importation est terminée, vous revenez à la table de données de langage et vous verrez une entrée qui correspond à votre jeu de données de langage. Notez qu’un ID unique (GUID) lui a été attribué. Les données contiennent également un état qui reflète leur état actuel. L’état sera « En attente » lorsqu’elles sont en file d’attente de traitement, « Traitement en cours » pendant la phase de validation et « Terminé » lorsque les données sont prêtes à être utilisées. La validation des données procède à une série de vérifications sur le texte du fichier et à une normalisation du texte des données.

Lorsque l’état est « Terminé », vous pouvez cliquer sur « Afficher le rapport » pour afficher le rapport de vérification des données de langage. Le nombre d’énoncés dont la vérification a réussi ou échoué s’affiche, ainsi que des détails sur les énoncés ayant échoué. Dans l’exemple ci-dessous, la vérification a échoué pour deux exemples en raison de caractères incorrects (dans ce jeu de données, la première ligne avait deux caractères de tabulation, la deuxième contenait deux caractères en dehors du jeu de caractères imprimables ASCII et la troisième était vide).

![Essayer](media/stt/speech-language-datasets-report.png)

Lorsque l’état du jeu de données de langage est « Terminé », il peut être utilisé pour créer un modèle de langage personnalisé.

![Essayer](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Créer un modèle linguistique personnalisé

Lorsque vos données de langage sont prêtes, cliquez sur « Language Models » (Modèles de langage) dans le menu déroulant « Menu » pour démarrer le processus de création de modèle de langage personnalisé. Cette page contient une table appelée « Language Models » (Modèles de langage) avec vos modèles de langage personnalisés actuels. Si vous n’avez pas encore créé de modèles de langage personnalisés, le tableau est vide. Les paramètres régionaux actuels sont indiqués dans le tableau en regard de l’entrée des données concernées.

Les paramètres régionaux appropriés doivent être sélectionnés avant d’entreprendre une action. Les paramètres régionaux actuels sont indiqués dans le titre du tableau sur toutes les pages de données, de modèles et de déploiements. Pour changer les paramètres régionaux, cliquez sur le bouton « Change Locale » (Changer les paramètres régionaux) situé sous le titre du tableau. Vous êtes ensuite dirigé vers une page de confirmation des paramètres régionaux. Cliquez sur « OK » pour revenir au tableau.

Dans la page « Create Language Model» (Créer un modèle de langage), entrez un « Nom » et une « Description » pour vous aider à suivre les informations utiles sur ce modèle comme le jeu de données utilisé. Sélectionnez ensuite « Base Language Model » (Modèle de langage de base) dans le menu déroulant. Ce modèle sera le point de départ de votre personnalisation. Deux modèles de langage de base existants peuvent être sélectionnés. Les modèles Search and Dictation (Recherche et Dictée) sont appropriés pour la voix dans une application, comme des commandes, des requêtes de recherche ou la dictée. Le modèle oral est approprié pour la reconnaissance vocale de paroles prononcées dans un style oral. Ce type de voix s’adresse généralement à une autre personne et se retrouve dans les centres d’appel ou les réunions. Un nouveau modèle appelé « Universal » (Universel) est également disponible publiquement. Le modèle Universal a pour but de convenir à tout scénario et de remplacer au final les modèles Search and Dictation (Recherche et Dictée) et Conversational (Conversationnel).

5.  Dans l’exemple ci-dessous, une fois que vous avez spécifié le modèle de langage de base, sélectionnez le jeu de données de langage à utiliser pour la personnalisation avec le menu déroulant « Language Data » (Données de langage)

![Essayer](media/stt/speech-language-models-create2.png)

Comme avec la création d’un modèle acoustique, vous pouvez choisir de tester votre nouveau modèle en mode hors connexion lorsque le traitement est terminé. Les évaluations des modèles nécessitent un jeu de données acoustiques.

Pour tester votre modèle de langage en mode hors connexion, cochez la case en regard de « Offline Testing » (Tests hors ligne). Sélectionnez ensuite un modèle acoustique dans le menu déroulant. Si vous n’avez créé de modèle acoustique personnalisé, le modèle acoustique de base de Microsoft sera le seul modèle dans le menu. Si vous avez sélectionné un modèle de base LM conversationnel, vous devez utiliser un modèle AM conversationnel ici. Si vous utilisez une recherche et dictez le modèle LM, vous devez sélectionner une recherche et un modèle AM de dictée.

Enfin, sélectionnez le jeu de données acoustiques que vous souhaitez utiliser pour l’évaluation.

Quand vous êtes prêt à démarrer le traitement, cliquez sur « Create » (Créer), qui vous permettra d’accéder au tableau des modèles de langage. Une nouvelle entrée correspondant à ce modèle apparaitra dans la table. L’état reflète l’état du modèle et passe par plusieurs états, notamment « En attente », « Traitement en cours » et « Terminé ».

Lorsque le modèle atteint l’état « Terminé », il peut être déployé sur un point de terminaison. Cliquez sur « View Result » (Afficher le résultat) pour afficher les résultats du test hors connexion, s’il est effectué.

Pour modifier à tout moment le « nom » ou la « description » du modèle, vous pouvez utiliser le lien « Modifier » de la ligne concernée dans la table de modèles de langage.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
- [Exemples de données Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
