---
title: Entité Pattern.any – LUIS
titleSuffix: Azure Cognitive Services
description: Utilisez l’entité pattern.any pour extraire des données à partir d’énoncés correctement mis en forme et où la fin des données peut être facilement confondue avec les mots restants de l’énoncé.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 165163d0a889e0bd5f1faef8d7f6a17d28c2b4f7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563275"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Didacticiel : Extraire des données de forme libre avec l’entité Pattern.any

Dans ce tutoriel, vous allez utiliser l’entité pattern.any pour extraire des données à partir d’énoncés correctement mis en forme et où la fin des données peut être facilement confondue avec les mots restants de l’énoncé. 

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Importer l’exemple d’application
> * Ajouter des exemples d’énoncés à une entité existante
> * Créer une entité Pattern.any
> * Créer un modèle
> * Former
> * Tester le nouveau modèle

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Utilisation de l’entité pattern.any

L’entité pattern.any vous permet de rechercher des données en forme libre où le libellé de l’entité ne permet pas de distinguer la fin de l’entité du reste de l’énoncé. 

Cette application Ressources humaines permet aux employés de trouver les formulaires de l’entreprise. 

|Énoncé|
|--|
|Où se trouve **HRF-123456** ?|
|Qui a créé **HRF-123234** ?|
|**HRF-456098** est-il publié en français ?|

Toutefois, chaque formulaire comporte à la fois un nom mis en forme utilisé dans le tableau précédent, ainsi qu’un nom convivial, tel que `Request relocation from employee new to the company 2018 version 5`. 

Les énoncés avec le nom du formulaire convivial ressemblent à ceci :

|Énoncé|
|--|
|Où se trouve le formulaire **Request relocation from employee new to the company 2018 version 5** (demande de réaffectation d'un nouvel employé) ?|
|Qui a écrit le formulaire **Request relocation from employee new to the company 2018 version 5** (demande de réaffectation d'un nouvel employé - 2018 version 5) ?|
|Le formulaire **Request relocation from employee new to the company 2018 version 5** (demande de réaffectation d'un nouvel employé - 2018 version 5) ? est-t disponible en français ?|

Certains mots peuvent être source de confusion pour LUIS, qui ne sait pas où se termine l’entité. Dans un modèle, l’entité Pattern.any permet de spécifier le début et la fin du nom du formulaire afin que LUIS puisse extraire correctement son nom.

|Exemple de modèle d’énoncé|
|--|
|Où se trouve {FormName}[?]|
|Qui a créé {FormName}[?]|
|{nom_formulaire} est-il publié en Français[?]|

## <a name="import-example-app"></a>Importer l’exemple d’application

1. Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json).

1. Dans le [portal LUIS](https://www.luis.ai), sur la page **Mes applications**, importez le code JSON dans une nouvelle application.

1. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `patt-any`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="add-example-utterances"></a>Ajouter des exemples d’énoncés 

1. Sélectionnez **Build** dans le volet de navigation supérieur, puis **Intentions** dans le volet de navigation gauche.

1. Sélectionnez **FindForm** dans la liste des intentions.

1. Ajoutez quelques exemples d’énoncés :

    |Exemple d’énoncé|
    |--|
    |Où se trouve le formulaire **What to do when a fire breaks out in the Lab** (Que faire en cas d’incendie dans le labo) ? Et qui doit le signer une fois que je l’ai lu ?|
    |Où se trouve le formulaire **Request relocation from employee new to the company** (demande de réaffectation d'un nouvel employé) sur le serveur ?|
    |Qui a écrit le formulaire **Health and wellness requests on the main campus** (demandes concernant la santé et le bien-être sur le campus principal) et quelle est la version la plus récente ?|
    |Je recherche le formulaire intitulé **Office move request including physical assets** (demande de déménagement de bureau, y compris les biens matériels). |

    Sans entité Pattern.any, il serait difficile pour LUIS de comprendre où se termine le titre du formulaire en raison des nombreuses variations des noms de formulaire.

## <a name="create-a-patternany-entity"></a>Créer une entité Pattern.any
L’entité Pattern.any extrait des entités de longueur variable. Elle fonctionne seulement dans un modèle, car le modèle marque le début et la fin de l’entité.  

1. Sélectionnez **Entités** dans le volet de navigation gauche.

1. Sélectionnez **Créer une entité**, entrez le nom `FormName`, puis choisissez le type **Pattern.any**. Sélectionnez **Terminé**. 

    Vous ne peut pas étiqueter l’entité avec les exemples d’énoncés de l’intention, car Pattern.any est uniquement valide dans un modèle. 

    Si vous souhaitez que les données extraites incluent d’autres entités telles qu’un nombre ou une valeur datetimeV2, vous devez créer une entité composite incluant l’entité Pattern.any ainsi que le nombre et la valeur datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Ajouter un modèle qui utilise l’entité Pattern.any

1. Sélectionnez **Modèles** dans le volet de navigation gauche.

1. Sélectionnez l’intention **FindForm**.

1. Entrez les modèles d’énoncés suivants, qui utilisent la nouvelle entité :

    |Modèles d’énoncés|
    |--|
    |Où se trouve le formulaire ["]{FormName}["] et qui doit le signer une que je l’ai lu [?]|
    |Où se trouve le formulaire ["]{FormName}["] sur le serveur [?]|
    |Qui a écrit le formulaire ["]{FormName}["] et quelle est la version la plus récente [?]|
    |Je recherche le formulaire intitulé ["]{FormName}["][.]|

    Si vous souhaitez prendre en compte les variations du formulaire, comme des guillemets simples au lieu de guillemets doubles ou un point au lieu d’un point d’interrogation, créez un modèle pour chaque variante.

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tester l’extraction de données à structure libre dans le nouveau modèle
1. Sélectionnez **Tester** dans la barre supérieure pour ouvrir le panneau de test. 

1. Entrez l’énoncé suivant : 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Sélectionnez **Inspecter** sous le résultat afin de voir les résultats des tests pour l’entité et l’intention.

    L’entité `FormName` est trouvée en premier, suivie du modèle, indiquant l’intention. Si, dans votre résultat de test, les entités ne sont pas détectées et, par conséquent, le modèle est introuvable, vous devrez ajouter des exemples d’énoncés sur l’intention (et non le modèle).

1. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

## <a name="using-an-explicit-list"></a>Avec une liste explicite

Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](luis-concept-patterns.md#explicit-lists) pour corriger ce problème.


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel a ajouté des exemple d’énoncés à une intention existante, puis a créé un nouveau modèle Pattern.any pour le nom du formulaire. Ensuite, le tutoriel a créé un modèle pour l’intention existante avec la nouvelle entité et les nouveaux exemples d’énoncés. Des tests interactifs ont montré que le modèle et son intention étaient prédits car l’entité était trouvée. 

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de rôles avec un modèle](luis-tutorial-pattern-roles.md)
