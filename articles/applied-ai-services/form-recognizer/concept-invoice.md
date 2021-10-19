---
title: Modèle de facture Form Recognizer
titleSuffix: Azure Applied AI Services
description: Concepts englobant l’extraction et l’analyse de données à l’aide d’un modèle prédéfini de facture
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b660d464a8615886be9b466fd2e9de808ef3bd9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716519"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Modèle de facture Form Recognizer

 Le modèle de facture combine de puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles Deep Learning pour analyser et extraire les champs clés et les éléments de ligne des factures de vente.  Les factures peuvent être de formats et de qualités divers, notamment des images capturées par téléphone, des documents numérisés et des fichiers PDF. L’API analyse le texte de la facture, extrait les informations clés, telles que le nom du client, l’adresse de facturation, la date d’échéance et le montant dû, et renvoie une représentation structurée des données JSON.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>Exemple de facture traitée avec l’[outil d’étiquetage des exemples Form Recognizer](https://fott-2-1.azurewebsites.net/) :

:::image type="content" source="media/overview-invoices.jpg" alt-text="Exemple de facture" lightbox="media/overview-invoices-big.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Essayer Form Recognizer Studio (préversion)

* Form Recognizer Studio est disponible avec l’API (v 3.0) en préversion.

* Extrayez les détails du client et du fournisseur, les éléments de ligne, et plus encore grâce à notre fonctionnalité Facture Form Recognizer Studio :

> [!div class="nextstepaction"]
> [Essayer Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## <a name="try-it-sample-labeling-tool"></a>Essayer : Outil d’étiquetage des exemples

Vous pouvez voir la méthode d’extraction des données de facturation en essayant notre outil d’étiquetage des exemples. Vous aurez besoin des éléments suivants :

* Un abonnement Azure : [vous pouvez en créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)

* Une [instance Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service. Une fois votre ressource déployée, cliquez sur **Accéder à la ressource** pour accéder à la clé et au point de terminaison de votre API.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le Portail Azure.":::

* Une facture. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf).

> [!div class="nextstepaction"]
  > [Essayer](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Dans l’interface utilisateur de Form Recognizer :

  1. Sélectionnez **Utiliser un modèle prédéfini pour extraire des données**.
  1. Sélectionnez **Facture** dans le menu déroulant **Type de formulaire** :

  :::image type="content" source="media/try-invoice.png" alt-text="Capture d’écran : menu déroulant de sélection du modèle prédéfini de l’outil d’étiquetage des exemples.":::

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

| Modèle | Langue : code de paramètres régionaux | Default |
|--------|:----------------------|:---------|
|Facture| <ul><li>Anglais (États-Unis) : en-US</li></ul>| Anglais (États-Unis) : en-US|

## <a name="key-value-pair-extraction"></a>Extraction de paires clé-valeur

|Name| Type | Description | Sortie standardisée |
|:-----|:----|:----|:---:|
| CustomerName | string | Client facturé| |
| CustomerId | string | ID de référence client | |
| PurchaseOrder | string | Numéro de référence du bon de commande | |
| InvoiceId | string | ID de cette facture (souvent appelé « numéro de facture ») | |
| InvoiceDate | Date | Date d’émission de la facture | aaaa-mm-jj|
| DueDate | Date | Date d’échéance du paiement de cette facture | aaaa-mm-jj|
| VendorName | string | Nom du fournisseur |  |
| VendorAddress | string |  Adresse postale du vendeur|  |
| VendorAddressRecipient | string | Nom associé à l’adresse postale du fournisseur |  |
| CustomerAddress | string | Adresse postale du client | |
| CustomerAddressRecipient | string | Nom associé à l’adresse postale du client | |
| BillingAddress | string | Adresse de facturation explicite du client |  |
| BillingAddressRecipient | string | Nom associé à l’adresse de facturation | |
| ShippingAddress | string | Adresse d’expédition explicite du client | |
| ShippingAddressRecipient | string | Nom associé à l’adresse d’expédition |  |
| SubTotal | nombre | Champ de sous-total identifié sur cette facture | entier |
| TotalTax | nombre | Champ du total des taxes identifié sur cette facture | entier |
| InvoiceTotal | nombre (USD) | Total des nouveaux frais associés à cette facture | entier |
| AmountDue |  nombre (USD) | Montant total dû au fournisseur | entier |
| ServiceAddress | string | Adresse du service ou de la propriété explicite du client | |
| ServiceAddressRecipient | string | Nom associé à l’adresse du service |  |
| RemittanceAddress | string | Adresse d’envoi du paiement explicite pour le client |   |
| RemittanceAddressRecipient | string | Nom associé à l’adresse d’envoi du paiement explicite |  |
| ServiceStartDate | Date | Date de début de la période de service (par exemple, pour une période de service de facturation d’utilitaire) | aaaa-mm-jj |
| ServiceEndDate | Date | Date de fin de la période de service (par exemple, pour une période de service de facturation d’utilitaire) | aaaa-mm-jj|
| PreviousUnpaidBalance | nombre | Solde précédent impayé explicite | entier |

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
    > [API Form Recognizer v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
