---
title: Instructions relatives au format des documents importés – QnA Maker
description: Suivez ces instructions pour l’importation de documents afin d’obtenir les meilleurs résultats pour votre contenu.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: e45720543a5fc5f3293a2aae0c2740af1048384a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110465226"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Instructions relatives au format pour les documents et les URL importés

Passez en revue ces instructions de mise en forme afin d’obtenir les meilleurs résultats pour votre contenu.

## <a name="formatting-considerations"></a>Mise en forme - Éléments à prendre en compte

Après l’importation d’un fichier ou d’une URL, QnA Maker convertit et stocke votre contenu dans le [format Markdown](https://en.wikipedia.org/wiki/Markdown). Le processus de conversion ajoute de nouvelles lignes dans le texte, telles que `\n\n`. Une connaissance du format Markdown vous aide à comprendre le contenu converti et à gérer le contenu de votre base de connaissances.

Si vous ajoutez ou modifiez votre contenu directement dans votre base de connaissances, utilisez la **mise en forme Markdown** pour créer un contenu de texte enrichi ou modifiez le contenu du format Markdown qui est déjà dans la réponse. QnA Maker prend en charge une grande partie du format Markdown pour offrir des fonctionnalités de texte enrichi à votre contenu. Toutefois, l’application cliente, telle qu’un bot de chat, peut ne pas prendre en charge le même ensemble de formats Markdown. Il est important de tester l’affichage des réponses de l’application cliente.

Consultez la liste complète des [types et exemples de contenu](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).

## <a name="basic-document-formatting"></a>Mise en forme de base du document

QnA Maker identifie les sections, sous-sections et relations dans le fichier en fonction d’indices visuels, notamment :

* la taille de police
* le style de police
* la numérotation
* les couleurs

> [!NOTE]
> Actuellement, nous ne prenons pas en charge l’extraction d’images à partir de documents chargés.

### <a name="product-manuals"></a>Manuels de produit

Un manuel comprend habituellement des supports d’orientation qui accompagnent un produit. Il aide l’utilisateur à configurer, utiliser, gérer et dépanner le produit. Lorsque QnA Maker traite un manuel, il extrait les titres et les sous-titres sous forme de questions et le contenu qui suit sous forme de réponses. Un exemple vous est proposé [ici](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Voici un exemple de manuel avec une page d’index et du contenu hiérarchique

> [!div class="mx-imgBorder"]
> ![Exemple de manuel de produit pour une base de connaissances](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> L’extraction est plus efficace sur les manuels qui ont une table des matières et/ou une page d’index, mais aussi une structure clairement définie avec des en-têtes hiérarchiques.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, instructions, livres blancs et autres fichiers

De nombreux autres types de documents peuvent également être traités pour générer des paires de questions-réponses, à condition qu’ils aient une structure et une disposition claires. notamment : brochures, instructions, rapports, livres blancs, articles scientifiques, stratégies, ouvrages, etc. Un exemple vous est proposé [ici](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Voici un exemple de document semi-structuré, sans index :

> [!div class="mx-imgBorder"]
> ![Document semi-structuré de Stockage d’objets blob Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="unstructured-document-support"></a>Prise en charge des documents non structurés

Réponses aux questions personnalisées prend maintenant en charge les documents non structurés. Un document dont le contenu n’est pas organisé d’une manière hiérarchique bien définie, n’a pas de structure définie ou est libre de toute mise en forme peut être considéré comme un document non structuré.

Voici ci-dessous un exemple de document PDF non structuré :

> [!div class="mx-imgBorder"]
> ![Exemple de document non structuré pour une base de connaissances](./media/qnamaker-concepts-datasources/unstructured-qna-pdf.png)

 Actuellement, cette fonctionnalité est disponible uniquement via le chargement de documents et pour les formats de fichier PDF et DOC.

> [!IMPORTANT]
> La prise en charge des fichiers/contenus non structurés est disponible uniquement dans Réponses aux questions personnalisées (préversion)

### <a name="structured-qna-document"></a>Document QnA structuré

Le format des questions-réponses structurées dans les fichiers DOC consiste à alterner les questions et les réponses ligne par ligne, une question sur une ligne suivie de sa réponse sur la ligne suivante, comme indiqué ci-dessous :

```text
Question1

Answer1

Question2

Answer2
```

Voici un exemple de document Word QnA structuré :

> [!div class="mx-imgBorder"]
> ![Exemple de document QnA structuré pour une base de connaissances](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Fichiers *TXT*, *TSV* et *XLS* structurés

Les QnA sous forme de fichiers *.txt*, *.tsv* ou *.xls* structurés peuvent également être chargés vers QnA Maker pour créer ou augmenter une base de connaissances.  Il peuvent être en texte brut, ou peuvent avoir du contenu au format RTF ou HTML. Les [paires QnA](/how-to/edit-knowledge-base#question-and-answer-pairs) ont un champ de métadonnées facultatif qui peut être utilisé pour regrouper des paires QnA en catégories.

| Question  | Réponse  | Métadonnées (1 clé : 1 valeur) |
|-----------|---------|-------------------------|
| Question1 | Réponse1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 |      `Key:Value`           |

Toutes les autres colonnes du fichier source sont ignorées.

#### <a name="example-of-structured-excel-file"></a>Exemple de fichier Excel structuré

Voici un exemple de fichier *.xls* QnA structuré, avec du contenu HTML :

> [!div class="mx-imgBorder"]
> ![Exemple de feuille Excel QnA structurée pour une base de connaissances](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemple de questions multiples avec réponse unique dans un fichier Excel

Voici un exemple de fichier *.xls* QnA structuré, avec plusieurs questions pour une réponse unique :

> [!div class="mx-imgBorder"]
> ![Exemple de questions multiples avec réponse unique dans un fichier Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Une fois le fichier importé, la paire question-réponse figure dans la base de connaissances, comme indiqué ci-dessous :

> [!div class="mx-imgBorder"]
> ![Capture d’écran de questions multiples avec réponse unique importées dans la base de connaissances](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Format de données structurées via l’importation

L’importation d’une base de connaissances remplace le contenu de la base de connaissances existante. L’importation nécessite un fichier .tsv structuré qui contient des informations sur la source de données. Ces informations aident QnA Maker à regrouper les paires de question-réponse et à les attribuer à une source de données en particulier. Les [paires QnA](/how-to/edit-knowledge-base#question-and-answer-pairs) ont un champ de métadonnées facultatif qui peut être utilisé pour regrouper des paires QnA en catégories.

| Question  | Réponse  | Source| Métadonnées (1 clé : 1 valeur) |
|-----------|---------|----|---------------------|
| Question1 | Réponse1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Réponse2 | Éditorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Mise en forme multitour du document

* Utilisez des titres et sous-titres pour montrer la hiérarchie. Par exemple, vous pouvez H1 pour dénoter la QnA parente et H2 pour dénoter la QnA à considérer comme invite. Utilisez une petite taille de titre pour dénoter la hiérarchie suivante. N’utilisez pas de style, de couleur ou d’autre mécanisme pour montrer la structure de votre document. QnA Maker n’extrait pas les invites multitours.
* Le premier caractère de l’en-tête doit être en majuscule.
* Ne pas terminer un titre par un point d’interrogation (`?`).

**Exemples de documents** :<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>URL de FAQ

QnA Maker peut prendre en charge 3 différentes formes de pages web de questions fréquentes (FAQ), à savoir :

* Pages de FAQ simples
* Pages de FAQ avec des liens
* Pages de FAQ avec une page d’accueil Rubriques

### <a name="plain-faq-pages"></a>Pages de FAQ simples

Il s’agit du type le plus courant de page de FAQ, dans lequel les réponses suivent immédiatement les questions dans la même page.

Voici un exemple d’une page de FAQ simple :

> [!div class="mx-imgBorder"]
> ![Exemple de page de FAQ simple pour une base de connaissances](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Pages de FAQ avec des liens

Dans ce type de page de FAQ, les questions sont regroupées puis associées à des réponses qui se trouvent soit dans différentes sections de la même page, soit dans différentes pages.

Voici un exemple de page de FAQ avec des liens dans des sections qui se trouvent dans la même page :

> [!div class="mx-imgBorder"]
> ![Exemple de page de FAQ avec liens de sections pour une base de connaissances](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Liens de la page Rubriques parente vers les pages de réponses enfants

Ce type de FAQ comporte une page Rubriques sur laquelle chaque rubrique est liée à un ensemble de questions et de réponses correspondant sur une autre page. QnA Maker analyse toutes les pages associées pour extraire les questions et réponses correspondantes.

Voici un exemple de page Rubriques comportant des liens vers des sections de FAQ sur d’autres pages.

> [!div class="mx-imgBorder"]
> ![Exemple de page de FAQ avec liens profonds pour une base de connaissances](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>URL de support technique

QnA Maker peut traiter des pages web de support semi-structurées, telles que des articles web décrivant comment effectuer une tâche donnée, comment diagnostiquer et résoudre un problème donné, ainsi quelles sont les meilleures pratiques pour un processus donné. L’extraction fonctionne de façon optimale sur du contenu ayant une structure claire avec des titres hiérarchiques.

> [!NOTE]
> L’extraction pour les articles de support est une nouvelle fonctionnalité à un stade de développement encore précoce. Il convient idéalement pour les pages simples, qui sont bien structurées et qui ne contiennent pas d’en-têtes ou pieds de page complexes.

> [!div class="mx-imgBorder"]
> ![QnA Maker prend en charge l’extraction à partir de pages web semi-structurées où une structure claire est présentée avec des titres hiérarchiques](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Importer et exporter une base de connaissances

L’utilisation de **fichiers TSV et XLS** provenant de bases de connaissances exportées n’est possible qu’en les important à partir de la page **Paramètres** du portail QnA Maker. Il est impossible de les utiliser en tant que sources de données lors de la création de la base de connaissances ou à partir de la fonctionnalité **+ Ajouter un fichier** ou **+ Ajouter une URL** dans la page **Paramètres**. 

Lorsque vous importez la base de connaissances via ces **fichiers TSV et XLS**, les paires question-réponse sont ajoutées à la source éditoriale et non aux sources à partir desquelles les questions et réponses ont été extraites dans la base de connaissances exportée. 


## <a name="next-steps"></a>Étapes suivantes

Consultez la liste complète des [types et exemples de contenu](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)
