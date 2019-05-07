---
title: Qu’est-ce que Form Recognizer ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser Form Recognizer pour analyser des données de formulaire et de table.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 2a120a59a58eb8d7a017cce0dd85c21038bdcf51
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143215"
---
# <a name="what-is-form-recognizer"></a>Qu’est-ce que Form Recognizer ?

Azure Form Recognizer est un service cognitif qui utilise la technologie de machine learning pour identifier et extraire des données de paires clé-valeur et de tables à partir de documents de formulaire. Il restitue ensuite des données structurées qui incluent les relations du fichier d’origine. Vous pouvez appeler votre modèle personnalisé Form Recognizer au moyen d’une API REST simple, afin de réduire la complexité et facilement l’intégrer à votre workflow ou application. Vous n’avez besoin que de cinq documents de formulaire ou d’un formulaire vide d’un type identique à votre document d’entrée pour commencer. Vous pouvez obtenir des résultats rapidement, avec précision et en adéquation avec votre contenu particulier, sans la nécessité d’une intervention manuelle lourde ou de compétences approfondies en science des données.

## <a name="request-access"></a>Demander l'accès
Form Recognizer est disponible en préversion à accès limité. Pour accéder à la préversion, remplissez et envoyez le formulaire de [demande d’accès à Cognitive Services Form Recognizer](https://aka.ms/FormRecognizerRequestAccess). Des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser Form Recognizer vous sont demandées dans le formulaire. Si votre demande est approuvée par l’équipe Azure Cognitive Services, vous recevez un e-mail contenant des instructions sur la façon d’accéder au service.

## <a name="what-it-does"></a>Résultat

Lorsque vous soumettez vos données d’entrée, l’algorithme s’entraîne avec elles, groupe les formulaires par types, détecte les clés et les tables présentes, et apprend à associer les valeurs aux clés et les entrées aux tables. Un apprentissage non supervisé permet au modèle de comprendre la disposition ainsi que les relations entre les champs et les entrées, sans étiquetage manuel des données ni codage et maintenance intensifs. En revanche, les modèles Machine Learning préentrainés nécessitent des données standardisées et s’avèrent moins précis avec des documents d’entrée qui s’écartent des formats classiques, comme les formulaires spécifiques à un secteur d’activité.

Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

## <a name="what-it-includes"></a>Contenu

Form Recognizer est disponible en tant qu’API REST. Vous pouvez créer, entraîner et scorer un modèle en appelant l’API, puis exécuter ce modèle dans un conteneur Docker local si vous le souhaitez.

## <a name="input-requirements"></a>Critères des entrées

Form Recognizer fonctionne sur les documents d’entrée qui répondent aux critères suivants :

* Format JPG, PNG ou PDF (texte ou numérisé). Les PDF englobant du texte sont préférables, car il n’existe aucun risque d’erreur au niveau de l’extraction et de l’emplacement des caractères.
* La taille du fichier doit être inférieure à 4 mégaoctets (Mo)
* Pour les images, les dimensions doivent être comprises entre 50x50 et 4200x4200 pixels
* S’ils sont numérisés à partir de documents papier, la qualité de numérisation des formulaires doit être supérieure
* Doivent utiliser l’alphabet latin (lettres de l’alphabet)
* Données imprimées (pas manuscrites)
* Doivent contenir des clés et des valeurs
* Les clés peuvent apparaître au-dessus ou à gauche des valeurs, mais pas en dessous ni à droite.

Qui plus est, Form Recognizer ne prend pas encore en charge les types de données d’entrée suivants :

* Tables complexes (tables imbriquées, cellules ou en-têtes fusionnés, etc.) 
* Cases à cocher ou cases d’option
* Documents PDF de plus de 50 pages

## <a name="where-do-i-start"></a>Par où commencer ?

**Étape 1 :** Créer une ressource Form Recognizer dans le portail Azure.

**Étape 2 :** Essayer un guide de démarrage rapide pour une expérience pratique :
* [Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec cURL](quickstarts/curl-train-extract.md)
* [Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec Python](quickstarts/python-train-extract.md)

Pour l’apprentissage, nous vous recommandons le service gratuit. Notez toutefois que le nombre de pages gratuites est limité à 500 pages par jour.

**Étape 3 :** Passer en revue les API REST. Utiliser les API suivantes pour entraîner et extraire des données structurées à partir de formulaires.

| API REST | Description |
|-----|-------------|
| Former | Entraîner un nouveau modèle pour analyser vos formulaires avec 5 formulaires du même type ou un formulaire vide.  |
| Analyser  |Analyser un seul document transmis en tant que flux de données pour extraire à partir du formulaire des paires clé-valeur et des tables avec votre modèle personnalisé.  |

Explorez le [document de référence sur l’API REST](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Le service est disponible en [Préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) d’un service Azure selon les [Conditions des Services en Ligne](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Comme c’est le cas pour tous les services Cognitive Services, les développeurs utilisant le service Form Recognizer doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de [démarrage rapide](quickstarts/curl-train-extract.md) pour commencer à utiliser les [API Form Recognizer](https://aka.ms/form-recognizer/api).
