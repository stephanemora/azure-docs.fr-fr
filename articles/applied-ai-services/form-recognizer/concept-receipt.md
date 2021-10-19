---
title: Modèle de reçu Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide du modèle prédéfini de reçu
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 48ebbb4975e57db34fe080db2a61328cef65ca80
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716493"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Modèle de reçu Form Recognizer

Le modèle de reçu combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour analyser et extraire des informations clés des reçus de vente. Les reçus peuvent être de différents formats et qualités, y compris les reçus imprimés et manuscrits. L’API extrait les informations clés telles que le nom du commerçant, le numéro de téléphone du commerçant, la date de la transaction, la taxe et le total de la transaction et renvoie une représentation structurée des données JSON.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>Exemple de reçu traité avec l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/) :

:::image type="content" source="./media/overview-receipt.jpg" alt-text="exemple de ticket de caisse" lightbox="./media/overview-receipt.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

* Extrayez l’heure et la date des transactions, les informations du commerçant, les totaux des montants, et plus encore grâce à notre fonctionnalité Reçu Form Recognizer Studio :

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

## <a name="try-it-sample-labeling-tool"></a>Essayer : Outil d’étiquetage des exemples

Vous pouvez voir la méthode d’extraction des données de reçu en essayant notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, cliquez sur **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

* Un reçu. Vous pouvez utiliser notre [exemple de reçu](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png).

> [!div class="nextstepaction"]
  > [Essayer](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Dans l’interface utilisateur de Form Recognizer :

  1. Sélectionnez **Utiliser un modèle prédéfini pour extraire des données**.
  1. Sélectionnez **Reçu** dans le menu déroulant **Type de formulaire** :

  :::image type="content" source="media/try-receipt.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

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

## <a name="supported-languages-and-locales-v21"></a>Langues et paramètres régionaux pris en charge v2.1

>[!NOTE]
 > Il n’est pas nécessaire de spécifier de paramètres régionaux. Il s'agit d'un paramètre facultatif. La technologie d’apprentissage profond de Form Recognizer détecte automatiquement la langue du texte figurant dans votre image.

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Réception| <ul><li>Anglais (États-Unis) : en-US</li><li> Anglais (Australie) : en-AU</li><li>Anglais (Canada) : en-CA</li><li>Anglais (Royaume-Uni) : en-GB</li><li>Anglais (Inde) : en-IN</li></ul>  | Détection automatique |

## <a name="key-value-pair-extraction"></a>Extraction de paires clé-valeur

|Name| Type | Description | Sortie standardisée |
|:-----|:----|:----|:----|
| ReceiptType | string | Type de reçu |  Itemized |
| MerchantName | string | Nom du commerçant émettant le reçu |  |
| MerchantPhoneNumber | phoneNumber | Numéro de téléphone de la liste de commerçants | +1 xxx xxx xxxx |
| MerchantAddress | string | Adresse répertoriée du commerçant |   |
| TransactionDate | Date | Date d’émission du reçu | aaaa-mm-jj |
| TransactionTime | time | Heure d’émission du reçu | hh-mm-ss (24 heures)  |
| Total | nombre (USD)| Total du reçu | Virgule flottante à deux décimales|
| Sous-total | nombre (USD) | Sous-total du reçu, souvent avant application des taxes | Virgule flottante à deux décimales|
| Taxe | nombre (USD) | Taxe sur le reçu (TVA ou équivalent) | Virgule flottante à deux décimales |
| Conseil | nombre (USD) | Pourboire inclus par l’acheteur | Virgule flottante à deux décimales|
| Éléments | tableau d’objets | Lignes extraites, avec le nom, la quantité, le prix unitaire et le prix total extraits | |
| Nom | string | Nom de l’élément | |
| Quantité | nombre | Quantité de chaque élément | entier |
| Price | nombre | Prix individuel de chaque unité d’article| Virgule flottante à deux décimales |
| Prix total | nombre | Prix total de la ligne de facturation | Virgule flottante à deux décimales |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer préversion v3.0

 La préversion de Form Recognizer introduit plusieurs nouvelles fonctionnalités et capacités :

* Le modèle de **reçu** prend en charge le traitement des reçus d’hôtel d’une seule page.

    ### <a name="hotel-receipt-key-value-pair-extraction"></a>Extraction de paires clé-valeur de reçus d’hôtel

    |Name| Type | Description | Sortie standardisée |
    |:-----|:----|:----|:----|
    | ArrivalDate | Date | Date d’arrivée | aaaa-mm-jj |
    | Devise | currency | Unité monétaire des montants du reçu. Par exemple, USD, EUR ou MIXED si plusieurs valeurs sont trouvées ||
    | DepartureDate | Date | Date de départ | aaaa-mm-jj |
    | Éléments | tableau | | |
    | Items.*.Category | string | Catégorie de l’élément, par exemple, chambre, taxe, etc. |  |
    | Items.*.Date | Date | Date de l’élément | aaaa-mm-jj |
    | Items.*.Description | string | Description de l’élément | |
    | Items.*.TotalPrice |  nombre | Prix total de l’élément | entier |
    | Paramètres régionaux | string | Paramètres régionaux du reçu, par exemple, en-US. | Code ISO langue-pays   |
    | MerchantAddress | string | Adresse répertoriée du commerçant | |
    | MerchantAliases | tableau| | |
    | MerchantAliases.* | string | Autre nom du commerçant |  |
    | MerchantName | string | Nom du commerçant émettant le reçu | |
    | MerchantPhoneNumber | phoneNumber | Numéro de téléphone de la liste de commerçants | +1 xxx xxx xxxx|
    | ReceiptType | string | Type de reçu, par exemple, Hôtel, Détaillé | |
    | Total | nombre | Total du reçu | Virgule flottante à deux décimales |

    ### <a name="hotel-receipt-supported-languages-and-locales"></a>Langues et paramètres régionaux pris en charge pour les reçus d’hôtel

    | Modèle | Langue : code de paramètres régionaux | Default |
    |--------|:----------------------|:---------|
    |Reçu (hôtel) | <ul><li>Anglais (États-Unis) : en-US</li></ul>| Anglais (États-Unis) : en-US|

* Suivez le [**Guide de migration de Form Recognizer v3.0**](v3-migration-guide.md) pour apprendre à utiliser la version préliminaire dans vos applications et vos flux de travail.

* Découvrez notre [**API REST (préversion)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) pour en savoir plus sur la préversion et les nouvelles fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

* Suivre un démarrage rapide Form Recognizer :

  > [!div class="nextstepaction"]
  > [Démarrage rapide Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Découvrir notre API REST :

    > [!div class="nextstepaction"]
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
