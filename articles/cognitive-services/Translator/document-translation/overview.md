---
title: Qu’est-ce que la traduction de documents ?
description: Une vue d’ensemble du processus et du service de traduction de documents en lot basés sur le cloud.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968167"
---
# <a name="what-is-document-translation-preview"></a>Qu’est-ce que Document Translation (préversion) ?

Document Translation est une fonctionnalité basée sur le cloud du service [Azure Translator](../translator-info-overview.md) qui fait partie de la famille Azure Cognitive Service des API REST. L’API de Document Translation traduit les documents vers et à partir de 90 langues et dialectes tout en préservant la structure des documents et le format des données.

Cette documentation contient les types d’articles suivants :  

* Les [**Démarrages rapides**](get-started-with-document-translation.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](create-sas-tokens.md) contiennent des instructions sur l’utilisation de la fonctionnalité de manière plus spécifique ou personnalisée.  

## <a name="document-translation-key-features"></a>Fonctionnalités clés de Document Translation

| Fonctionnalité | Description |
| ---------| -------------|
| **Traduire des fichiers volumineux**| Traduisez des documents entiers de manière asynchrone.|
|**Traduire de nombreux fichiers**|Traduisez plusieurs fichiers vers et à partir de 90 langues et dialectes.|
|**Conserver la présentation du fichier source**| Traduisez les fichiers tout en conservant la disposition et le format d’origine.|
|**Personnaliser la traduction**| Traduisez des documents à l’aide de modèles de traduction généraux et [personnalisés](../customization.md#custom-translator).|
|**Appliquer des glossaires personnalisés**|Traduisez des documents à l’aide de glossaires personnalisés.|

## <a name="how-to-get-started"></a>Commencer

Dans notre guide pratique, vous apprendrez comment commencer à utiliser Document Translator. Pour commencer, vous avez besoin d’un [compte Azure](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Démarrer](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Formats de document pris en charge

Les types de fichiers de document suivants sont pris en charge par Document Translation :

| Type de fichier| Extension de fichier|Description|
|---|---|--|
|Adobe PDF|.pdf|Format de document portable Adobe Acrobat|
|HTML|.html|Hyper Text Markup Language.|
|Format du fichier d’échange de localisation|.xlf. , xliff| Un format de document parallèles, pour les systèmes d’exportation de mémoires de traductions. Les langues utilisées sont définies dans le fichier.|
|Microsoft Excel|.xlsx|Fichier de feuille de calcul pour l’analyse et la documentation des données.|
|Microsoft Outlook|.msg|E-mail créé ou enregistré dans Microsoft Outlook.|
|Microsoft PowerPoint|.pptx| Fichier de présentation utilisé pour afficher le contenu dans un format de diaporama.|
|Microsoft Word|.docx| Fichier de document texte.|
|Valeurs séparées par des tabulations/TAB|.tsv/.tab| Fichier de données brutes délimité par des tabulations utilisé par les programmes de feuille de calcul.|
|Texte|.txt| Document texte non mis en forme.|

## <a name="supported-glossary-formats"></a>Formats de glossaire pris en charge

Les types de fichiers suivants sont pris en charge par Document Translation :

| Type de fichier| Extension de fichier|Description|
|---|---|--|
|Format du fichier d’échange de localisation|.xlf. , xliff| Un format de document parallèles, pour les systèmes d’exportation de mémoires de traductions. Les langues utilisées sont définies dans le fichier.|
|Valeurs séparées par des tabulations/TAB|.tsv/.tab| Fichier de données brutes délimité par des tabulations utilisé par les programmes de feuille de calcul.|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec Document Translation](get-started-with-document-translation.md)
