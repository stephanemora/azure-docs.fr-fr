---
title: Types de contenu - QnA Maker
description: Les types de contenu incluent bon nombre de documents structurés standard tels que PDF, DOC et TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843434"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Types de contenu de documents que vous pouvez ajouter à une base de connaissances
Les types de contenu incluent bon nombre de documents structurés standard tels que PDF, DOC et TXT.

## <a name="file-and-url-data-types"></a>Types de données de fichier et d’URL

Le tableau ci-dessous récapitule les types de contenu et formats de fichiers pris en charge par QnA Maker.

|Type de source|Type de contenu| Exemples|
|--|--|--|
|URL|FAQ<br> (plates, avec des sections ou une page d’accueil de rubriques)<br>Pages de support <br> (Articles sur les procédures d’une seule page, articles sur la résolution des problèmes, etc.)|[FAQ brut](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Forum aux questions avec des liens](https://www.microsoft.com/en-us/software-download/faq),<br> [Forum aux questions avec une page d’accueil contenant des rubriques](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Article de support technique](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs,<br> Manuel de produit,<br> Brochures,<br> Article,<br> Stratégie de prospectus,<br> Guide de support,<br> Questions et réponses structurées,<br> , etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Sample multi-turn.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|*Excel|Fichier QnA structuré<br> (y compris la prise en charge de RTF et HTML)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Fichier QnA structuré|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Si vous avez besoin d’une authentification pour votre source de données, envisagez les méthodes suivantes afin d'obtenir ce contenu dans QnA Maker :

* Télécharger le fichier manuellement et l’importer dans QnA Maker
* Ajouter un fichier à partir d'un [emplacement SharePoint](../how-to/add-sharepoint-datasources.md) authentifié

## <a name="url-content"></a>Contenu d'URL

Deux types de documents peuvent être importés via **URL** dans QnA Maker :

* URL de FAQ
* URL de support technique

Chaque type indique un format attendu.

## <a name="file-based-content"></a>Contenu basé sur des fichiers

Vous pouvez ajouter des fichiers à une base de connaissances à partir d’une source publique ou de votre système de fichiers local, dans le portail [QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Instructions relatives au format de contenu

QnA Maker ajoute du contenu de fichier et d’URL, en convertissant le contenu en ensembles de questions-réponses, stockés au format Markdown (`.md`). Un contenu adapté à une base de connaissances est un contenu organisé sous forme structurée et représenté dans des sections bien définies. Les sections peuvent ensuite être décomposées en sous-sections ou en sous-rubriques. L’extraction fonctionne de façon optimale sur du contenu ayant une structure claire avec des titres hiérarchiques.

QnA Maker identifie les sections, sous-sections et relations dans le contenu en fonction d’indices visuels comme la taille de police, le style de police, la numérotation, les couleurs, et ainsi de suite. Le contenu semi-structuré peut inclure des manuels, des FAQ, des instructions, des stratégies, des brochures, des prospectus et de nombreux autres types de fichiers.

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

De nombreux autres types de documents peuvent également être traités pour générer des paires de questions-réponses, à condition qu’ils aient une structure et une disposition claires. notamment : brochures, instructions, rapports, livres blancs, articles scientifiques, stratégies, ouvrages, etc. Un exemple vous est proposé [ici](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

| Question  | Réponse  | Métadonnées (1 clé : 1 valeur) |
|-----------|---------|-------------------------|
| Question1 | Réponse1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 |      `Key:Value`           |

Toutes les autres colonnes du fichier source sont ignorées.

### <a name="example-of-structured-excel-file"></a>Exemple de fichier Excel structuré

Voici un exemple de fichier *.xls* QnA structuré, avec du contenu HTML :

 ![Exemple de feuille Excel QnA structurée pour une base de connaissances](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemple de questions multiples avec réponse unique dans un fichier Excel

Voici un exemple de fichier *.xls* QnA structuré, avec plusieurs questions pour une réponse unique :

 ![Exemple de questions multiples avec réponse unique dans un fichier Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Une fois le fichier importé, la paire question-réponse figure dans la base de connaissances, comme indiqué ci-dessous :

 ![Capture d’écran de questions multiples avec réponse unique importées dans la base de connaissances](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>Mise en forme - Éléments à prendre en compte

Après l’importation d’un fichier ou d’une URL, QnA Maker convertit et stocke votre contenu dans le [format Markdown](https://en.wikipedia.org/wiki/Markdown). Le processus de conversion ajoute de nouvelles lignes dans le texte, telles que `\n\n`. Une connaissance du format Markdown vous aide à comprendre le contenu converti et à gérer le contenu de votre base de connaissances.

Si vous ajoutez ou modifiez votre contenu directement dans votre base de connaissances, utilisez la **mise en forme Markdown** pour créer un contenu de texte enrichi ou modifiez le contenu du format Markdown qui est déjà dans la réponse. QnA Maker prend en charge une grande partie du format Markdown pour offrir des fonctionnalités de texte enrichi à votre contenu. Toutefois, l’application cliente, telle qu’un bot de chat, peut ne pas prendre en charge le même ensemble de formats Markdown. Il est important de tester l’affichage des réponses de l’application cliente.

Pour plus d'informations, consultez la [documentation de référence Markdown QnA Maker](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>Test de votre Markdown

Utilisez le tutoriel **[CommonMark](https://commonmark.org/help/tutorial/index.html)** pour valider votre Markdown. Ce tutoriel a une fonctionnalité **Essayer** pour la validation rapide de copier/coller.

## <a name="next-steps"></a>Étapes suivantes

* Comprendre comment concevoir et gérer des [ensembles de questions-réponses (QnA)](question-answer-set.md)