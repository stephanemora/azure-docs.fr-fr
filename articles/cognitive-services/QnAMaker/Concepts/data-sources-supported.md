---
title: Sources de données prises en charge - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extrait automatiquement des paires de questions-réponses à partir de contenu semi-structuré tel que des Questions fréquentes (FAQ), manuels de produits, instructions, documents de prise en charge et stratégies stockés sous forme de pages web, fichiers PDF ou fichiers de documents MS Word. Le contenu peut également être ajouté à la base de connaissances à partir de fichiers de contenu QnA structurés.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/04/2019
ms.author: tulasim
ms.openlocfilehash: b30f910b7eebabe02443e5b3ce328a89a25c7a6f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213699"
---
# <a name="data-sources-for-qna-maker-content"></a>Sources de données pour le contenu QnA Maker

QnA Maker extrait automatiquement des paires de questions-réponses à partir de contenu semi-structuré tel que des Questions fréquentes (FAQ), manuels de produits, instructions, documents de prise en charge et stratégies stockés sous forme de pages web, fichiers PDF ou fichiers de documents MS Word. Le contenu peut également être ajouté à la base de connaissances à partir de fichiers de contenu QnA structurés. 

Le tableau ci-dessous récapitule les types de contenu et formats de fichiers pris en charge par QnA Maker.

|Type de source|Type de contenu| Exemples|
|--|--|--|
|URL|FAQ<br> (plates, avec des sections ou une page d’accueil de rubriques)<br>Pages de support <br> (Articles sur les procédures d’une seule page, articles sur la résolution des problèmes, etc.)|[FAQ brut](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Forum aux questions avec des liens](https://www.microsoft.com/software-download/faq),<br> [Forum aux questions avec une page d’accueil contenant des rubriques](https://support.microsoft.com/products/windows?os=windows-10)<br>[Article de support technique](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs,<br> Manuel de produit,<br> Brochures,<br> Article,<br> Stratégie de prospectus,<br> Guide de support,<br> Questions et réponses structurées,<br> etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Fichier QnA structuré<br> (y compris la prise en charge de RTF et HTML)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Fichier QnA structuré|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Emplacements des sources de données

Seules les URL publiques sont valides pour toutes les sources de données. Ne soumettez pas de sources de données qui réclament une authentification. Vous pouvez télécharger le fichier sur le site authentifié, puis utiliser l’option de chargement de fichier pour extraire les questions et les réponses.

## <a name="faq-urls"></a>URL de FAQ

QnA Maker peut prendre en charge 3 différentes formes de pages web de questions fréquentes (FAQ), à savoir : des pages de FAQ simples, des pages de FAQ avec liens, des pages de FAQ avec une page d’accueil de rubriques.

### <a name="plain-faq-pages"></a>Pages de FAQ simples

Il s’agit du type le plus courant de page de FAQ, dans lequel les réponses suivent immédiatement les questions dans la même page. 

Voici un exemple d’une page de FAQ simple :

![Exemple de page de FAQ simple pour une base de connaissances](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Pages de FAQ avec des liens 

Dans ce type de page de FAQ, les questions sont regroupées puis associées à des réponses qui se trouvent soit dans différentes sections de la même page, soit dans différentes pages.

Voici un exemple de page de FAQ avec des liens dans des sections qui se trouvent dans la même page :

 ![Exemple de page de FAQ avec liens de sections pour une base de connaissances](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Pages de FAQ avec une page d’accueil de rubriques

Ce type de FAQ comporte une page d’accueil avec des rubriques, où chaque rubrique est un lien vers ses QnA pertinents dans une autre page. Ici, QnA Maker analyse toutes les pages associées pour extraire les questions-réponses correspondantes.

Voici un exemple de page de FAQ où une page d’accueil de rubriques comporte des liens vers des sections de FAQ dans différentes pages. 

 ![Exemple de page de FAQ avec liens profonds pour une base de connaissances](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>URL de support technique

QnA Maker peut traiter des pages web de support semi-structurées, telles que des articles web décrivant comment effectuer une tâche donnée, comment diagnostiquer et résoudre un problème donné, ainsi quelles sont les meilleures pratiques pour un processus donné. L’extraction fonctionne de façon optimale sur du contenu ayant une structure claire avec des titres hiérarchiques.

> [!NOTE]
> L’extraction pour les articles de support est une nouvelle fonctionnalité à un stade de développement encore précoce. Il convient idéalement pour les pages simples, qui sont bien structurées et qui ne contiennent pas d’en-têtes ou pieds de page complexes.

![QnA Maker prend en charge l’extraction à partir de pages web semi-structurées où une structure claire est présentée avec des titres hiérarchiques](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Fichiers PDF/ DOC

QnA Maker peut traiter du contenu semi-structuré dans un fichier PDF ou DOC, et le convertir en QnA. Un bon fichier pouvant être extrait correctement est un fichier dans lequel le contenu est organisé sous forme structurée et représenté dans des sections bien définies. Les sections peuvent ensuite être décomposées en sous-sections ou en sous-rubriques. L’extraction fonctionne mieux sur les documents qui ont une structure claire avec des en-têtes hiérarchiques.

QnA Maker identifie les sections, sous-sections et relations dans le fichier en fonction d’indices visuels comme la taille de police, le style de police, la numérotation, les couleurs, et ainsi de suite. Les fichiers PDF ou DOC semi-structurés peuvent être des manuels, des FAQ, des instructions, des stratégies, des brochures, des prospectus et de nombreux autres types de fichiers. Voici quelques exemples de types de ces fichiers.

### <a name="product-manuals"></a>Manuels de produit

Un manuel comprend habituellement des supports d’orientation qui accompagnent un produit. Il aide l’utilisateur à configurer, utiliser, gérer et dépanner le produit. Lorsque QnA Maker traite un manuel, il extrait les titres et les sous-titres sous forme de questions et le contenu qui suit sous forme de réponses. Un exemple vous est proposé [ici](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Voici un exemple de manuel avec une page d’index et du contenu hiérarchique

 ![Exemple de manuel de produit pour une base de connaissances](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> L’extraction est plus efficace sur les manuels qui ont une table des matières et/ou une page d’index, mais aussi une structure clairement définie avec des en-têtes hiérarchiques.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, instructions, livres blancs et autres fichiers

De nombreux autres types de documents peuvent également être traités pour générer des paires de questions-réponses, à condition qu’ils aient une structure et une disposition claires. Il s’agit des actions suivantes : brochures, instructions, rapports, livres blancs, articles scientifiques, stratégies, ouvrages, etc. Un exemple vous est proposé [ici](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

 ![Exemple de document QnA structuré pour une base de connaissances](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Fichiers *TXT*, *TSV* et *XLS* structurés

Les QnA sous forme de fichiers *.txt*, *.tsv* ou *.xls* structurés peuvent également être chargés vers QnA Maker pour créer ou augmenter une base de connaissances.  Il peuvent être en texte brut, ou peuvent avoir du contenu au format RTF ou HTML. 

| Question  | Réponse  | Métadonnées                |
|-----------|---------|-------------------------|
| Question1 | Réponse1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 |      `Key:Value`           |

Toutes les autres colonnes du fichier source sont ignorées.

Voici un exemple de fichier *.xls* QnA structuré, avec du contenu HTML :

 ![Exemple de feuille Excel QnA structurée pour une base de connaissances](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

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

|Format|Objectif|
|--|--|
|`\n\n`| Nouvelle ligne|
|`\n*`|Point pour une liste ordonnée|

## <a name="editing-your-knowledge-base-locally"></a>Modification de votre base de connaissances en local

Une fois qu’une base de connaissances est créée, il est recommandé d’apporter des modifications au texte associé dans le [portail QnA Maker](https://qnamaker.ai), plutôt que d’exporter et de réimporter via des fichiers locaux. Toutefois, vous pouvez être amené à modifier une base de connaissances localement. 

Exportez la base de connaissances à partir de la page **Paramètres**, puis modifiez la base de connaissances avec Microsoft Excel. Si vous choisissez d’utiliser une autre application pour modifier votre fichier TSV exporté, celle-ci peut introduire des erreurs de syntaxe, car elle ne prend pas totalement en charge le format TSV. Les fichiers TSV de Microsoft Excel n’entraînent généralement pas d’erreurs de mise en forme. 

Une fois que vous avez terminé vos modifications, réimportez le fichier TSV à partir de la page **Paramètres**. Cela remplace complètement la base de connaissances actuelle par la base de connaissances importée. 

## <a name="testing-your-markdown"></a>Test de votre Markdown

Utilisez le tutoriel **[CommonMark](https://commonmark.org/help/tutorial/index.html)** pour valider votre Markdown. Ce tutoriel a une fonctionnalité **Essayer** pour la validation rapide de copier/coller. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer un service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
