---
title: 'Procédure : Utiliser des modèles de génération de langage pour les réponses vocales - service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous apprendrez à utiliser des modèles de génération de langage avec des commandes personnalisées. Les modèles de génération de langage vous permettent de personnaliser les réponses envoyées au client et d’introduire une variance dans les réponses.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020944"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Ajouter des modèles de génération de langage pour les réponses vocales

Dans cet article, vous apprendrez à utiliser des modèles de génération de langage avec des commandes personnalisées. Les modèles de génération de langage vous permettent de personnaliser les réponses envoyées au client et d’introduire une variance dans les réponses. Pour personnaliser la génération de langage, vous pouvez :

- Utiliser des modèles de génération de langage
- Utiliser des expressions adaptatives

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :

> [!div class="checklist"]
> * [Guide pratique pour Créer une application avec des commandes simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Guide pratique pour Ajouter des paramètres aux commandes](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Vue d’ensemble des modèles de génération de langage

Les modèles Commandes personnalisés sont basés sur les [modèles de génération de langage](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) BotFramework. Comme les commandes personnalisées créent un nouveau modèle de génération de langage quand cela est nécessaire (autrement dit, pour les réponses vocales dans les paramètres ou les actions), vous n’avez pas besoin de spécifier le nom du modèle de génération de langage. Par conséquent, au lieu de définir votre modèle ainsi :

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Vous devez uniquement définir le corps du modèle sans le nom, comme suit.

> [!div class="mx-imgBorder"]
> ![template editor example](./media/custom-commands/template-editor-example.png)


Cette modification introduit une variation aux réponses vocales envoyées au client. Ainsi, pour le même énoncé, la réponse vocale correspondante est choisie de manière aléatoire parmi les options fournies.

Les modèles de génération de langage vous permettent également de définir des réponses vocales complexes pour les commandes à l’aide d’expressions adaptatives. Pour plus d’informations, reportez-vous au [format des modèles de génération de langage](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). Par défaut, les commandes personnalisées prennent en charge toutes les fonctionnalités avec les différences mineures suivantes :

* Dans les modèles de génération de langage, les entités sont représentées sous la forme ${entityName}. Dans les commandes personnalisées, nous n’utilisons pas d’entités, mais les paramètres peuvent servir de variables avec l’une de ces représentations : ${parameterName} ou {parameterName}
* La composition et l’expansion de modèle ne sont pas prises en charge dans les commandes personnalisées. Cela est dû au fait que vous ne modifiez jamais directement le fichier `.lg`, mais uniquement les réponses de modèles automatiquement créés.
* Les fonctions personnalisées injectées par la génération de langage ne sont pas prises en charge dans les commandes personnalisées. Les fonctions prédéfinies sont toujours prises en charge.
* Les options (strict, replaceNull & lineBreakStyle) ne sont pas prises en charge dans les commandes personnalisées.

## <a name="add-template-responses-to-turnonoff-command"></a>Ajouter des modèles de réponses à la commande TurnOnOff

Modifiez la commande **TurnOnOff** pour ajouter un nouveau paramètre avec la configuration suivante :

| Paramètre            | Valeur suggérée       | 
| ------------------ | --------------------- | 
| Nom               | `SubjectContext`         | 
| Est global          | non cochée             | 
| Obligatoire           | non cochée               | 
| Type               | String                |
| Valeur par défaut      | `all` |
| Configuration      | Accepter les valeurs d'entrée prédéfinies du catalogue interne | 
| Valeurs d'entrée prédéfinies | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Modifier une règle d’exécution

Modifiez la section **Actions** de la règle d’exécution existante **ConfirmationResponse**. Dans la fenêtre contextuelle **Modifier l’action**, basculez vers l’**Éditeur de modèle** et remplacez le texte par l’exemple suivant.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Effectuez l'apprentissage** et **testez** votre application comme suit. Notez la variation des réponses en raison de l’utilisation de plusieurs alternatives du modèle de valeur et de l’utilisation d’expressions adaptatives.

* Entrée : allume la télévision
* Sortie : D’accord, j’allume la télévision
* Entrée : allume la télévision
* Sortie : Ok, télévision allumée
* Entrée : éteindre les lumières
* Sortie : OK, éteindre toutes les lumières
* Entrée : éteindre les lumières de la pièce
* Sortie : OK, éteindre les lumières de la pièce

## <a name="use-custom-voice"></a>Utiliser une voix personnalisée

Une autre façon de personnaliser les réponses de commandes personnalisées consiste à sélectionner une voix de sortie personnalisée. Procédez comme suit pour remplacer la voix par défaut par une voix personnalisée.

1. Dans votre application Commandes personnalisées, sélectionnez **Paramètres** dans le volet gauche.
1. Sélectionnez **Custom Voice** dans le volet central.
1. Sélectionnez la voix personnalisée ou publique souhaitée dans le tableau.
1. Sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Exemples de phrases avec paramètres](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Concernant les **Voix publiques**, les **Types neuronaux** ne sont disponibles que pour certaines régions. Pour connaître la disponibilité, consultez [Voix standard et neuronales par région/point de terminaison](./regions.md#standard-and-neural-voices).
> - Les **voix personnalisées** peuvent être créées sur la page du projet Custom Voice. Consultez [Bien démarrer avec Custom Voice](./how-to-custom-voice.md).

L’application répond à présent avec la voix sélectionnée, et non plus avec la voix par défaut.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Intégrer vos commandes personnalisées à l’aide du kit de développement logiciel (SDK) Speech](./how-to-custom-commands-setup-speech-sdk.md).