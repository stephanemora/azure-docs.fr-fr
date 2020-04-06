---
title: Importation depuis des sources de données – QnA Maker
description: Une base de connaissances QnA Maker est composée d’un jeu d’ensembles de questions-réponses (QnA) et de métadonnées facultatives associées à chaque paire QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7772b2c76dfb92ced4d2c0d4b8dae97a59d1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457819"
---
# <a name="importing-from-data-sources"></a>Importation depuis des sources de données

Une base de connaissances est constituée d’ensembles de questions-réponses apportés par des URL et des fichiers publics.

## <a name="data-source-locations"></a>Emplacements des sources de données

Le contenu est placé dans une base de connaissances à partir d’une source de données. Les emplacements des sources de données sont des **URL ou fichiers publics**, qui ne requièrent pas d’authentification.

Les [fichiers SharePoint](../how-to/add-sharepoint-datasources.md), sécurisés par une authentification, sont l’exception. Les ressources SharePoint doivent être des fichiers, et non des pages web. Si l’URL se termine par une extension web, telle que .ASPX, elle n’est pas importée dans QnA Maker à partir de SharePoint.

## <a name="chit-chat-content"></a>Contenu des bavardages

L’ensemble de contenu QnA des bavardages est proposé sous la forme d’une source de données de contenu complète dans plusieurs langues et styles de conversation. Cela peut constituer un point de départ pour la personnalité de votre bot, et vous permettra d’économiser le temps et les efforts qui seraient nécessaires à leur écriture à partir de zéro. Découvrez [comment ajouter](../how-to/chit-chat-knowledge-base.md) cet ensemble de contenu à votre base de connaissances.

## <a name="structured-data-format-through-import"></a>Format de données structurées via l’importation

L’importation d’une base de connaissances remplace le contenu de la base de connaissances existante. L’importation requiert un fichier `.tsv` structuré qui contient des questions et leur réponse. Ces informations aident QnA Maker à regrouper les ensembles de question-réponse et à les attribuer à une source de données en particulier.

| Question  | Réponse  | Source| Métadonnées (1 clé : 1 valeur) |
|-----------|---------|----|---------------------|
| Question1 | Réponse1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 | Éditorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Format multitour structuré via l’importation

Vous pouvez créer les conversations multitours dans un format de fichier `.tsv`. Le format vous permet de créer les conversations multitours en analysant les journaux de conversation précédents (avec d’autres processus, sans utiliser QnA Maker), puis de créer le fichier `.tsv` par le biais de l’automatisation. Importez le fichier pour remplacer la base de connaissances existante.

> [!div class="mx-imgBorder"]
> ![Modèle conceptuel de trois niveaux de question multitour](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

La colonne d’un `.tsv` multitour, spécifique au multitour, est **Invites**. Un exemple de `.tsv`, affiché dans Excel, illustre les informations à inclure pour définir les enfants multitours :

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

Le **displayOrder** est numérique et le **displayText** est un texte qui ne doit pas inclure de Markdown.

> [!div class="mx-imgBorder"]
> ![Exemple de questions multitours comme indiqué dans Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exporter en tant qu’exemple

Si vous ne savez pas comment représenter votre ensemble QnA dans le fichier `.tsv` :
* Utilisez cet [exemple téléchargeable à partir de GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Ou créez le jeu dans le portail QnA Maker, enregistrez, puis exportez la base de connaissances pour obtenir un exemple illustrant la façon de représenter le jeu.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cycle de vie du développement d’une base de connaissances](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Voir aussi

Pour mettre en forme vos réponses, utilisez la [référence Markdown](../reference-markdown-format.md) de QnA Maker.

[Vue d’ensemble de QnA Maker](../Overview/overview.md)

Créez et modifiez la base de connaissances avec :
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Générez une réponse avec :
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
