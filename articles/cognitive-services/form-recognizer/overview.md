---
title: Qu’est-ce que Form Recognizer ?
titleSuffix: Azure Cognitive Services
description: Le service Azure Form Recognizer vous permet d’identifier et d’extraire des paires clé/valeur et des données de table de vos formulaires, ainsi que d’extraire des informations importantes des tickets de caisse et des cartes de visite.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: traitement de données automatisé, traitement de documents, entrée de données automatisée, traitement des formulaires
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318957"
---
# <a name="what-is-form-recognizer"></a>Qu’est-ce que Form Recognizer ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer est un service cognitif qui vous permet de créer des logiciels de traitement de données automatisé à l’aide des technologies du Machine Learning. Identifiez et extrayez du texte, des paires clé/valeur et des données de table de vos formulaires : le service génère des données structurées qui incluent les relations dans le fichier d’origine. Vous pouvez obtenir des résultats rapidement, avec précision et en adéquation avec votre contenu particulier, sans la nécessité d’une intervention manuelle lourde ou de compétences approfondies en science des données. Utilisez Form Recognizer pour automatiser l’entrée de données dans vos applications.

Form Recognizer est constitué de modèles de traitement de documents personnalisés, de modèles de carte de visite et de reçus prédéfinis, ainsi que de l’API de disposition. Vous pouvez appeler les modèles Form Recognizer au moyen d’une API REST ou de SDK de bibliothèque de client, afin d’en réduire la complexité et de l’intégrer à votre workflow ou application.

Form Recognizer est constitué des services suivants :
* **[Modèles personnalisés](#custom-models)**  : extrayez des paires clé/valeur et des données de table de vos formulaires. Ces modèles étant entraînés avec vos propres données, ils sont adaptés à vos formulaires.
* **[Modèles prédéfinis](#prebuilt-models)**  : extrayez les données de types de formulaires uniques à l’aide de modèles prédéfinis. Les modèles prédéfinis actuellement disponibles s’appliquent aux tickets de caisse et cartes de visite rédigés en anglais.
* **[API de disposition](#layout-api)**  : extrayez les structures du texte et des tables, ainsi que les coordonnées de leur cadre englobant, à partir de documents.

## <a name="custom-models"></a>Modèles personnalisés

Les modèles personnalisés Form Recognizer effectuent l’entraînement avec vos propres données, et il vous suffit de cinq exemples de formulaires d’entrée pour démarrer. Un modèle de traitement de documents entraîné peut restituer des données structurées qui incluent les relations du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

Quand vous entraînez des modèles personnalisés, vous disposez des options suivantes : entraînement avec des données étiquetées et sans données étiquetées.

### <a name="train-without-labels"></a>Effectuer l’entraînement sans étiquettes

Par défaut, Form Recognizer utilise l’apprentissage non supervisé pour comprendre la disposition et les relations entre les champs et les entrées de vos formulaires. Quand vous soumettez vos formulaires d’entrée, l’algorithme groupe les formulaires par types, découvre les clés et les tableaux présents, et associe les valeurs aux clés et les entrées aux tableaux. Cette méthode ne nécessitant pas d’étiquetage manuel des données ni de programmation et de maintenance intensives, nous vous recommandons de l’essayer en premier.

Consultez [Créer un jeu de données d’entraînement](./build-training-data-set.md) pour obtenir des conseils sur la collecte de vos documents d’entraînement.

### <a name="train-with-labels"></a>Effectuer l’entraînement avec des étiquettes

Quand vous effectuez l’entraînement avec des données étiquetées, le modèle supervise l’apprentissage afin d’extraire les valeurs dignes d’intérêt, à l’aide des formulaires étiquetés que vous fournissez. Cette approche aboutit à des modèles plus performants et peut engendrer des modèles qui fonctionnent avec des formulaires complexes ou des formulaires contenant des valeurs sans clés.

Form Recognizer utilise l’[API de disposition](#layout-api) pour connaître les tailles et les positions attendues des éléments de texte imprimés et manuscrits. Ensuite, il utilise des étiquettes spécifiées par l’utilisateur pour connaître les associations clé/valeur dans les documents. Nous vous recommandons d’utiliser cinq formulaires étiquetés manuellement du même type pour démarrer l’entraînement d’un nouveau modèle et d’ajouter des données étiquetées en fonction des besoins afin d’améliorer l’exactitude du modèle.

## <a name="prebuilt-models"></a>Modèles prédéfinis

Form Recognizer comprend aussi des modèles prédéfinis pour le traitement de données automatisé des types de formulaires uniques.

### <a name="prebuilt-receipt-model"></a>Modèle de ticket de caisse prédéfini

Le modèle Prebuilt Receipt (Ticket de caisse prédéfini) permet de lire les tickets de caisse en anglais émis en Australie, au Canada, en Grande-Bretagne, en Inde et aux États-Unis&mdash;du type utilisé dans les restaurants, les stations-service, les commerces, etc. Ce modèle extrait des informations clés telles que la date et l’heure de la transaction, les détails du commerçant, le montant des taxes, les articles, les totaux, etc. En outre, le modèle de reçu préconstruit est entraîné pour reconnaître et retourner tout le texte d’un reçu. Pour plus d’informations, consultez le guide conceptuel des [reçus](./concept-receipts.md).

![exemple de ticket de caisse](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Modèle de cartes de visite prédéfini

Le modèle Business Cards (Cartes de visite) vous permet d’extraire des informations d’une carte de visite rédigée en anglais telles que le nom, la fonction, l’adresse postale, l’adresse e-mail, la société et les numéros de téléphone de la personne. Pour plus d’informations, consultez le guide conceptuel des [cartes de visite](./concept-business-cards.md).

![exemple de carte de visite](./media/business-card-english.jpg)

## <a name="layout-api"></a>API de disposition

Form Recognizer peut également extraire la structure du texte et des tableaux (les numéros de ligne et de colonne associés au texte) à l’aide de la reconnaissance optique de caractères (OCR) haute définition.

## <a name="get-started"></a>Bien démarrer

Suivez un guide de démarrage rapide pour commencer à extraire des données de vos formulaires. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

* [Démarrages rapides sur la bibliothèque de client](./quickstarts/client-library.md) (tous les langages, plusieurs scénarios)
* Démarrages rapides sur l’interface utilisateur web
  * [Entraînement avec des étiquettes - Outil Exemple d’étiquetage](quickstarts/label-tool.md)
* Démarrages rapides REST
  * Entraîner des modèles personnalisés et extraire des données de formulaire
    * [Effectuer l’entraînement sans étiquettes - cURL](quickstarts/curl-train-extract.md)
    * [Effectuer l’entraînement sans étiquettes - Python](quickstarts/python-train-extract.md)
    * [Effectuer l’entraînement avec des étiquettes - Python](quickstarts/python-labeled-data.md)
  * Extraire les données des reçus de ventes
    * [Extraire des données de reçu - cURL](quickstarts/curl-receipts.md)
    * [Extraire des données de reçu - Python](quickstarts/python-receipts.md)
  * Extraire des données à partir de cartes de visite
    * [Extraire des données de cartes de visite - Python](quickstarts/python-business-cards.md)
  * Extraire la structure du texte et des tableaux à partir de formulaires
    * [Extraire des données de disposition - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Passer en revue les API REST

Vous allez utiliser les API suivantes pour entraîner des modèles et extraire des données structurées à partir de formulaires.

|Nom |Description |
|---|---|
| **Entraîner un modèle personnalisé**| Entraîner un nouveau modèle pour analyser vos formulaires avec 5 formulaires du même type. Définissez le paramètre _useLabelFile_ sur `true` pour effectuer l’entraînement avec des données étiquetées manuellement. |
| **Analyser le formulaire** |Analysez un seul document transmis en tant que flux de données pour extraire du formulaire le texte, les paires clé/valeur et les tableaux avec votre modèle personnalisé.  |
| **Analyser le reçu** |Analysez un document de reçu unique pour extraire les informations clés et les autres éléments textuels du reçu.|
| **Analyser la carte de visite** |Analysez une carte de visite pour extraire du texte et des informations clés.|
| **Analyser la disposition** |Analysez la disposition d’un formulaire pour extraire la structure du texte et des tableaux.|

Explorez la [documentation de référence sur l’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) pour en savoir plus. Si vous êtes familiarisé avec une version précédente de l’API, consultez l’article [Nouveautés](./whats-new.md) pour en savoir plus sur les modifications récentes.

## <a name="input-requirements"></a>Critères des entrées

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme c’est le cas pour tous les services Cognitive Services, les développeurs utilisant le service Form Recognizer doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un [guide de démarrage rapide sur la bibliothèque de client](quickstarts/client-library.md) pour bien démarrer avec l’écriture d’une application de traitement de formulaires avec Form Recognizer dans le langage de votre choix.