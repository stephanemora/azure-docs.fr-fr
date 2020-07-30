---
title: Types de contenu - QnA Maker
description: Les types de contenu incluent bon nombre de documents structurés standard tels que PDF, DOC et TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 70640246b15e7f68704657116724284498f62684
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524452"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Types de contenu de documents que vous pouvez ajouter à une base de connaissances
Les types de contenu incluent bon nombre de documents structurés standard tels que PDF, DOC et TXT.

## <a name="file-and-url-data-types"></a>Types de données de fichier et d’URL

Le tableau ci-dessous récapitule les types de contenu et formats de fichiers pris en charge par QnA Maker.

|Type de source|Type de contenu| Exemples|
|--|--|--|
|URL|FAQ<br> (plates, avec des sections ou une page d’accueil de rubriques)<br>Pages de support <br> (Articles sur les procédures d’une seule page, articles sur la résolution des problèmes, etc.)|[FAQ brut](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Forum aux questions avec des liens](https://www.microsoft.com/en-us/software-download/faq),<br> [Forum aux questions avec une page d’accueil contenant des rubriques](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Article de support technique](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|FAQs,<br> Manuel de produit,<br> Brochures,<br> Article,<br> Stratégie de prospectus,<br> Guide de support,<br> Questions et réponses structurées,<br> , etc.|**Sans fonctionnalité multi-tour**<br>[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Fonctionnalité multi-tour** :<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Fichier QnA structuré<br> (y compris la prise en charge de RTF et HTML)|**Sans fonctionnalité multi-tour** :<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Fonctionnalité multi-tour** :<br>[Structured simple FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface laptop FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
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

En savoir plus sur les [directives relatives au format](../reference-document-format-guidelines.md) des différents fichiers.

## <a name="next-steps"></a>Étapes suivantes

Comprendre quelles informations sont stockées dans une paire [question/réponse (Q/R)](question-answer-set.md).