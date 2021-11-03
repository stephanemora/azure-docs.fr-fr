---
title: Gérer les bases de connaissances - Réponses aux questions
description: Les réponses aux questions personnalisées vous permettent de gérer des projets en fournissant un accès aux paramètres et au contenu du projet.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 1a28a0a4bf66824cc1c25d73512415e2a5613827
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097686"
---
# <a name="create-and-manage-project-settings"></a>Créer et gérer des paramètres de projet

Les réponses aux questions vous permettent de gérer vos projets/bases de connaissances en fournissant un accès aux paramètres du projet et aux sources de données. Si vous n’avez encore jamais créé de projet de réponse aux questions, nous vous conseillons de commencer par l’[article de prise en main](create-test-deploy.md).

## <a name="prerequisites"></a>Prérequis

> * Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
> * Une [ressource Language](https://aka.ms/create-language-resource) avec la fonctionnalité de réponses aux questions personnalisées activée dans le portail Azure. Rappelez-vous l’ID Azure Active Directory, l’abonnement et le nom de ressource de langue que vous avez sélectionnés au moment de créer la ressource.

## <a name="create-a-project"></a>Création d’un projet

1. Connectez-vous au portail [Language Studio](https://language.azure.com/) avec vos informations d’identification Azure.

2. Ouvrez la page de [réponses aux questions](https://language.azure.com/languageStudio/questionAnswering/projects).

3. Sélectionnez **Create new project**.

4. Si vous créez le premier projet associé à votre ressource de langue, vous avez la possibilité de créer des projets ultérieurs avec plusieurs langues pour la même ressource. Si vous choisissez de définir explicitement une langue unique dans votre premier projet, vous ne pourrez pas modifier ce paramètre ultérieurement, et tous les projets suivants pour cette ressource utiliseront la langue sélectionnée lors de la création de votre premier projet.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’interface utilisateur de sélection de la langue.](../media/manage-knowledge-base/choose-language-option.png)

5. Entrez les paramètres de base du projet :

    |Paramètre| Valeur|
    |-------|------|
    |**Nom** | Entrez le nom unique de votre projet ici (« projet » et « base de connaissances » font référence à la même chose dans le contexte des réponses aux questions.) |
    |**Description** | Entrer une description pour votre projet |
    |**Langue source** | Le caractère grisé de cette valeur dépend de la sélection effectuée lorsque le premier projet associé à la ressource de langue a été créé.  |
    |**Réponse par défaut** | Réponse par défaut que le système enverra si aucune réponse n’a été trouvée pour la question. Vous pouvez modifier ce paramètre à tout moment dans les paramètres de projet.

## <a name="manage-projects"></a>Gérer les projets

À partir de la page principale de réponse aux questions dans Language Studio, vous pouvez :

- Créer des projets
- Supprimer des projets.
- Exporter des projets existants pour la sauvegarde ou la migration vers d’autres ressources de langue.
- Importer des projets/bases de connaissances. (Le format de fichier attendu est un fichier `.zip` contenant un projet/une base de connaissances qui a été exporté au format `excel` ou `.tsv`).
- Les projets peuvent être triés par date de **dernière modification** ou de **dernière publication**.

## <a name="manage-sources"></a>Gérer les sources

1. Dans la barre de navigation de gauche, sélectionnez **Manage sources**.

1.  Il existe trois types de sources : **URLS**, **Files** et **Chitchat**

       |Objectif|Action|
       |--|--|
       |Ajouter la source|Vous pouvez ajouter de nouvelles sources et du contenu de FAQ à votre projet en sélectionnant **Add source** > et en choisissant **URLs**, **Files** ou **Chitchat**.|
       |Supprimer une source|Vous pouvez supprimer des sources existantes en sélectionnant à gauche de la source, ce qui entraîne l’affichage d’un cercle bleu avec une coche > sélectionnez l’icône de corbeille. |
       |Marquer du contenu comme non structuré|Si vous souhaitez marquer le contenu du fichier chargé comme non structuré, sélectionnez **Unstructured content** dans la liste déroulante lors de l’ajout de la source.|
       |Détection automatique| Autorisez Réponse aux questions à tenter de déterminer si le contenu est structuré ou non structuré.|

## <a name="manage-large-projects"></a>Gérer de grands projets

À partir de la **page de modification de base de connaissances**, vous pouvez :

* **Rechercher dans le projet** : vous pouvez effectuer une recherche dans le projet en tapant dans la zone de texte en haut du panneau de réponse aux questions. Appuyez sur Entrée pour effectuer une recherche dans le contenu des métadonnées, la question ou la réponse.

* **Pagination** : parcourez rapidement les sources de données pour gérer de grands projets. Les numéros de page s’affichent en bas de l’interface utilisateur et sont parfois hors écran.

## <a name="delete-project"></a>Supprimer le projet

La suppression d’un projet est une opération définitive. Elle ne peut pas être annulée. Avant de supprimer un projet, vous devez l’exporter à partir de la page principale de réponse aux questions dans Language Studio.

Si vous partagez votre projet avec des collaborateurs et que vous le supprimez par la suite, tout le monde perd l’accès au projet.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration des ressources](./configure-resources.md)
