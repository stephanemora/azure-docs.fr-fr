---
title: 'Démarrage rapide : Créer une commande personnalisée (préversion) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Cet article explique comment créer et tester une application de commandes personnalisées hébergée.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 5c90c91791af8a9a16039e9650765bd24433ff38
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815819"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Démarrage rapide : Créer une commande personnalisée (préversion)

Cet article décrit comment créer et tester une application de commandes personnalisées hébergée.
L’application reconnaît un énoncé tel que « Allume la télévision » et répond avec un simple message « D’accord, j’allume la télévision ».

## <a name="prerequisites"></a>Prérequis

- Abonnement Speech. [Essayez le service Speech gratuitement](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Pendant la période de préversion, seule la région WestUS2 est prise en charge pour les clés d’abonnement.

- Clé de création [Language Understanding](https://www.luis.ai/home) (LUIS) :
  1. Ouvrez un navigateur web et accédez au [portail Azure](https://portal.azure.com)
  1. Sélectionnez Créer une ressource
  1. Recherchez et sélectionnez [Language Understanding](https://aka.ms/sc-luis-all)
  1. Sélectionnez Création dans les options de création
  1. Une fois la ressource déployée, accédez-y et copiez la clé à partir de la section Démarrage rapide ou Clés

      > [!div class="mx-imgBorder"]
      > ![Créez une ressource de création](media/custom-speech-commands/resources-lu-authoring.png)

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Accédez à Speech Studio pour les commandes personnalisées

1. Ouvrez votre navigateur web et accédez à [Speech Studio](https://speech.microsoft.com/)
1. Entrez vos informations d’identification pour vous connecter au portail

   - L’affichage par défaut est votre liste d’abonnements Speech
     > [!NOTE]
     > Si vous ne voyez pas la page Sélectionner un abonnement, vous pouvez y accéder en choisissant « Ressources Speech » dans le menu Paramètres sur la barre supérieure.

1. Sélectionnez votre abonnement Speech, puis **Accéder à Studio**
1. Sélectionnez **Commandes personnalisées (préversion)**

L’affichage par défaut présente liste des applications Commandes personnalisées que vous avez créées.

## <a name="create-a-custom-commands-project"></a>Créer un projet Commandes personnalisées

1. Sélectionnez **Nouveau projet** pour créer un projet

   > [!div class="mx-imgBorder"]
   > ![Créer un projet](media/custom-speech-commands/create-new-project.png)

1. Entrez le nom et la langue du projet, puis sélectionnez **Suivant** pour continuer
1. Entrez votre clé de création LUIS
1. Une fois votre projet créé, sélectionnez-le

Votre affichage devrait à présent présenter une vue d’ensemble de votre application Commandes personnalisées.

## <a name="create-a-new-command"></a>Créer une commande

Vous pouvez maintenant créer une commande. Nous allons utiliser un exemple qui prendra un seul énoncé, `turn on the tv`, puis répondra avec le message `Ok, turning on the TV`.

1. Créez une commande en sélectionnant l’icône `+` en regard des commandes et attribuez-lui le nom `TurnOn`
1. Sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Créer une commande](media/custom-speech-commands/create-add-command.png)

Une commande comprend les éléments suivants :

| Groupe            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exemples de phrases | Exemples d’énoncés que l’utilisateur peut prononcer pour déclencher la commande                                                                 |
| parameters       | Informations requises pour exécuter la commande                                                                                |
| Règles d’exécution | Actions à effectuer pour exécuter la commande. Par exemple, répondre à l’utilisateur ou communiquer avec un autre service web |
| Règles avancées   | Règles supplémentaires pour gérer des situations plus spécifiques ou complexes                                                              |

### <a name="add-a-sample-sentence"></a>Ajouter un exemple de phrase

Commençons par des exemples de phrases et fournissons un exemple de ce que l’utilisateur peut dire :

```
turn on the tv
```

Pour le moment, comme nous n’avons aucun paramètre, nous pouvons passer aux Règles d’exécution.

### <a name="add-a-completion-rule"></a>Ajouter une règle d’exécution

Ajoutez maintenant une règle d’exécution pour répondre à l’utilisateur en lui indiquant qu’une action est en cours.

> [!div class="mx-imgBorder"]
> ![Créer une règle d’exécution](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Paramètre    | Valeur suggérée                        | Description                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Nom de la règle  | « RéponseConfirmation »                 | Nom décrivant l’objectif de la règle          |
| Conditions | Aucun                                   | Conditions déterminant le moment où la règle peut s’exécuter    |
| Actions    | SpeechResponse « D’accord, j’allume la télévision » | Action à exécuter lorsque la condition de la règle est remplie |

## <a name="try-it-out"></a>Faites un essai

Testez le comportement à l’aide du volet de conversation Tester.

> [!div class="mx-imgBorder"]
> ![Test avec WebChat](media/custom-speech-commands/create-basic-test-chat.png)

- Vous tapez : « Allume la télévision »
- La réponse attendue est : « D’accord, j’allume la télévision »

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)
