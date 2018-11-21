---
title: Sources de données prises en charge - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extrait automatiquement des paires de questions-réponses à partir de contenu semi-structuré tel que des Questions fréquentes (FAQ), manuels de produits, instructions, documents de prise en charge et stratégies stockés sous forme de pages web, fichiers PDF ou fichiers de documents MS Word. Le contenu peut également être ajouté à la base de connaissances à partir de fichiers de contenu QnA structurés.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: e6c654b00ee6be0ed87feb0fb2a5ccba38e5cbe4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624875"
---
# <a name="data-sources-for-qna-maker-content"></a>Sources de données pour le contenu QnA Maker

QnA Maker extrait automatiquement des paires de questions-réponses à partir de contenu semi-structuré tel que des Questions fréquentes (FAQ), manuels de produits, instructions, documents de prise en charge et stratégies stockés sous forme de pages web, fichiers PDF ou fichiers de documents MS Word. Le contenu peut également être ajouté à la base de connaissances à partir de fichiers de contenu QnA structurés. 

Le tableau ci-dessous récapitule les types de contenu et formats de fichiers pris en charge par QnA Maker.

|Type de source|Type de contenu| Exemples|
|--|--|--|
|URL|Questions fréquentes (FAQ) (plates, avec des sections ou une page d’accueil de rubriques)|[FAQ simple](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), [FAQ avec des liens](https://www.microsoft.com/software-download/faq), [FAQ avec une page d’accueil de rubriques](https://support.microsoft.com/products/windows?os=windows-10)|
|PDF / DOC|FAQ, manuel de produit, brochures, livres blancs, stratégie de prospectus, guide de prise en charge, QnA structurés, etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx), [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf), [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx), [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Fichier QnA structuré (notamment prise en charge RTF, HTML)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Fichier QnA structuré|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="faq-urls"></a>URL de FAQ

QnA Maker peut prendre en charge les pages web de FAQ dans trois différentes formes : pages de FAQ simples, pages de FAQ avec des liens, pages de FAQ avec une page d’accueil des rubriques.

### <a name="plain-faq-pages"></a>Pages de FAQ simples

Il s’agit du type le plus courant de page de FAQ, dans lequel les réponses suivent immédiatement les questions dans la même page. 

Voici un exemple d’une page de FAQ simple :

![Page de FAQ simple](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Pages de FAQ avec des liens 

Dans ce type de page de FAQ, les questions sont regroupées puis associées à des réponses qui se trouvent soit dans différentes sections de la même page, soit dans différentes pages.

Voici un exemple de page de FAQ avec des liens dans des sections qui se trouvent dans la même page :

 ![Page de FAQ avec lien de section](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Pages de FAQ avec une page d’accueil de rubriques

Ce type de FAQ comporte une page d’accueil avec des rubriques, où chaque rubrique est un lien vers ses QnA pertinents dans une autre page. Ici, QnA Maker analyse toutes les pages associées pour extraire les questions-réponses correspondantes.

Voici un exemple de page de FAQ où une page d’accueil de rubriques comporte des liens vers des sections de FAQ dans différentes pages. 

 ![Page de FAQ avec lien ciblé](../media/qnamaker-concepts-datasources/topics-faq.png) 


## <a name="pdf-doc-files"></a>Fichiers PDF/ DOC

QnA Maker peut traiter du contenu semi-structuré dans un fichier PDF ou DOC, et le convertir en QnA. Un bon fichier pouvant être extrait correctement est un fichier dans lequel le contenu est organisé sous forme structurée et représenté dans des sections bien définies. Les sections peuvent ensuite être décomposées en sous-sections ou en sous-rubriques. L’extraction fonctionne mieux sur les documents qui ont une structure claire avec des en-têtes hiérarchiques.

QnA Maker identifie les sections, sous-sections et relations dans le fichier en fonction d’indices visuels comme la taille de police, le style de police, la numérotation, les couleurs, et ainsi de suite. Les fichiers PDF ou DOC semi-structurés peuvent être des manuels, des FAQ, des instructions, des stratégies, des brochures, des prospectus et de nombreux autres types de fichiers. Voici quelques exemples de types de ces fichiers.

### <a name="product-manuals"></a>Manuels de produit

Un manuel comprend habituellement des supports d’orientation qui accompagnent un produit. Il aide l’utilisateur à configurer, utiliser, gérer et dépanner le produit. Lorsque QnA Maker traite un manuel, il extrait les titres et les sous-titres sous forme de questions et le contenu qui suit sous forme de réponses. Un exemple vous est proposé [ici](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Voici un exemple de manuel avec une page d’index et du contenu hiérarchique

 ![Exemple de manuel de produit](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> L’extraction est plus efficace sur les manuels qui ont une table des matières et/ou une page d’index, mais aussi une structure clairement définie avec des en-têtes hiérarchiques.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, instructions, livres blancs et autres fichiers

De nombreux autres types de documents peuvent également être traités pour générer des paires de questions-réponses, à condition qu’ils aient une structure et une disposition claires. Il s’agit notamment des brochures, instructions, rapports, livres blancs, articles scientifiques, stratégies, ouvrages, et ainsi de suite. Un exemple vous est proposé [ici](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Voici un exemple de document semi-structuré, sans index :

 ![Document semi-structuré de Stockage d’objets blob Azure](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Document QnA structuré

Le format des questions-réponses structurées dans les fichiers DOC consiste à alterner les questions et les réponses ligne par ligne, une question sur une ligne suivie de sa réponse sur la ligne suivante, comme indiqué ci-dessous : 

```text
Question1

Answer1

Question2

Answer2
```

Voici un exemple de document Word QnA structuré :

 ![Document QnA structuré](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Fichiers *TXT*, *TSV* et *XLS* structurés

Les QnA sous forme de fichiers *.txt*, *.tsv* ou *.xls* structurés peuvent également être chargés vers QnA Maker pour créer ou augmenter une base de connaissances.  Il peuvent être en texte brut, ou peuvent avoir du contenu au format RTF ou HTML. 

| Question  | Réponse  | Métadonnées                |
|-----------|---------|-------------------------|
| Question1 | Réponse1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 |      `Key:Value`           |

Toutes les autres colonnes du fichier source sont ignorées.

Voici un exemple de fichier *.xls* QnA structuré, avec du contenu HTML :

 ![Fichier Excel QnA structuré](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Format de données structurées via l’importation

L’importation d’une base de connaissances remplace le contenu de la base de connaissances existante. L’importation nécessite un fichier .tsv structuré qui contient des informations sur la source de données. Ces informations aident QnA Maker à regrouper les paires de question-réponse et à les attribuer à une source de données en particulier.

| Question  | Réponse  | Source| Métadonnées                |
|-----------|---------|----|---------------------|
| Question1 | Réponse1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 | Éditorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Ajouter à la base de connaissances de manière éditoriale

Si vous ne possédez aucun contenu pour alimenter la base de connaissances, vous pouvez ajouter du contenu QnA éditorial dans la base de connaissances QnA Maker. Découvrez comment mettre à jour votre base de connaissances [ici](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Mise en forme - Éléments à prendre en compte

Après l’importation d’un fichier ou d’une URL, celui-ci est converti en Markdown et stocké dans ce format. Si le processus de conversion ne convertit pas correctement les liens dans vos fichiers et les URL, vous devez modifier les questions et réponses dans la page **Editer**. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer un service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
