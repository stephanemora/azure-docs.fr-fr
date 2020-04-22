---
title: 'Tutoriel : Intégration à Power Virtual Agents – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, améliorez la qualité de votre base de connaissances avec l’apprentissage actif. Examinez, acceptez ou refusez, ou ajoutez sans supprimer ou changer les questions existantes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402809"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Tutoriel : Ajouter votre base de connaissances à Power Virtual Agents
Créez et étendez un bot [Power Virtual Agents](https://powervirtualagents.microsoft.com/) pour fournir des réponses à partir de votre base de connaissances.

Dans ce tutoriel, vous allez apprendre à :

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un bot Power Virtual Agents
> * Créer une rubrique de secours du système
> * Ajouter QnA Maker comme action à une rubrique sous forme de flux Power Automate
> * Créer une solution Power Automate
> * Ajouter un flux Power Automate à votre solution
> * Publier les agents Power Virtual Agents
> * Tester les agents Power Virtual Agents et recevoir une réponse de votre base de connaissances QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Intégrer un agent à une base de connaissances

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) permet aux équipes de créer des bots puissants à l’aide d’une interface graphique guidée sans code. Vous n’avez pas besoin de développeurs ni de scientifiques des données.

Dans Power Virtual Agents, vous créez un agent avec une série de rubriques (domaines) de façon à répondre à des questions d’utilisateurs en effectuant des actions. En l’absence de réponse, une rubrique de secours du système peut en retourner une.

Configurez l’agent de telle sorte qu’il envoie la question à votre base de connaissances dans une action de rubrique ou dans le chemin de rubrique de *secours du système*. Les deux utilisent une action pour se connecter à votre base de connaissances et retourner une réponse.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate se connecte à l’action `GenerateAnswer`

Pour connecter votre agent à votre base de connaissances, utilisez Power Automate pour créer l’action. Power Automate fournit un flux de processus, qui se connecte à l’API `GenerateAnswer` de QnA Maker.

Une fois le flux conçu et enregistré, il est accessible à partir d’une solution Power Automate. Utilisez cette solution en tant qu’action dans votre agent.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Connecter un agent à votre base de connaissances

Voici une vue d’ensemble des étapes permettant de connecter un agent dans Power Virtual Agents à une base de connaissances dans QnA Maker.

* Sur le portail [QnA Maker](https://www.qnamaker.ai/) :
    * Créez et publiez votre base de connaissances.
    * Copiez les détails de votre base de connaissances, notamment l’ID, la clé du point de terminaison du runtime et l’hôte du point de terminaison du runtime.
* Sur le portail [Power Virtual Agents](https://powerva.microsoft.com/) :
    * Créez une rubrique d’agent.
    * Appelez une action (du flux Power Automate).
* Sur le portail [Power Automate](https://us.flow.microsoft.com/) :
    * Créez un flux avec un connecteur à [GenerateAnswer de QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Informations sur la base de connaissances publiée QnA Maker :
            * ID de base de connaissances
            * Hôte du point de terminaison de ressource QnA Maker
            * Clé du point de terminaison de ressource QnA Maker
        * Entrée – Requête utilisateur
        * Sortie – Réponse de la base de connaissances
    * Créez une solution et ajoutez le flux.
* Revenez à Power Virtual Agents :
    * sélectionnez la sortie de la solution sous forme de message pour une rubrique.

## <a name="create-and-publish-a-knowledge-base"></a>Créer et publier une base de connaissances

1. Suivez le guide de [démarrage rapide](../Quickstarts/create-publish-knowledge-base.md) pour créer une base de connaissances. N’effectuez pas la dernière section relative à la création d’un bot. Utilisez plutôt ce tutoriel pour créer un bot avec Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran des paramètres de la base de connaissances publiée](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Entrez les paramètres de la base de données que vous avez publiée et qui se trouvent dans la page **Settings** (Paramètres) du portail [QnA Maker](https://www.qnamaker.ai/). Vous aurez besoin de ces information à l’[étape Power Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) pour configurer votre connexion à QnA Maker `GenerateAnswer`.

1. Sur le portail QnA Maker, dans la page **Settings** (Paramètres), recherchez la clé du point de terminaison, l’hôte du point de terminaison et l’ID de la base de connaissances.

## <a name="create-an-agent-in-power-virtual-agents"></a>Créer un agent dans Power Virtual Agents

1. [Connectez-vous à Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Utilisez votre compte de messagerie scolaire ou professionnel.
1. S’il s’agit de votre premier bot, vous accédez à la page **Home** (Accueil) de l’agent. Si ce n’est pas votre premier bot, sélectionnez le bot dans la zone supérieure droite de la page, puis sélectionnez **+ New Bot** (Nouveau bot).

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page d’accueil de Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Entrez les paramètres de la base de données que vous avez publiée et qui se trouvent dans la page **Settings** (Paramètres) du portail [QnA Maker](https://www.qnamaker.ai/).

## <a name="topics-provided-in-the-bot"></a>Rubriques fournies dans le bot

L’agent de sert de la collection de rubriques pour répondre aux questions relevant de votre domaine. Dans ce tutoriel, l’agent propose de nombreuses rubriques, réparties en rubriques utilisateur et rubriques système.

> [!div class="mx-imgBorder"]
> ![Capture d’écran des rubriques fournies dans l’agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Créer la rubrique de secours du système

Bien que l’agent puisse se connecter à votre base de connaissances à partir de n’importe quelle rubrique, ce tutoriel utilise la rubrique *system fallback* (secours du système). La rubrique de secours est utilisée quand l’agent ne trouve pas de réponse. L’agent transmet le texte de l’utilisateur à l’API `GenerateAnswer` de QnA Maker, reçoit la réponse de votre base de connaissances et la présente à l’utilisateur sous forme de message.

1. Sur le portail [Power Virtual Agents](https://powerva.microsoft.com/#/), en haut à droite, sélectionnez **Settings** (Paramètres) (icône d’engrenage). Ensuite, sélectionnez **System Fallback** (Secours du système).

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’élément de menu Power Virtual Agents pour la rubrique de secours du système](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Sélectionnez **+ Add** (Ajouter) pour ajouter une rubrique de secours du système.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’ajout d’une rubrique de secours.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Une fois la rubrique ajoutée, sélectionnez **Go to Fallback topic** (Accéder à la rubrique de secours) pour créer la rubrique de secours sur le canevas de création.

    > [!TIP]
    > Si vous avez besoin de retourner à la rubrique de secours, celle-ci se trouve dans la section **Topics** (Rubriques), dans la rubrique **System** (Système).

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Utiliser le canevas de création pour ajouter une action

Utilisez le canevas de création Power Virtual Agents pour connecter la rubrique de secours à votre base de connaissances. La rubrique commence par le texte utilisateur non reconnu. Ajoutez une action qui transmet ce texte à QnA Maker, puis qui affiche la réponse sous forme de message. La dernière étape d’affichage de la réponse est traitée dans une [étape distincte](#add-your-solutions-flow-to-power-virtual-agents), plus loin dans ce tutoriel.

Cette section vise à créer le flux de conversation de la rubrique de secours.

1. La nouvelle action de secours peut déjà comporter des éléments de flux de conversation. Supprimez l’élément **Escalate** (Réaffecter) en sélectionnant le menu **Options**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la rubrique de secours de Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Sélectionnez le connecteur **+** en dessous de la zone **Message**, puis sélectionnez **Call an action** (Appeler une action).

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option d’appel d’une action](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Sélectionnez **Create a flow** (Créer un flux). Le processus vous amène au portail Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option de création d’un flux](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Créer un flux Power Automate à connecter à votre base de connaissances

La procédure suivante crée un flux Power Automate qui effectue les opérations suivantes :
* Il intercepte le texte utilisateur entrant et l’envoie à QnA Maker.
* Il affecte la meilleure réponse de QnA Maker à une variable, puis envoie en retour la variable (meilleure réponse) à votre agent.

1. Dans **Power Automate**, le modèle de flux (**Flow Template**) démarre automatiquement. Dans l’élément de flux **Power Virtual Agents**, sélectionnez **Edit** (Modifier) pour configurer la variable d’entrée de l’agent vers votre base de connaissances. La variable d’entrée textuelle est la question soumise sous forme de texte par l’utilisateur depuis l’agent.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option Power Automate permettant de configurer votre variable d’entrée en tant que chaîne de texte](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Ajoutez une entrée textuelle et nommez la variable `InputText`, avec `IncomingUserQuestion` en guise de description. Ce nom permet de distinguer le texte d’entrée du texte de sortie que vous créerez par la suite.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option Power Automate permettant de configurer le nom et la description de la variable d’entrée](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Sélectionnez le connecteur **+** en dessous de la zone **Power Virtual Agents** pour insérer une nouvelle étape dans le flux (avant **Return value(s) to Power Virtual Agent**). Ensuite, sélectionnez **Add an action** (Ajouter une action).

1. Effectuez une recherche sur `Qna` pour trouver les actions **QnA Maker**, puis sélectionnez **Generate answer** (Générer une réponse).

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option permettant de générer une réponse](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Les paramètres de connexion obligatoires pour QnA Maker apparaissent dans les paramètres d’action et de question de l’agent.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran des paramètres de connexion obligatoires](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configurez l’action avec l’ID de votre base de connaissances, l’hôte du point de terminaison et la clé du point de terminaison. Ces valeurs se trouvent dans la page **Settings** (Paramètres) de votre base de données, sur le portail QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran des paramètres de la base de connaissances publiée](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Pour configurer la **Question**, cochez la case, puis sélectionnez le texte d’entrée (`InputText`) dans la liste.

1. Pour insérer une nouvelle étape dans le flux, sélectionnez le connecteur **+** en dessous de la zone de l’action **Generate answer** (Générer une réponse). Ensuite, sélectionnez **Add an action** (Ajouter une action).

1. Pour ajouter une variable destinée à intercepter le texte de réponse retourné par `GenerateAnswer`, recherchez l’action `Initialize variable` et sélectionnez-la.

    Nommez la variable `OutgoingQnAAnswer`, puis sélectionnez le type **String** (Chaîne). Ne définissez pas **Value** (Valeur).

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’initialisation de la variable de sortie](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Pour insérer une nouvelle étape dans le flux, sélectionnez le connecteur **+** en dessous de la zone de l’action **Initialize variable** (Initialiser la variable). Ensuite, sélectionnez **Add an action** (Ajouter une action).

1. Pour définir l’ensemble de la réponse de la base de connaissances sur la variable, recherchez l’action `Apply to each` et sélectionnez-la. Sélectionnez `GenerateAnswer` `answers`.

1. Pour retourner uniquement la meilleure réponse, dans la même zone **Apply to each** (Appliquer à tout), sélectionnez **Add an action** (Ajouter une action). Recherchez et sélectionnez **Set variable** (Définir la variable).

    Dans la zone **Set variable** (Définir la variable), sélectionnez la zone de texte **Name** (Nom), puis sélectionnez **OutgoingQnAAnswer** dans la liste.

    Sélectionnez la zone de texte **Value** (Valeur), puis sélectionnez **Answers Answer** dans la liste.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la définition du nom et de la valeur de la variable](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Pour retourner la variable (et sa valeur), sélectionnez l’élément de flux **Return value(s) to Power Virtual Agent** (Retourner les valeurs à l’agent Power Virtual Agent). Ensuite, sélectionnez **Edit** (Modifier) > **Add an output** (Ajouter une sortie). Sélectionnez le type de sortie **Text** (Texte), puis entrez le titre (**Title**) de `FinalAnswer`. Sélectionnez la zone de texte **Value** (Valeur), puis sélectionnez la variable `OutgoingQnAAnswer`.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la définition de la valeur de retour](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Sélectionnez **Enregistrer** pour enregistrer le flux.

## <a name="create-a-solution-and-add-the-flow"></a>Créer une solution et ajouter le flux

Pour que l’agent puisse trouver le flux et s’y connecter, il convient d’inclure le flux dans une solution Power Automate.

1. Toujours sur le portail Power Automate, sélectionnez **Solutions** dans le volet de navigation à gauche.

1. Sélectionnez **+ New solution** (Nouvelle solution).

1. Entrez un nom d’affichage. La liste des solutions comprend chacune des solutions de votre organisation ou école. Choisissez une convention de nommage qui vous aide à filtrer uniquement vos solutions. Par exemple, vous pouvez faire précéder votre nom de solution de votre adresse e-mail : `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Sélectionnez votre serveur de publication dans le liste de choix.

1. Acceptez les valeurs par défaut pour le nom et la version.

1. Sélectionnez **Create** (Créer) pour terminer le processus.

## <a name="add-your-flow-to-the-solution"></a>Ajouter votre flux à la solution

1. Dans la liste des solutions, sélectionnez la solution que vous venez de créer. Elle doit figurer en haut de la liste. Si ce n’est pas le cas, effectuez une recherche sur votre adresse e-mail, qui est comprise dans le nom de la solution.

1. Dans la solution, sélectionnez **+ Add existing** (Ajouter existant), puis sélectionnez **Flow** (Flux) dans la liste.

1. Recherchez votre flux, puis sélectionnez **Add** (Ajouter) pour terminer le processus. Si les flux sont nombreux, examinez la colonne **Modified** (Modifié) pour trouver le flux le plus récent.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Ajouter le flux de votre solution à Power Virtual Agents

1. Revenez à l’onglet du navigateur où figure votre agent dans Power Virtual Agents. Le canevas de création doit encore être ouvert.

1. Pour insérer une nouvelle étape dans le flux, sous la zone d’action **Message**, sélectionnez le connecteur **+** . Ensuite, sélectionnez **Call an action** (Appeler une action).

1. Dans la nouvelle action, sélectionnez la valeur d’entrée de **UnrecognizedTriggerPhrase**. Le texte passe alors de l’agent au flux.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option Power Virtual Agents permettant de sélectionner une expression déclenchante non reconnue](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Pour insérer une nouvelle étape dans le flux, sous la zone **Action**, sélectionnez le connecteur **+** . Ensuite, sélectionnez **Show a message** (Afficher un message).

1. Entrez le texte du message, `Your answer is:`. Sélectionnez `FinalAnswer` en tant que variable contextuelle en utilisant la fonction de la barre d’outils sur place.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’option Power Virtual Agents permettant d’entrer le texte du message](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Dans la barre d’outils contextuelle, sélectionnez **Save** (Enregistrer) pour enregistrer les détails du canevas de création pour la rubrique.

Voici à quoi ressemble le canevas final de l’agent.

> [!div class="mx-imgBorder"]
> ![Capture d’écran du canevas final de l’agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Tester l’agent

1. Dans le volet de test, activez **Track between topics** (Suivi entre les rubriques). Vous pouvez ainsi examiner la progression entre les rubriques, mais aussi dans une même rubrique.

1. Testez l’agent en entrant le texte utilisateur dans l’ordre suivant. Le canevas de création indique la bonne exécution des étapes par une coche verte.

    |Ordre des questions|Questions de test|Objectif|
    |--|--|--|
    |1|Hello|Commencer la conversation|
    |2|Store hours (Horaires du magasin)|Exemple de rubrique. Elle est configurée automatiquement sans aucune action supplémentaire de votre part.|
    |3|Oui|En réponse à `Did that answer your question?`|
    |4|Excellent|En réponse à `Please rate your experience.`|
    |5|Oui|En réponse à `Can I help with anything else?`|
    |6|Qu’est-ce qu’une base de connaissances ?|Cette question déclenche une action de secours, qui envoie le texte à votre base de connaissances pour obtenir une réponse. La réponse est ensuite affichée. |

> [!div class="mx-imgBorder"]
> ![Capture d’écran du canevas final de l’agent](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publier votre bot

Pour rendre l’agent accessible à tous les membres de votre école ou organisation, vous devez le publier.

1. Dans le volet de navigation de gauche, sélectionnez **Publish** (Publier). Sélectionnez ensuite **Publish** dans la page.

1. Essayez votre bot sur le site web de démonstration (recherchez le lien sous **Publish**).

    Une nouvelle page web s’ouvre avec votre bot. Posez la même question de test au bot : `What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du canevas final de l’agent](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Partager votre bot

Pour partager le site web de démonstration, configurez-le en tant que canal.

1. Dans le volet de navigation de gauche, sélectionnez **Manage** (Gérer)  > **Channels** (Canaux).

1. Sélectionnez **Demo website** (Site web de démonstration) dans la liste des canaux.

1. Copiez le lien, puis sélectionnez **Save** (Enregistrer). Collez le lien du site web de démonstration dans un e-mail destiné aux membres de votre école ou organisation.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous en avez fini avec la base de connaissances, supprimez les ressources QnA Maker sur le portail Azure.

## <a name="next-step"></a>Étape suivante

[Obtenir une analyse de votre base de connaissances](../How-To/get-analytics-knowledge-base.md)

Pour en savoir plus :
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Connecteur QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) et [paramètres du connecteur](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)