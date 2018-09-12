---
title: 'Tutoriel : Utiliser l’entité pattern.any pour améliorer les prédictions de LUIS – Azure | Microsoft Docs'
titleSuffix: Cognitive Services
description: Ce tutoriel utilise l’entité pattern.any afin d’améliorer les prédictions de LUIS en matière d’intentions et d’entités.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 43f169ae11191c2e98c4538189bce781821de980
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157852"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Tutoriel : Améliorer l’application avec l’entité pattern.any

Ce tutoriel utilise l’entité pattern.any afin d’améliorer les prédictions en matière d’intentions et d’entités.  

> [!div class="checklist"]
* Découvrir quand et comment utiliser pattern.any
* Ajouter un modèle qui utilise l’entité pattern.any
* Vérifier l’amélioration des prédictions

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Avant de commencer
Si vous ne disposez pas de l’application Ressources humaines du tutoriel [rôles de modèle](luis-tutorial-pattern-roles.md), [importez](luis-how-to-start-new-app.md#import-new-app) le JSON dans une nouvelle application du site Web [LUIS](luis-reference-regions.md#luis-website). L’application à importer se trouve dans le référentiel GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json).

Si vous souhaitez conserver l’application Ressources humaines d’origine, clonez la version sur la page [Paramètres](luis-how-to-manage-versions.md#clone-a-version), et nommez-la `patt-any`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. 

## <a name="the-purpose-of-patternany"></a>L’objectif de pattern.any
L’entité pattern.any vous permet de rechercher des données de forme libre où le libellé de l’entité ne permet pas de distinguer la fin de l’entité du reste de l’énoncé. 

Cette application Ressources humaines permet aux employés de trouver les formulaires de l’entreprise. Des formulaires ont été ajoutés dans le [tutoriel sur l’expression régulière](luis-quickstart-intents-regex-entity.md). Dans ce tutoriel, les noms de formulaire utilisaient une expression régulière pour extraire un nom de formulaire correctement mis en forme, par exemple les noms de formulaire en gras dans le tableau d’énoncé suivant :

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

Certaines expressions peuvent être source de confusion pour LUIS, qui ne sait pas où se termine l’entité. Dans un modèle, l’entité Pattern.any permet de spécifier le début et la fin du nom du formulaire afin que LUIS puisse extraire correctement son nom.

**Même si les modèles vous permettent de fournir moins d’exemples d’énoncés, si les entités ne sont pas détectées, le modèle ne correspond pas.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>Ajouter des exemples d’énoncés à l’intention existante FindForm 
Supprimez l’entité keyPhrase prédéfinie si vous avez des difficultés à créer et étiqueter l’entité FormName. 

1. Sélectionnez **Build** dans le volet de navigation supérieur, puis **Intentions** dans le volet de navigation gauche.

2. Sélectionnez **FindForm** dans la liste des intentions.

3. Ajoutez quelques exemples d’énoncés :

    |Exemple d’énoncé|
    |--|
    |Où se trouve le formulaire **What to do when a fire breaks out in the Lab** (Que faire en cas d’incendie dans le labo) ? Et qui doit le signer une fois que je l’ai lu ?|
    |Où se trouve le formulaire **Request relocation from employee new to the company** (demande de réaffectation d'un nouvel employé) sur le serveur ?|
    |Qui a écrit le formulaire **Health and wellness requests on the main campus** (demandes concernant la santé et le bien-être sur le campus principal) et quelle est la version la plus récente ?|
    |Je recherche le formulaire intitulé **Office move request including physical assets** (demande de déménagement de bureau, y compris les biens matériels). |

    Sans entité Pattern.any, il serait difficile pour LUIS de comprendre où se termine le titre du formulaire en raison des nombreuses variations des noms de formulaire.

## <a name="create-a-patternany-entity"></a>Créer une entité Pattern.any
L’entité Pattern.any extrait des entités de longueur variable. Elle fonctionne seulement dans un modèle, car le modèle marque le début et la fin de l’entité. Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](luis-concept-patterns.md#explicit-lists) pour corriger ce problème. 

1. Sélectionnez **Entités** dans le volet de navigation gauche.

2. Sélectionnez **Créer une entité**, entrez le nom `FormName`, puis choisissez le type **Pattern.any**. Sélectionnez **Terminé**. 

    Vous ne peut pas étiqueter l’entité dans l’intention car Pattern.any est uniquement valide dans un modèle. 

    Si vous souhaitez que les données extraites incluent d’autres entités telles qu’un nombre ou une valeur datetimeV2, vous devez créer une entité composite incluant l’entité Pattern.any ainsi que le nombre et la valeur datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Ajouter un modèle qui utilise l’entité Pattern.any

1. Sélectionnez **Modèles** dans le volet de navigation gauche.

2. Sélectionnez l’intention **FindForm**.

3. Entrez les modèles d’énoncés suivants, qui utilisent la nouvelle entité :

    |Modèles d’énoncés|
    |--|
    |Où se trouve le formulaire ["]{FormName}["] et qui doit le signer une que je l’ai lu [?]|
    |Où se trouve le formulaire ["]{FormName}["] sur le serveur [?]|
    |Qui a écrit le formulaire ["]{FormName}["] et quelle est la version la plus récente [?]|
    |Je recherche le formulaire intitulé ["]{FormName}["][.]|

    Si vous souhaitez prendre en compte les variations du formulaire, comme des guillemets simples au lieu de guillemets doubles ou un point au lieu d’un point d’interrogation, créez un modèle pour chaque variante.

4. Si vous avez supprimé l’entité keyPhrase, replacez-la dans l’application. 

## <a name="train-the-luis-app"></a>Entraîner l’application LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tester l’extraction de données à structure libre dans le nouveau modèle
1. Sélectionnez **Tester** dans la barre supérieure pour ouvrir le panneau de test. 

2. Entrez l’énoncé suivant : 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Sélectionnez **Inspecter** sous le résultat afin de voir les résultats des tests pour l’entité et l’intention.

    L’entité `FormName` est trouvée en premier, suivie du modèle, indiquant l’intention. Si, dans votre résultat de test, les entités ne sont pas détectées et, par conséquent, le modèle est introuvable, vous devrez ajouter des exemples d’énoncés sur l’intention (et non le modèle).

4. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

## <a name="clean-up-resources"></a>Supprimer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de rôles avec un modèle](luis-tutorial-pattern-roles.md)