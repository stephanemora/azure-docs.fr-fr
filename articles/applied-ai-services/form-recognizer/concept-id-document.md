---
title: Modèle de document d’identité Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide du modèle de document d’identité prédéfini
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b73526ad245d9364b0ada997fc014e0dd16df40
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716476"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Modèle de document d’identité Form Recognizer

Le modèle de document d’identité associe de puissantes fonctionnalités de reconnaissance optique de caractères (OCR) à des modèles de Deep Learning pour analyser et extraire des informations clés de permis de conduire américains (les 50 états et le District de Columbia) et de pages biographiques de passeports internationaux (à l’exclusion des visas et autres documents de voyage). L’API analyse les documents d’identité ; extrait des informations clés, telles que le prénom, le nom, l’adresse et la date de naissance ; et retourne une représentation des données JSON structurée.

***Exemple de permis de conduire américain traité avec [l’outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/) :***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="exemple de carte d’identité" lightbox="./media/overview-id.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio est disponible avec l’API (v3.0) en préversion.

* Extrayez le nom, la zone lisible par l’ordinateur et la date d’expiration avec la fonctionnalité de document d’identité Form Recognizer Studio :

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

## <a name="try-it-sample-labeling-tool"></a>Essayer : Outil d’étiquetage des exemples

Vous pouvez voir comment les données du document d’identité sont extraites en essayant notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : vous pouvez [en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, cliquez sur **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

* Un document d’identité. Vous pouvez utiliser notre [exemple de document d’identité](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg).

> [!div class="nextstepaction"]
  > [Essayer](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Dans l’interface utilisateur de Form Recognizer :

  1. Sélectionnez **Utiliser un modèle prédéfini pour extraire des données**.
  1. Sélectionnez **Reçu** dans le menu déroulant **Type de formulaire** :

  :::image type="content" source="media/try-id-document.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

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
> L'[outil d’étiquetage des exemples](https://fott-2-1.azurewebsites.net/) ne prend pas en charge le format de fichier BMP. Il s’agit d’une limitation de l’outil et non du service Form Recognizer.

## <a name="supported-languages-and-locales-v21"></a>Langues et régions prises en charge v2.1

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Document d’identité| <ul><li>Anglais (États-Unis) : en-US (permis de conduire)</li><li>Pages biographiques de passeports internationaux</br> (sauf visa et autres documents de voyage)</li></ul></br>|Anglais (États-Unis) : en-US|

## <a name="key-value-pair-extraction"></a>Extraction de paires clé-valeur

|Name| Type | Description | Sortie standardisée|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 |  |
|  DateOfBirth | Date | DOB | aaaa-mm-jj |
|  DateOfExpiration | Date | Date d’expiration DOB | aaaa-mm-jj |
|  DocumentNumber | string | Numéro de passeport, numéro de permis de conduire, etc. |  |
|  FirstName | string | Prénom et initiale du second prénom extraits, le cas échéant |  |
|  LastName | string | Nom de famille extrait |  |
|  Nationalité | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 (passeport uniquement) |  |
|  Sex | string | Les valeurs extraites possibles sont « M », « F » et « X ». | |
|  MachineReadableZone | object | MRZ du passeport extraite, y compris 2 lignes de 44 caractères chacun | « P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816 » |
|  DocumentType ; | string | Type de document, par exemple, passeport ou permis de conduire | « passeport » |
|  Adresse | string | Adresse extraite (permis de conduire uniquement) ||
|  Region | string | Région, État, province et autres informations extraites (permis de conduire uniquement) |  |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La préversion de Form Recognizer introduit plusieurs nouvelles fonctionnalités :

* Le modèle **Document d’identité (v3.0)** prend en charge l’extraction des approbations, des restrictions et des classifications de véhicules à partir de permis de conduire américains.

    ### <a name="id-document-preview-key-value-pair-extraction"></a>Préversion du document d’identité - Extraction de paires clé-valeur

    |Name| Type | Description | Sortie standardisée|
    |:-----|:----|:----|:----|
    | 🆕 Approbations | string | Privilèges de conduite supplémentaires accordés à un conducteur comme Motocyclette ou Bus scolaire.  | |
    | 🆕 Restrictions | string | Privilèges de conduite restreints applicables aux permis suspendus ou révoqués.| |
    | 🆕VehicleClassification | string | Types de véhicules qui peuvent être pilotés par un conducteur. ||
    |  CountryRegion | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 |  |
    |  DateOfBirth | Date | DOB | aaaa-mm-jj |
    |  DateOfExpiration | Date | Date d’expiration DOB | aaaa-mm-jj |
    |  DocumentNumber | string | Numéro de passeport, numéro de permis de conduire, etc. |  |
    |  FirstName | string | Prénom et initiale du second prénom extraits, le cas échéant |  |
    |  LastName | string | Nom de famille extrait |  |
    |  Nationalité | countryRegion | Code de pays ou de région conforme à la norme ISO 3166 (passeport uniquement) |  |
    |  Sex | string | Les valeurs extraites possibles sont « M », « F » et « X ». | |
    |  MachineReadableZone | object | MRZ du passeport extraite, y compris 2 lignes de 44 caractères chacun | « P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816 » |
    |  DocumentType ; | string | Type de document, par exemple, passeport ou permis de conduire | « passeport » |
    |  Adresse | string | Adresse extraite (permis de conduire uniquement) ||
    |  Region | string | Région, État, province et autres informations extraites (permis de conduire uniquement) |  |

* Suivez le [**Guide de migration de Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la préversion dans vos applications et vos flux de travail.

* Découvrez notre [**API REST (préversion)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)