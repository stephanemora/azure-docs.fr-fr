---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a8af7d5c76552ade3ffd5d50b308cee6b0b1446c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096998"
---
## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services).

## <a name="sign-in-to-language-studio"></a>Connexion à Language Studio

1. Accédez à [Language Studio](https://aka.ms/languageStudio) et connectez-vous avec votre compte Azure. 

2. Dans la fenêtre **Choose a language resource (Choisir une ressource de langue)** qui s’affiche, recherchez votre abonnement Azure, puis choisissez votre ressource de langue. Si vous n’avez pas de ressource, vous pouvez en créer une nouvelle.

    > [!NOTE]
    > Actuellement, seules les ressources avec le niveau tarifaire standard (**S**) peuvent être utilisées avec le service Conversational Language Understanding (compréhension du langage courant).
    
    :::image type="content" source="../../media/quickstart-language-resource.png" alt-text="Capture d’écran montrant l’écran de sélection de ressource dans Language Studio." lightbox="../../media/quickstart-language-resource.png":::

## <a name="create-a-conversation-project"></a>Création d’un projet de conversation

Une fois que vous disposez d’une ressource de langue associée à votre compte, créez un projet Conversational Language Understanding. Dans ce guide de démarrage rapide, vous allez créer un projet capable d’identifier les commandes en lien avec les e-mails, telles que la lecture des e-mails par certaines personnes, la suppression d’e-mails et l’attachement d’un document à un e-mail.

1. Dans [Language Studio](https://aka.ms/languageStudio), recherchez la section **Understand conversational language (Comprendre le langage courant)** et cliquez sur **Understand conversational language (Compréhension du langage courant)**. Cela vous permet d’afficher la page **Conversations project (Projet de conversations)**. 
 
    
    :::image type="content" source="../../media/projects-page.png" alt-text="Capture d’écran montrant la page de projet de conversation dans Language Studio." lightbox="../../media/projects-page.png":::

2. Cliquez sur **Create new project (Créer un projet)**. Sélectionnez **Conversation project (Projet de conversation)**, puis cliquez sur **Suivant**.

Vous devez ensuite fournir les informations suivantes :

|Value  | Description  |
|---------|---------|
|Nom     | Nom de votre projet        |
|Description    | Description facultative du projet.        |
|Langue principale du texte     | Langue principale de votre projet. Vos données de formation doivent être principalement dans cette langue. Pour ce guide de démarrage rapide, choisissez **Anglais**.        |
|Activer plusieurs langages     |  Si vous souhaitez autoriser votre projet à prendre en charge plusieurs langues à la fois. Pour ce guide de démarrage rapide, activez cette option.       |

Une fois que vous avez terminé, cliquez sur **Suivant**, vérifiez les informations, puis cliquez sur **create project (Créer le projet)** pour terminer le processus. Vous devez maintenant voir l’écran **Build Schema (Générer le schéma)** dans votre projet.

## <a name="build-schema"></a>Générer le schéma

1. Sélectionnez l’onglet Intents (Intentions) ou Entities (Entités) dans la page **Build Schema (Générer le schéma)**, puis cliquez sur **Ajouter**. Vous êtes invité à entrer un nom avant de terminer la création de l’intention ou de l’entité.

2. Créez trois intentions avec les noms suivants :
    - **Lire**
    - **Supprimer**
    - **Attacher**

3. Créez trois entités avec les noms suivants :
    - **Expéditeur**
    - **FileName**
    - **FileType**


Cliquez sur l’intention pour atteindre la page des [énoncés de balises](../../how-to/tag-utterances.md), dans laquelle vous pouvez ajouter des exemples pour les intentions et les étiqueter avec des entités.


:::image type="content" source="../../media/quickstart-intents.png" alt-text="Capture d’écran montrant la page de schéma dans Language Studio." lightbox="../../media/quickstart-intents.png":::

## <a name="tag-utterances"></a>Étiqueter les énoncés

Dans la page des énoncés de balises, nous allons ajouter quelques exemples aux intentions. Sélectionnez l’intention de **lecture** (Read) dans la zone de liste déroulante qui indique **Select Intent (Sélectionner l’intention)**.

Dans la zone de texte qui indique **Write your example utterance and press enter (Écrivez votre exemple d’énoncé et appuyez sur entrée)**, écrivez la phrase « *read the email from Carol (lire l’e-mail de Carole)*  » et appuyez sur Entrée pour l’ajouter comme exemple d’énoncé.

Faites glisser le curseur sur le mot « *Carol* » et sélectionnez l’entité **Sender** (Expéditeur) pour étiqueter « *Carol* » en tant qu’entité.

Ajoutez le reste de ces énoncés avec les intentions et les entités suivantes.

|Énoncé|Intentionnel|Entités|
|--|--|--|
|*Read John's email for me (Lire l’e-mail de John pour moi)*|**Lire**|"John": **Sender**|
|*What did the email from Matt say (Que dit le message de Matt)*|**Lire**|"Matt": **Sender**|
|*Delete my last email from Martha (Supprimer mon dernier e-mail de Martha)*|**Supprimer**|"Martha": **Sender**|
|*Delete this (Supprimer ceci)*|**Supprimer**|_No entities (Aucune entité)_|
|*Remove this one (Supprimer celui-ci)*|**Supprimer**|_No entities (Aucune entité)_|
|*Move this to the deleted folder (Déplacer ceci vers le dossier supprimé)*|**Supprimer**|_No entities (Aucune entité)_|
|*Attach the excel file called reports q1 (Joindre le fichier Excel nommé Reports Q1)*|**Attacher**|"excel": **FileType**, <br> "reports q1" -> **FileName**|
|*Attach the PowerPoint file (Joindre le fichier PowerPoint)*|**Attacher**|"PowerPoint": **FileType**|
|*Add the PDF file with the name signed contract (Ajouter le fichier PDF avec le contrat signé)* |**Attacher**|"PDF": **FileType**, <br> "signed contract": **FileName**|


Lorsque vous avez terminé, cliquez sur **Save Changes (Enregistrer les modifications)** pour enregistrer les énoncés et les étiquettes dans le projet. L’icône en regard du bouton devient verte lorsque les modifications sont enregistrées. Ensuite, accédez à la page **Train Model (Former le modèle)**.

:::image type="content" source="../../media/quickstart-utterances.png" alt-text="Capture d’écran montrant l’écran d’étiquetage des intentions dans Language Studio." lightbox="../../media/quickstart-utterances.png":::

## <a name="train-your-model-and-view-its-details"></a>Former votre modèle et afficher ses détails

Cliquez sur **Train Model (Former un modèle)** à gauche de l’écran. Pour former votre modèle, vous devez fournir un nom pour celui-ci. Écrivez un nom tel que « *v0.1* » et appuyez sur la touche Entrée. 

Désactivez **Run evaluation with training (Exécuter l’évaluation avec la formation)** avant de cliquer sur **Train (Former)**. 

Vous devez voir la page **View model details (Afficher les détails du modèle)**. Attendez la fin de la formation, ce qui peut prendre environ 5 minutes. Lorsque la formation est terminée, sélectionnez **Deploy Model (Déployer le modèle)** à gauche de l’écran.

## <a name="deploy-your-model"></a>Déployer votre modèle

Dans la page **Deploy Model (Déployer le modèle)** à gauche de l’écran, sélectionnez le modèle formé, puis cliquez sur le bouton **Deploy Model (Déployer le modèle)**. Dans l’écran qui s’affiche, cliquez sur **Deploy (Déployer)**.

## <a name="test-your-model"></a>Tester votre modèle

Cliquez sur **Test model (Tester le modèle)** à gauche de l’écran, puis sélectionnez le lien de modèle. Écrivez l’énoncé « *trash this one (mettre ceci à la corbeille)*  », puis cliquez sur **Run the test (Exécuter le test)**. 

Vous voyez maintenant la principale intention, **Suppress (Supprimer)**, sans entité.

Vous pouvez tester d’autres énoncés tels que :
* « *attach my docx file (joindre mon fichier docx)*  », 
* « *read the email by Jason (lire l’e-mail de Jason)*  », 
* « *attach the ppt file named CLU demo (Joindre le fichier ppt nommé CLU demo)*  ».

Vous pouvez également tester des énoncés dans d’autres langues, tels que les expressions suivantes :

* « *Joindre le fichier PDF* » 
* « *lesen sie die email von Macy* » (« *lire l’e-mail de Macy* »)
