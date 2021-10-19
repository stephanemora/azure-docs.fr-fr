---
title: Modèle de carte de visite Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide d’un modèle prédéfini de carte de visite
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: b9517dcab3f748283c7dace99c0e49796dae7f7f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716471"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>Modèle de carte de visite Form Recognizer

## <a name="overview"></a>Vue d’ensemble

Le modèle de carte de visite combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour analyser et extraire des informations clés à partir d’images de cartes de visite. L’API analyse les cartes de visite imprimées, extrait les informations clés, telles que le prénom, le nom, le nom de la société, l’adresse e-mail et le numéro de téléphone, et renvoie une représentation structurée des données JSON.

***Exemple de carte de visite traitée avec l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/) :***

:::image type="content" source="./media/overview-business-card.jpg" alt-text="exemple de carte de visite" lightbox="./media/overview-business-card.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

* Extrayez le nom, la fonction, l’adresse, l’adresse e-mail, le nom de la société et bien plus encore grâce à notre fonctionnalité Carte de visite Form Recognizer Studio :

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

## <a name="try-it-sample-labeling-tool"></a>Essayer : Outil d’étiquetage des exemples

Vous pouvez voir la méthode d’extraction des données de carte de visite en essayant notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, cliquez sur **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

* Une carte de visite. Vous pouvez utiliser notre [exemple de carte de visite](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png).

> [!div class="nextstepaction"]
  > [Essayer](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Dans l’interface utilisateur de Form Recognizer :

  1. Sélectionnez **Utiliser un modèle prédéfini pour extraire des données**.
  1. Sélectionnez **Carte de visite** dans le menu déroulant **Type de formulaire** :

  :::image type="content" source="media/try-business-card.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

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

> [!NOTE]
> L’[outil d’étiquetage des exemples](https://fott-2-1.azurewebsites.net/) ne prend pas en charge le format de fichier BMP. Il s’agit d’une limitation de l’outil et non du service Form Recognizer.

## <a name="supported-languages-and-locales"></a>Langues et régions prises en charge

>[!NOTE]
 > Il n’est pas nécessaire de spécifier de paramètres régionaux. Il s'agit d'un paramètre facultatif. La technologie d’apprentissage profond de Form Recognizer détecte automatiquement la langue du texte figurant dans votre image.

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Carte de visite| <ul><li>Anglais (États-Unis) : en-US</li><li> Anglais (Australie) : en-AU</li><li>Anglais (Canada) : en-CA</li><li>Anglais (Royaume-Uni) : en-GB</li><li>Anglais (Inde) : en-IN</li></ul>  | Détection automatique |

## <a name="key-value-pair-extraction"></a>Extraction de paires clé-valeur

|Name| Type | Description |Sortie standardisée |
|:-----|:----|:----|:----:|
| ContactNames | tableau d’objets | Nom du contact |  |
| FirstName | string | Prénom du contact |  |
| LastName | string | Nom du contact |  |
| CompanyNames | tableau de chaînes | Nom(s) de la société|  |
| Departments | tableau de chaînes | Service(s) ou organisation(s) de contact |  |
| JobTitles | tableau de chaînes | Poste(s) indiqué(s) du contact |  |
| E-mails | tableau de chaînes | Adresse(s) e-mail du contact |  |
| Sites web | tableau de chaînes | Site(s) web de la société |  |
| Adresses | tableau de chaînes | Adresse(s) extraite(s) de la carte de visite | |
| MobilePhones | tableau de numéros de téléphone | Numéro(s) de téléphone mobile sur la carte de visite |+1 xxx xxx xxxx |
| Télécopies | tableau de numéros de téléphone | Numéro(s) de fax sur la carte de visite | +1 xxx xxx xxxx |
| WorkPhones | tableau de numéros de téléphone | Numéro(s) de téléphone du bureau sur la carte de visite | +1 xxx xxx xxxx | 
| OtherPhones     | tableau de numéros de téléphone | Autre(s) numéro(s) de téléphone sur la carte de visite | +1 xxx xxx xxxx |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La version préliminaire de Form Recognizer introduit plusieurs nouvelles fonctionnalités.

* Suivez le [**Guide de migration de Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la version préliminaire dans vos applications et vos flux de travail.

* Découvrez notre [**API REST (préversion)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
