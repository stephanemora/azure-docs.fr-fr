---
title: Qu’est-ce que Form Recognizer ?
titleSuffix: Azure Cognitive Services
description: Form Recognizer est un service Azure Cognitive Services qui permet d’identifier et d’extraire des paires clé-valeur et des données de table à partir de documents de formulaire.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5b8628a8a235e89614ab87dcc2020915db459f38
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978422"
---
# <a name="what-is-form-recognizer"></a>Qu’est-ce que Form Recognizer ?

Azure Form Recognizer est un service cognitif qui utilise la technologie de machine learning pour identifier et extraire des données de paires clé-valeur et de tables à partir de documents de formulaire. Il restitue ensuite des données structurées qui incluent les relations du fichier d’origine. Vous pouvez appeler votre modèle personnalisé Form Recognizer au moyen d’une API REST simple, afin de réduire la complexité et facilement l’intégrer à votre workflow ou application. Vous n’avez besoin que de cinq documents de formulaire remplis ou de deux formulaires remplis plus un formulaire vide d’un type identique à votre document d’entrée pour commencer. Vous pouvez obtenir des résultats rapidement, avec précision et en adéquation avec votre contenu particulier, sans la nécessité d’une intervention manuelle lourde ou de compétences approfondies en science des données.

## <a name="custom-models"></a>Modèles personnalisés

Le modèle personnalisé Form Recognizer effectue l’apprentissage avec vos propres données, et il vous suffit de cinq exemples de formulaires d’entrée pour démarrer. Lorsque vous soumettez vos données d’entrée, l’algorithme groupe les formulaires par types, détecte les clés et les tables présentes, et associe les valeurs aux clés et les entrées aux tables. Il restitue ensuite des données structurées qui incluent les relations du fichier d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

Un apprentissage non supervisé permet au modèle de comprendre la disposition ainsi que les relations entre les champs et les entrées, sans étiquetage manuel des données ni codage et maintenance intensifs. En revanche, les modèles d’apprentissage automatique préformés nécessitent des données standardisées. Ils sont moins précis avec les documents d’entrée qui s’écartent des formats traditionnels, tels que les formulaires spécifiques à certains secteurs.

## <a name="prebuilt-receipt-model"></a>Modèle de reçu préconstruit

Form Recognizer comprend également un modèle pour la lecture des reçus d’achat. Ce modèle extrait des informations clés telles que la date et l’heure de la transaction, les détails du vendeur, le montant des taxes et frais totaux et plus encore. En outre, le modèle de reçu préconstruit est entraîné pour reconnaître et retourner tout le texte d’un reçu.

## <a name="what-it-includes"></a>Contenu

Form Recognizer est disponible en tant qu’API REST. Vous pouvez créer, effectuer l’apprentissage et noter un modèle personnalisé, ou accéder au modèle préconstruit en appelant ces API. Si vous le souhaitez, vous pouvez entraîner et exécuter des modèles personnalisés dans un conteneur Docker local.

## <a name="input-requirements"></a>Critères des entrées
### <a name="custom-model"></a>Modèle personnalisé

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modèle de reçu préconstruit

Les exigences d’entrée pour le modèle de reçu sont légèrement différentes.

* Le format doit être JPEG, PNG, BMP, PDF (texte ou numérisé) ou TIFF.
* La taille du fichier doit être inférieure à 20 Mo.
* Les dimensions des images doivent être comprises entre 50 x 50 et 10 000 x 10 000 pixels. 
* Les dimensions des PDF ne doivent pas dépasser 17 x 17 pouces, ce qui correspond aux formats de papier Legal ou A3, et plus petits.
* Pour PDF et TIFF, seules les 200 premières pages sont traitées (avec un abonnement gratuit, seules les deux premières pages sont traitées).

## <a name="request-access"></a>Demander l'accès

Form Recognizer est disponible en préversion à accès limité. Pour accéder à la préversion, remplissez et envoyez le formulaire de [demande d’accès Form Recognizer](https://aka.ms/FormRecognizerRequestAccess). Des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser Form Recognizer vous sont demandées dans le formulaire. Si votre demande est approuvée par l’équipe Azure Cognitive Services, vous recevez un e-mail contenant des instructions sur la façon d’accéder au service.

## <a name="where-do-i-start"></a>Par où commencer ?

**Étape 1 :** Créer une ressource Form Recognizer dans le portail Azure.

**Étape 2 :** Suivez un démarrage rapide pour utiliser l’API REST :
* [Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec cURL](quickstarts/curl-train-extract.md)
* [Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec Python](quickstarts/python-train-extract.md)
* [Démarrage rapide : Extraire des données de reçu à l’aide de cURL](quickstarts/curl-receipts.md)
* [Démarrage rapide : Extraire des données de reçu à l’aide de Python](quickstarts/python-receipts.md)

Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

**Étape 3 :** Passer en revue les API REST

Vous utilisez les API suivantes pour entraîner et extraire des données structurées à partir de formulaires.

|||
|---|---|
| Entraîner le modèle| Entraîner un nouveau modèle pour analyser vos formulaires avec 5 formulaires du même type. Ou bien entraînez le modèle avec un formulaire vide et deux formulaires remplis.  |
| Analyser le formulaire |Analyser un seul document transmis en tant que flux de données pour extraire à partir du formulaire des paires clé-valeur et des tables avec votre modèle personnalisé.  |
| Analyser le reçu |Analysez un document de reçu unique pour extraire les informations clés et les autres éléments textuels du reçu.|

Explorez la [documentation de référence sur l’API REST](https://aka.ms/form-recognizer/api) pour en savoir plus. 

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Le service est disponible en [préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) d’un service Azure selon les [Conditions des Services en Ligne](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Comme c’est le cas pour tous les services Cognitive Services, les développeurs utilisant le service Form Recognizer doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de [démarrage rapide](quickstarts/curl-train-extract.md) pour commencer à utiliser les [API Form Recognizer](https://aka.ms/form-recognizer/api).