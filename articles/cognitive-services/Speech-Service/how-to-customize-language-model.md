---
title: 'Tutoriel : Guide pratique pour créer un modèle de langage avec le service de reconnaissance vocale'
titlesuffix: Azure Cognitive Services
description: Découvrez comment créer un modèle de langage avec Service Speech. Utilisez ce modèle de langage personnalisé avec des modèles vocaux existants de Microsoft pour ajouter l’interaction vocale à votre application.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 8276b86df2dc1bc90fc07da262aa0979f7562619
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548487"
---
# <a name="tutorial-create-a-custom-language-model"></a>Tutoriel : Créer un modèle linguistique personnalisé

Dans ce document, vous créez un modèle de langage personnalisé. Vous pouvez ensuite utiliser ce modèle de langage personnalisé avec des modèles vocaux existants de Microsoft pour ajouter l’interaction vocale à votre application.

Le document explique comment :
> [!div class="checklist"]
> * Préparer les données
> * Importer le jeu de données de langage
> * Créer le modèle de langage personnalisé

Si vous n’avez pas de compte Cognitive Services, créez un [compte gratuit](https://azure.microsoft.com/try/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour vérifier la connexion de votre compte Cognitive Services à un abonnement, ouvrez la page [Cognitive Services Subscriptions](https://customspeech.ai/Subscriptions) (Abonnements Cognitive Services).

Pour vous connecter à un abonnement Speech Services créé dans le portail Azure, sélectionnez le bouton **Connect existing subscription** (Se connecter à un abonnement existant).

Pour plus d’informations sur la création d’un abonnement à Speech Services dans le portail Azure, consultez la page [Bien démarrer](get-started.md).

## <a name="prepare-the-data"></a>Préparer les données

Pour créer un modèle de langage personnalisé destiné à votre application, vous devez fournir au système une liste d’exemples d’énoncés, par exemple :

*   « Le patient a eu de l’urticaire la semaine dernière. »
*   « Le patient avait une hernioplastie bien cicatrisée.»

Il n’est pas nécessaire que les phrases soient complètes ou grammaticalement correctes, par contre elles doivent refléter avec exactitude l’entrée vocale que le système est susceptible de rencontrer dans le déploiement. Ces exemples doivent reproduire le style et le contenu de la tâche que les utilisateurs vont effectuer avec votre application.

Les données du modèle de langage doivent être écrites au format UTF-8 BOM. Le fichier texte doit contenir un exemple (phrase, énoncé ou requête) par ligne.

Si vous voulez que certains termes aient plus de poids (plus d’importance), vous pouvez ajouter plusieurs énoncés qui incluent ces termes, à vos données.

Les principales conditions requises pour les données de langage sont résumées dans le tableau suivant.

| Propriété | Valeur |
|----------|-------|
| Encodage de texte | UTF-8 BOM|
| Nb d’énoncés par ligne | 1 |
| Taille maximale du fichier | 1,5 Go |
| Remarques | Évitez de répéter des caractères plus de 4 fois, par exemple « aaaaa »|
| Remarques | Aucun caractère spécial tel que « \t » ou tout autre caractère UTF-8 au-dessus de U+00A1 dans le [tableau de caractères Unicode](https://www.utf8-chartable.de/)|
| Remarques | les URI seront également rejetées, car il est impossible de prononcer une URI|

Lorsque le texte est importé, il est normalisé pour pouvoir être traité par le système. Une normalisation importante doit toutefois être effectuée par l’utilisateur _avant_ de charger les données. Consultez les [instructions concernant la transcription](prepare-transcription.md) pour déterminer la langue appropriée à utiliser lors de la préparation de vos données de langage.

## <a name="language-support"></a>Support multilingue

Consultez la liste complète des [langues prises en charge](language-support.md#text-to-speech) dans les modèles de langage de **reconnaissance vocale** personnalisés.



## <a name="import-the-language-data-set"></a>Importer le jeu de données de langage

Sélectionnez le bouton **Import** (Importer) sur la ligne **Language Datasets** (Jeux de données de langage) ; le site affiche la page de chargement d’un nouveau jeu de données.

Quand vous êtes prêt à importer votre jeu de données de langage, connectez-vous au [portail Speech Services](https://customspeech.ai). En premier lieu, sélectionnez le menu déroulant **Custom Speech** dans le ruban supérieur. Sélectionnez ensuite **Adaptation Data** (Données d’adaptation). La première fois que vous tentez de charger des données dans Speech Services, vous voyez un tableau vide nommé **Datasets** (Jeux de données).

Pour importer un nouveau jeu de données, sélectionnez le bouton **Import** (Importer) situé sur la ligne **Language Datasets** (Jeux de données de langage). Le site affiche la page de chargement d’un nouveau jeu de données. Entrez un **Name** (Nom) et une **Description** pour vous aider ultérieurement à identifier le jeu de données, puis choisissez les paramètres régionaux.

Utilisez ensuite le bouton **Choose File** (Choisir un fichier) pour rechercher le fichier texte des données de langage. Sélectionnez **Import** (Importer) pour charger ensuite le jeu de données. Selon la taille du jeu de données, l’importation peut durer plusieurs minutes.

![Essai](media/stt/speech-language-datasets-import.png)

Lorsque l’importation est terminée, les entrées des données de langage correspondent à votre jeu de données de langage. Notez qu’un ID unique (GUID) lui a été affecté. Les données présentent également un statut qui reflète leur état actuel. L’état est **Waiting** (En attente) lorsqu’elles sont en file d’attente de traitement, **Processing** (Traitement en cours) pendant la phase de validation et **Complete** (Terminé) lorsque les données sont prêtes à être utilisées. La validation des données procède à une série de vérifications sur le texte du fichier. Elle effectue également une normalisation du texte des données.

Lorsque l’état est **Complete** (Terminé), vous pouvez sélectionner **View Report** (Afficher le rapport) pour consulter le rapport de vérification des données de langage. Le nombre d’énoncés dont la vérification a réussi ou échoué s’affiche, ainsi que des détails sur les énoncés ayant échoué. Dans l’exemple suivant, deux vérifications ont échoué en raison de caractères incorrects. (Dans ce jeu de données, la première ligne contenait deux caractères de tabulation, la deuxième ligne comportait plusieurs caractères ne faisant pas partie du jeu de caractères imprimables ASCII et la troisième ligne était vide).

![Essai](media/stt/speech-language-datasets-report.png)

Lorsque son état est **Terminé**, le jeu de données de langage peut être utilisé pour créer un modèle de langage personnalisé.

![Essai](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Créer un modèle linguistique personnalisé

Lorsque vos données de langage sont prêtes, sélectionnez **Language Models** (Modèles de langage) dans le menu déroulant **Menu** pour démarrer le processus de création d’un modèle de langage personnalisé. Cette page contient un tableau nommé **Language Models** (Modèles de langage) qui répertorie vos modèles de langage personnalisés actuels. Si vous n’avez pas déjà créé de modèles de langage personnalisés, ce tableau est vide. Les paramètres régionaux actuels sont indiqués dans le tableau en regard de l’entrée des données concernées.

Les paramètres régionaux appropriés doivent être sélectionnés avant d’entreprendre une action. Les paramètres régionaux actuels sont indiqués dans le titre du tableau sur toutes les pages de données, de modèles et de déploiements. Pour modifier les paramètres régionaux, sélectionnez le bouton **Change Locale** (Changer les paramètres régionaux) situé sous le titre du tableau.  Vous êtes dirigé vers une page de confirmation des paramètres régionaux. Sélectionnez **OK** pour revenir au tableau.

Dans la page Create Language Model (Créer un modèle de langage), entrez un **Name** (Nom) et une **Description** pour pouvoir effectuer un suivi sur les informations utiles de ce modèle, telles que le jeu de données qui est utilisé. Sélectionnez ensuite **Base Language Model** (Modèle de langage de base) dans le menu déroulant. Ce modèle est le point de départ de votre personnalisation.

Deux modèles de langage de base existants peuvent être sélectionnés. Le modèle Search and Dictation (Recherche et dictée) est approprié pour la reconnaissance vocale qui est destinée à une application, comme les commandes, les requêtes de recherche ou la dictée. Le modèle Conversational (Conversation) est adapté à la reconnaissance vocale d’énoncés de type conversationnel. Ce type de voix s’adresse généralement à une autre personne et se retrouve dans les centres d’appel ou les réunions. Un nouveau modèle appelé Universal (Universel) est également disponible publiquement. Le modèle Universal a pour but de convenir à tous les scénarios, et de remplacer au final les modèles Search and Dictation (Recherche et dictée) et Conversational (Conversation).

Comme illustré dans l’exemple suivant, après avoir spécifié le modèle de langage de base, servez-vous du menu déroulant **Language Data** (Données de langage) pour sélectionner le jeu de données de langage à utiliser pour la personnalisation.

![Essai](media/stt/speech-language-models-create2.png)

À l’instar de la création d’un modèle acoustique, vous pouvez choisir de tester votre nouveau modèle en mode hors connexion lorsque le traitement est terminé. Les évaluations des modèles nécessitent un jeu de données acoustiques.

Pour tester votre modèle de langage en mode hors connexion, cochez la case en regard de **Offline Testing** (Tests hors ligne). Sélectionnez ensuite un modèle acoustique dans le menu déroulant. Si vous n’avez pas créé de modèle acoustique personnalisé, les modèles acoustiques de base de Microsoft sont les seuls modèles affichés dans le menu. Si vous avez sélectionné un modèle de base LM (Language Model) conversationnel, vous devez utiliser un modèle AM (Acoustic Model) conversationnel ici. Si vous utilisez un modèle LM de recherche et dictée, vous devez sélectionner un modèle AM de recherche et dictée.

Pour terminer, sélectionnez le jeu de données acoustiques à utiliser pour l’évaluation.

Lorsque vous êtes prêt à lancer le traitement, sélectionnez**Create** (Créer). Le tableau des modèles de langage s’affiche. Une nouvelle entrée correspondant à ce modèle apparaitra dans la table. Le statut reflète l’état du modèle qui passe par différents stades, notamment **Waiting** (En attente), **Processing** (Traitement en cours) et **Complete** (Terminé).

Lorsque le modèle atteint l’état **Complete** (Terminé), il peut être déployé sur un point de terminaison. La sélection de **View Result** (Afficher le résultat) montre les résultats des tests hors connexion, si vous les avez effectués.

Pour modifier à tout moment le **Name** (Nom) ou la **Description** du modèle, vous pouvez utiliser le lien **Edit** (Modifier) de la ligne concernée dans le tableau des modèles de langage.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit à Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
- [Exemples de données Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
