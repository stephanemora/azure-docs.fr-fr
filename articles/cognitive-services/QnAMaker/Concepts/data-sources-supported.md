---
title: Sources de données prises en charge - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extrait automatiquement des paires question-réponse stockées sous forme de pages web, fichiers PDF, fichiers de document MS Word ou fichiers de contenu QnA structurés.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: dc948629784254c9153f7f48ead7ff253e5f4453
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806386"
---
# <a name="data-sources-for-qna-maker-content"></a>Sources de données pour le contenu QnA Maker

QnA Maker extrait automatiquement des paires de questions-réponses à partir de contenu semi-structuré tel que des Questions fréquentes (FAQ), manuels de produits, instructions, documents de prise en charge et stratégies stockés sous forme de pages web, fichiers PDF ou fichiers de documents MS Word. Le contenu peut également être ajouté à la base de connaissances à partir de fichiers de contenu QnA structurés. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Types de données de fichier et d’URL

Le tableau ci-dessous récapitule les types de contenu et formats de fichiers pris en charge par QnA Maker.

|Type de source|Type de contenu| Exemples|
|--|--|--|
|URL|FAQ<br> (plates, avec des sections ou une page d’accueil de rubriques)<br>Pages de support <br> (Articles sur les procédures d’une seule page, articles sur la résolution des problèmes, etc.)|[FAQ brut](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Forum aux questions avec des liens](https://www.microsoft.com/en-us/software-download/faq),<br> [Forum aux questions avec une page d’accueil contenant des rubriques](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Article de support technique](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs,<br> Manuel de produit,<br> Brochures,<br> Article,<br> Stratégie de prospectus,<br> Guide de support,<br> Questions et réponses structurées,<br> etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Sample multi-turn.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|*Excel|Fichier QnA structuré<br> (y compris la prise en charge de RTF et HTML)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Fichier QnA structuré|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Importer et exporter une base de connaissances

L’utilisation de **fichiers TSV et XLS** provenant de bases de connaissances exportées n’est possible qu’en les important à partir de la page **Paramètres** du portail QnA Maker. Il est impossible de les utiliser en tant que sources de données lors de la création de la base de connaissances ou à partir de la fonctionnalité **+ Ajouter un fichier** ou **+ Ajouter une URL** dans la page **Paramètres**. 

## <a name="data-source-locations"></a>Emplacements des sources de données

Les emplacements des sources de données sont des **URL ou fichiers publics**, qui ne requièrent pas d’authentification. 

Si vous avez besoin d’une authentification pour votre source de données, envisagez les méthodes suivantes pour obtenir ces données dans QnA Maker :

* [Télécharger le fichier manuellement](#download-file-from-authenticated-data-source-location) et l’importer dans QnA Maker
* Importer un fichier pour l’[emplacement SharePoint](#import-file-from-authenticated-sharepoint) authentifié 

### <a name="download-file-from-authenticated-data-source-location"></a>Télécharger le fichier à partir d’un emplacement de source de données authentifié

Si vous avez un fichier authentifié (hors d’un emplacement SharePoint authentifié) ou une URL authentifiée, une autre option consiste à télécharger le fichier sur votre ordinateur local à partir du site authentifié, puis à ajouter le fichier à la base de connaissances à partir de votre ordinateur local.

### <a name="import-file-from-authenticated-sharepoint"></a>Importer un fichier d’un emplacement SharePoint authentifié 

Les [emplacements de sources de données SharePoint](../How-to/add-sharepoint-datasources.md) sont autorisés à fournir des **fichiers** authentifiés. Les ressources SharePoint doivent être des fichiers, pas des pages web. Si l’URL se termine par une extension web, telle que **.ASPX**, elle n’est pas importée dans QnA Maker à partir de SharePoint.


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

## <a name="structured-data-format-through-import"></a>Format de données structurées via l’importation

L’importation d’une base de connaissances remplace le contenu de la base de connaissances existante. L’importation nécessite un fichier .tsv structuré qui contient des informations sur la source de données. Ces informations aident QnA Maker à regrouper les paires de question-réponse et à les attribuer à une source de données en particulier.

| Question  | Réponse  | Source| Métadonnées (1 clé : 1 valeur) |          
|-----------|---------|----|---------------------|
| Question1 | Réponse1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 | Éditorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Ajouter à la base de connaissances de manière éditoriale

Si vous ne possédez aucun contenu pour alimenter la base de connaissances, vous pouvez ajouter du contenu QnA éditorial dans la base de connaissances QnA Maker. Découvrez comment mettre à jour votre base de connaissances [ici](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Mise en forme - Éléments à prendre en compte

Après l’importation d’un fichier ou d’une URL, QnA Maker convertit et stocke votre contenu dans le [format Markdown](https://en.wikipedia.org/wiki/Markdown). Le processus de conversion ajoute de nouvelles lignes dans le texte, telles que `\n\n`. Une connaissance du format Markdown vous aide à comprendre le contenu converti et à gérer le contenu de votre base de connaissances. 

Si vous ajoutez ou modifiez votre contenu directement dans votre base de connaissances, utilisez la **mise en forme Markdown** pour créer un contenu de texte enrichi ou modifiez le contenu du format Markdown qui est déjà dans la réponse. QnA Maker prend en charge une grande partie du format Markdown pour offrir des fonctionnalités de texte enrichi à votre contenu. Toutefois, l’application cliente, telle qu’un bot de chat, peut ne pas prendre en charge le même ensemble de formats Markdown. Il est important de tester l’affichage des réponses de l’application cliente. 

Voici la liste des formats Markdown que vous pouvez utiliser dans QnA Maker : 

|Objectif|Format|Exemple de Markdown|Rendu<br>tel qu’il est affiché dans le bot de chat|
|--|--|--|--|
Nouvelle ligne entre 2 phrases.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![nouvelle ligne entre 2 phrases](../media/qnamaker-concepts-datasources/format-newline.png)|
|En-têtes de H1 à H6. Le nombre de `#` dénote de quel en-tête il s’agit. 1 `#` correspond à H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![format avec en-têtes Markdown](../media/qnamaker-concepts-datasources/format-headers.png)<br>![format avec en-têtes Markdown H1 à H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Italique |`*text*`|`How do I create a bot with *QnA Maker*?`|![format en italique](../media/qnamaker-concepts-datasources/format-italics.png)|
|Chaîne (gras)|`**text**`|`How do I create a bot with **QnA Maker**?`|![format avec un marquage renforcé pour le gras](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL du lien|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![format pour l’URL (lien hypertexte)](../media/qnamaker-concepts-datasources/format-url.png)|
|*URL pour l’image publique|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![format de l’URL de l’image publique ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Barré|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![format barré](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Gras et italique|`***text***`|`How can I create a ***QnA Maker*** bot?`|![format pour le gras et l’italique](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL en gras pour le lien|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![format de l’URL en gras](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL en italiques pour le lien|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![format de l’URL en italique](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Ajouter un caractère d’échappement devant les symboles Markdown|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![format de l’URL en italique](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Liste triée|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>L’exemple précédent utilise la numérotation automatique intégrée au Markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>L’exemple précédent utilise la numérotation explicite.|![format de la liste triée](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Liste non triée|`\n * item1 \n * item2`<br>or<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![format de la liste triée](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listes imbriquées|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Vous pouvez imbriquer des listes triées et non triées ensemble. L’onglet, `\t`, indique le niveau de mise en retrait de l’élément enfant.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![format de la liste non triée imbriquée](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![format de la liste triée imbriquée](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker ne traite pas du tout l’image. C’est le rôle de l’application cliente d’effectuer le rendu de l’image. 

Si vous souhaitez ajouter du contenu à l’aide des API de mise à jour ou de remplacement de la base de connaissances, et si le contenu ou le fichier contiennent des balises HTML, vous pouvez conserver le code HTML de votre fichier en veillant à ce que l’ouverture et la fermeture des balises soient converties au format encodé.

| Conserver le code HTML  | Représentation de la requête d’API  | Représentation dans la base de connaissances |
|-----------|---------|-------------------------|
| OUI | \&lt;br\&gt; | &lt;br&gt; |
| OUI | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;header&lt;/h3&gt; |

En outre, CR LF (\r\n) est converti en \n dans la base de connaissances. LF (\n) est conservé tel quel. Si vous souhaitez échapper une séquence d’échappement telle que \t ou \n, vous pouvez utiliser la barre oblique inverse, par exemple : « \\\\r\\\\n » et « \\\\t ».

## <a name="editing-your-knowledge-base-locally"></a>Modification de votre base de connaissances en local

Une fois qu’une base de connaissances est créée, il est recommandé d’apporter des modifications au texte associé dans le [portail QnA Maker](https://qnamaker.ai), plutôt que d’exporter et de réimporter via des fichiers locaux. Toutefois, vous pouvez être amené à modifier une base de connaissances localement. 

Exportez la base de connaissances à partir de la page **Paramètres**, puis modifiez la base de connaissances avec Microsoft Excel. Si vous choisissez d’utiliser une autre application pour modifier votre fichier TSV exporté, celle-ci peut introduire des erreurs de syntaxe, car elle ne prend pas totalement en charge le format TSV. Les fichiers TSV de Microsoft Excel n’entraînent généralement pas d’erreurs de mise en forme. 

Une fois que vous avez terminé vos modifications, réimportez le fichier TSV à partir de la page **Paramètres**. Cela remplace complètement la base de connaissances actuelle par la base de connaissances importée. 

## <a name="testing-your-markdown"></a>Test de votre Markdown

Utilisez le tutoriel **[CommonMark](https://commonmark.org/help/tutorial/index.html)** pour valider votre Markdown. Ce tutoriel a une fonctionnalité **Essayer** pour la validation rapide de copier/coller. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>Contrôle de version des données de votre base de connaissances

Le contrôle de version des données s’effectue via la [fonctionnalité d’importation/exportation](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) sur la page **Paramètres**. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer un service QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
