---
title: Qu’est-ce que la traduction de documents ?
description: Une vue d’ensemble du processus et du service de traduction de documents en lot basés sur le cloud.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 06/20/2021
ms.openlocfilehash: 267109bcacd8fcb9ff7d44ca6f9a74ca4dc39fda
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112582939"
---
# <a name="what-is-document-translation"></a>Qu’est-ce que la traduction de documents ?

Document Translation est une fonctionnalité basée sur le cloud du service [Azure Translator](../translator-info-overview.md) qui fait partie de la famille Azure Cognitive Service des API REST. L’API de Document Translation traduit les documents dans la totalité des [langues et dialectes pris en charge](../../language-support.md) tout en préservant la structure des documents et le format des données. 

Cette documentation contient les types d’articles suivants :  

* Les [**Démarrages rapides**](get-started-with-document-translation.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](create-sas-tokens.md) contiennent des instructions sur l’utilisation de la fonctionnalité de manière plus spécifique ou personnalisée. 
* Les [**informations de référence**](reference/rest-api-guide.md) fournissent les paramètres, les valeurs, les mots clés et la configuration de l’API REST.

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
## <a name="how-to-get-started"></a>Commencer

Dans notre guide pratique, vous apprendrez comment commencer à utiliser Document Translator. Pour commencer, vous avez besoin d’un [compte Azure](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Démarrer](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Formats de document pris en charge

Les types de fichiers de document suivants sont pris en charge par Document Translation :

| Type de fichier| Extension de fichier|Description|
|---|---|--|
|Adobe PDF|.pdf|Format de document portable Adobe Acrobat|
|Valeurs séparées par des virgules |.csv| Fichier de données brutes délimité par des virgules utilisé par les programmes de feuille de calcul.|
|HTML|.html, .htm|Hyper Text Markup Language.|
|Format du fichier d’échange de localisation|.xlf. , xliff| Un format de document parallèles, pour les systèmes d’exportation de mémoires de traductions. Les langues utilisées sont définies dans le fichier.|
|Markdown| .markdown, .mdown, .mkdn, .md, .mkd, .mdwn, .mdtxt, .mdtext, .rmd| Langage de balisage léger permettant de créer du texte mis en forme.|
|MHTML|.mthml, .mht| Format d’archive de page web utilisé pour combiner du code HTML et ses ressources associées.|
|Microsoft Excel|.xls, .xlsx|Fichier de feuille de calcul pour l’analyse et la documentation des données.|
|Microsoft Outlook|.msg|E-mail créé ou enregistré dans Microsoft Outlook.|
|Microsoft PowerPoint|.ppt, .pptx| Fichier de présentation utilisé pour afficher le contenu dans un format de diaporama.|
|Microsoft Word|.doc, .docx| Fichier de document texte.|
|Texte OpenDocument|.odt|Fichier de document texte open source.|
|Présentation OpenDocument|.odp|Fichier de présentation open source.|
|Feuille de calcul OpenDocument|.ods|Fichier de feuille de calcul open source.|
|Format de texte enrichi|.rtf|Document texte contenant une mise en forme.|
|Valeurs séparées par des tabulations/TAB|.tsv/.tab| Fichier de données brutes délimité par des tabulations utilisé par les programmes de feuille de calcul.|
|Texte|.txt| Document texte non mis en forme.|

## <a name="supported-glossary-formats"></a>Formats de glossaire pris en charge

Les types de fichiers suivants sont pris en charge par Document Translation :

| Type de fichier| Extension de fichier|Description|
|---|---|--|
|Valeurs séparées par des virgules| .csv |Fichier de données brutes délimité par des virgules utilisé par les programmes de feuille de calcul.|
|Format du fichier d’échange de localisation|.xlf. , xliff| Un format de document parallèles, pour les systèmes d’exportation de mémoires de traductions. Les langues utilisées sont définies dans le fichier.|
|Valeurs séparées par des tabulations/TAB|.tsv, .tab| Fichier de données brutes délimité par des tabulations utilisé par les programmes de feuille de calcul.|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec Document Translation](get-started-with-document-translation.md)
