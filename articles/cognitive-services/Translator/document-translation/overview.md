---
title: Qu’est-ce que la Traduction de documentation Microsoft Azure Cognitive Services ?
description: Une vue d’ensemble du processus et du service de traduction de documents en lot basés sur le cloud.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 08/09/2021
ms.openlocfilehash: 64198fce7acb3ea02349978de0c2787cb744e6b4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433887"
---
# <a name="what-is-document-translation"></a>Qu’est-ce que la traduction de documents ?

Document Translation est une fonctionnalité basée sur le cloud du service [Azure Translator](../translator-overview.md) qui fait partie de la famille Azure Cognitive Service des API REST. Dans cette vue d’ensemble, vous allez apprendre comment l’API de traduction de documentation peut être utilisée pour traduire des documents multiples et complexes sur l’ensemble des [langages et des dialectes pris en charge](../../language-support.md) tout en conservant la structure de documents et le format de données d’origine.

Cette documentation contient les types d’articles suivants :

* Les [**Démarrages rapides**](get-started-with-document-translation.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](create-sas-tokens.md) contiennent des instructions sur l’utilisation de la fonctionnalité de manière plus spécifique ou personnalisée.
* Les [**référence**](reference/rest-api-guide.md) fournissent les paramètres, les valeurs, les mots clés et la configuration de l’API REST.

## <a name="document-translation-key-features"></a>Fonctionnalités clés de Document Translation

| Fonctionnalité | Description |
| ---------| -------------|
| **Traduire des fichiers volumineux**| Traduisez des documents entiers de manière asynchrone.|
|**Traduire de nombreux fichiers**|Traduisez plusieurs fichiers dans la totalité des langues et dialectes pris en charge tout en préservant la structure des documents et le format des données.|
|**Conserver la présentation du fichier source**| Traduisez les fichiers tout en conservant la disposition et le format d’origine.|
|**Personnaliser la traduction**| Traduisez des documents à l’aide de modèles de traduction généraux et [personnalisés](../customization.md#custom-translator).|
|**Appliquer des glossaires personnalisés**|Traduisez des documents à l’aide de glossaires personnalisés.|
|**Détecter automatiquement la langue du document**|Laissez le service de traduction de documentation déterminer la langue du document.|
|**Traduire des documents présentant un contenu multilingue**|Utilisez la fonctionnalité de détection automatique pour traduire dans votre langue cible des documents présentant un contenu multilingue.|

> [!NOTE]
> Lors de la traduction de documents présentant un contenu multilingue, la fonctionnalité est conçue pour traduire des phrases complètes dans une seule langue. Si les phrases sont composées de plusieurs langues, le contenu peut ne pas être traduit entièrement dans la langue cible.
>

## <a name="document-translation-development-options"></a>Options de développement de la traduction de documentation

Vous pouvez ajouter une traduction de documentation à vos applications à l’aide de l’API REST ou d’un kit de développement logiciel (SDK) de bibliothèque cliente :

* L’[**API REST**](reference/rest-api-guide.md). est une interface indépendante du langage qui vous permet de créer des requêtes HTTP et des en-têtes d’autorisation pour traduire des documents.

* Les [**Kits de développement logiciel (SDK) de la bibliothèque cliente**](client-sdks.md) sont des classes, des objets, des méthodes et du code spécifiques au langage que vous pouvez utiliser rapidement en ajoutant une référence dans votre projet. Actuellement, la traduction de documentation propose un support du langage de programmation pour [**C#/.NET**](/dotnet/api/azure.ai.translation.document) et [**Python**](https://pypi.org/project/azure-ai-translation-document/).

## <a name="get-started"></a>Bien démarrer

Dans notre guide pratique, vous apprendrez comment commencer à utiliser Document Translator. Pour commencer, vous avez besoin d’un [compte Azure](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Commencez ici](get-started-with-document-translation.md "Découvrez comment utiliser la traduction de documentation avec HTTP REST")

## <a name="supported-document-formats"></a>Formats de document pris en charge

Les types de fichiers de document suivants sont pris en charge par Document Translation :

| Type de fichier| Extension de fichier|Description|
|---|---|--|
|Adobe PDF|pdf|Format de document portable Adobe Acrobat|
|Valeurs séparées par des virgules |csv| Fichier de données brutes délimité par des virgules utilisé par les programmes de feuille de calcul.|
|HTML|html, htm|Hyper Text Markup Language.|
|Format du fichier d’échange de localisation|xlf. , xliff| Un format de document parallèles, pour les systèmes d’exportation de mémoires de traductions. Les langues utilisées sont définies dans le fichier.|
|Markdown| markdown, mdown, mkdn, md, mkd, mdwn, mdtxt, mdtext, rmd| Langage de balisage léger permettant de créer du texte mis en forme.|
|MHTML|mthml, mht| Format d’archive de page web utilisé pour combiner du code HTML et ses ressources associées.|
|Microsoft Excel|xls, xlsx|Fichier de feuille de calcul pour l’analyse et la documentation des données.|
|Microsoft Outlook|msg|E-mail créé ou enregistré dans Microsoft Outlook.|
|Microsoft PowerPoint|ppt, pptx| Fichier de présentation utilisé pour afficher le contenu dans un format de diaporama.|
|Microsoft Word|doc, docx| Fichier de document texte.|
|Texte OpenDocument|odt|Fichier de document texte open source.|
|Présentation OpenDocument|odp|Fichier de présentation open source.|
|Feuille de calcul OpenDocument|ods|Fichier de feuille de calcul open source.|
|Format de texte enrichi|rtf|Document texte contenant une mise en forme.|
|Valeurs séparées par des tabulations/TAB|tsv/tab| Fichier de données brutes délimité par des tabulations utilisé par les programmes de feuille de calcul.|
|Texte|txt| Document texte non mis en forme.|

## <a name="supported-glossary-formats"></a>Formats de glossaire pris en charge

Les types de fichiers suivants sont pris en charge par Document Translation :

| Type de fichier| Extension de fichier|Description|
|---|---|--|
|Valeurs séparées par des virgules| csv |Fichier de données brutes délimité par des virgules utilisé par les programmes de feuille de calcul.|
|Format du fichier d’échange de localisation| xlf , xliff| Un format de document parallèle, exportation des systèmes de mémoire de traduction utilisés par les langages sont définis dans le fichier.|
|Valeurs séparées par des tabulations/TAB|tsv, tab| Fichier de données brutes délimité par des tabulations utilisé par les programmes de feuille de calcul.|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec Document Translation](get-started-with-document-translation.md)
