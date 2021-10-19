---
title: Modèle de document général Form Recognizer | Préversion
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide du modèle prédéfini de document général
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 49ee6ed3ff8f23cb819a901aba3f370b95901fa9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716484"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model--preview"></a>Modèle de document général Form Recognizer | Préversion

Le modèle Document général combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour extraire des paires clé-valeur et des entités des documents. Document général est disponible uniquement avec l’API de préversion (v3.0).  Pour plus d’informations sur l’utilisation de l’API de préversion (v3.0), consultez notre [guide de migration](v3-migration-guide.md).

* L’API de document général prend en charge la plupart des types de formulaires. Elle analyse vos documents et associe des valeurs aux clés et des entrées aux tableaux qu’elle découvre. Elle est idéale pour extraire les paires clé-valeur courantes des documents. Vous pouvez utiliser le modèle de document général comme alternative à la [formation d’un modèle personnalisé sans étiquettes](compose-custom-models.md#train-without-labels).

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio et le modèle de document général sont disponibles avec l’API de préversion (v3.0).

* Extrayez des tableaux, des valeurs et des entités de formulaires et de documents grâce à la fonctionnalité Documents généraux de Form Recognizer Studio :

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>Exemple de document traité dans [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) :

:::image type="content" source="media/general-document-analyze.png" alt-text="Capture d’écran : analyse d’un document général dans Form Recognizer Studio.":::

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="general-document-features"></a>Fonctionnalités Document général

* Il n’est pas nécessaire d’effectuer l’apprentissage d’un modèle personnalisé pour extraire des paires clé-valeur.

* Une seule API est utilisée pour extraire des paires clé-valeur, des entités, du texte, des tableaux et la structure de documents.

* Il s’agit d’un modèle préformé qui sera régulièrement formé sur de nouvelles données pour améliorer la couverture et la précision.

* Le modèle de document général prend en charge les données structurées, semi-structurées et non structurées.

## <a name="key-value-pairs"></a>Paires clé-valeur

Les paires clé-valeur sont des portions spécifiques dans le document qui identifient une étiquette ou une clé, ainsi que la réponse ou la valeur associée. Dans un formulaire structuré, il peut s’agir de l’étiquette et de la valeur que l’utilisateur a saisie pour ce champ. Dans un document non structuré, il peut s’agir de la date d’exécution d’un contrat en fonction du texte d’un paragraphe.  Le modèle d’IA est formé à l’extraction des clés et des valeurs identifiables à partir d’une grande variété de types, de formats et de structures de documents.

Les clés peuvent également exister de manière isolée lorsque le modèle détecte qu’une clé existe sans valeur associée ou lors du traitement de champs facultatifs. Par exemple, le champ du second prénom peut être laissé vide sur un formulaire dans certains cas. Les paires clé-valeur sont toujours des portions de texte contenues dans le document et, si vous avez des documents où la même valeur est décrite de différentes manières, par exemple un client ou un utilisateur, la clé associée sera soit client soit utilisateur en fonction du contenu du document. 

## <a name="entities"></a>Entités

Les modèles de traitement du langage naturel peuvent identifier des parties du message et classer chaque élément ou mot. Le modèle de reconnaissance d’entités nommées est capable d’identifier des entités telles que des personnes, des lieux et des dates afin d’offrir une expérience plus riche. L’identification des entités vous permet de distinguer les types de clients, par exemple, un individu ou une organisation.
Le modèle d’extraction de paires clé-valeur et le modèle d’identification des entités sont exécutés en parallèle sur l’ensemble du document et pas seulement sur les valeurs des paires clé-valeur extraites. Cela garantit que les structures complexes où une clé ne peut pas être identifiée sont tout de même enrichies par l’identification des entités référencées. Vous pouvez toujours faire correspondre des clés ou des valeurs à des entités en fonction des décalages des portions identifiées.

* Le document général est un modèle préformé qui peut être appelé directement par le biais de l’API REST. 

* Le modèle de document général prend en charge la reconnaissance d’entité nommée (NER) pour plusieurs catégories d’entités. La reconnaissance d’entité nommée est la capacité d’identifier différentes entités dans du texte et de les catégoriser en classes ou types prédéfinis tels que : personne, lieu, événement, produit et organisation. L’extraction d’entités peut être utile dans les scénarios où vous souhaitez valider des valeurs extraites. Les entités sont extraites de l’ensemble du contenu et pas seulement des valeurs extraites.

## <a name="general-document-model-data-extraction"></a>Extraction de données du modèle de document général

| **Modèle**   | **Extraction de texte** |**Paires clé-valeur** |**Marques de sélection**   | **Tables**   |**Entités** |
| --- | :---: |:---:| :---: | :---: |:---: |
|Document général  | ✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>Critères des entrées

* Pour de meilleurs résultats, fournissez une photo nette ou une copie de qualité par document.
* Formats de fichier pris en charge : JPEG, PNG, BMP, TIFF et PDF (texte incorporé ou numérisé). Les PDF avec du texte incorporé sont préférables pour éviter tout risque d’erreur au niveau de l’extraction et de l’emplacement des caractères.
* Pour PDF et TIFF, il est possible de traiter jusqu’à 2 000 pages (avec un abonnement gratuit, seules les deux premières pages sont traitées).
* La taille du fichier doit être inférieure à 50 Mo.
* Les dimensions des images doivent être comprises entre 50 x 50 et 10 000 x 10 000 pixels.
* Les dimensions des PDF vont jusqu’à 17x17 pouces, ce qui correspond au format papier Legal, A3 ou plus petit.
* La taille totale des données d’entraînement doit être de 500 pages maximum.
* Si vos fichiers PDF sont verrouillés par mot de passe, vous devez supprimer le verrou avant leur envoi.
* Pour l’apprentissage non supervisé (sans données étiquetées) :
  * Les données doivent contenir des clés et des valeurs.
  * Les clés doivent apparaître au-dessus ou à gauche des valeurs, pas en dessous ni à droite.

## <a name="supported-languages-and-locales"></a>Langues et régions prises en charge

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Document général| <ul><li>Anglais (États-Unis) : en-US</li></ul>| Anglais (États-Unis) : en-US|

### <a name="named-entity-recognition-ner-categories"></a>Catégories de reconnaissance d’entité nommée (NER)

| Category | Type | Description |
|-----------|-------|--------------------|
| Personne | string | Nom partiel ou complet d’une personne. |
|PersonType | string | Type d’emploi ou rôle d’une personne.  |
| Emplacement | string | Points de repère, structures et caractéristiques géographiques et entités géopolitiques naturels et créés par l’homme |
| Organisation | string | Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. |
| Événement | string | Événements historiques, sociaux et naturels. |
| Produit | string |Objets physiques de différentes catégories. |
| Compétence | string | Une capacité, une compétence ou une expertise. |
| Adresse | string | Adresses postales complètes. |
| Numéro de téléphone | string| Numéros de téléphone | 
Courrier | string | Adresse e-mail. |
| URL | string| URL et liens de sites web|
| Adresse IP | string| Adresses IP du réseau. |
| DateTime | string| Dates et heures du jour. |
| Quantité | string | Mesures et unités numériques. |

## <a name="considerations"></a>Considérations

* L’extraction d’entités peut être utile dans les scénarios où vous souhaitez valider des valeurs extraites. Les entités sont extraites sur l’ensemble du contenu des documents et pas seulement sur les valeurs extraites.

* Les clés sont des portions de texte extraites du document. Pour les documents semi-structurés, il peut être nécessaire de faire correspondre les clés à un dictionnaire de clés existant.

* Attendez-vous à voir des paires clé-valeur avec une clé, mais sans valeur. Par exemple, si un utilisateur a choisi de ne pas fournir d’adresse e-mail sur le formulaire.

## <a name="next-steps"></a>Étapes suivantes

* Suivez le [**Guide de migration de Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la version préliminaire dans vos applications et vos flux de travail.

* Découvrez notre [**API REST (préversion)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)
