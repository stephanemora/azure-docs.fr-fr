---
title: Factures - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés à l’analyse des factures avec l’API Form Recognizer - utilisation et limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: lajanuar
ms.openlocfilehash: e30e431ec393a59e0799bf1c56611fbba84d8960
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108330581"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Modèle de facture prédéfini Form Recognizer

Azure Form Recognizer peut analyser et extraire des informations dans les factures à l’aide de ses modèles de facture prédéfinis. L’API de facture permet aux clients d’accepter des factures dans divers formats et de retourner des données structurées pour automatiser le traitement des factures. L’API combine nos puissantes fonctionnalités de [Reconnaissance optique de caractères (OCR)](../computer-vision/overview-ocr.md) à des modèles de Deep Learning qui comprennent les factures afin d’extraire des informations clés des factures écrites en anglais. Elle extrait le texte, les tableaux et les informations comme le client, le fournisseur, le numéro de facture, la date d’échéance, le total, le montant dû, le montant des taxes, l’adresse d’expédition, l’adresse de facturation, les éléments de ligne, etc. L’API Invoice prédéfinie est en disponibilité publique dans la préversion de Form Recognizer v2.1.

## <a name="what-does-the-invoice-service-do"></a>Comment le service Invoice fonctionne-t-il ?

L’API Invoice extrait les champs et éléments de ligne clés des factures et les retourne dans une réponse JSON structurée et organisée. Les factures peuvent être de formats et de qualités divers, notamment des images capturées par téléphone, des documents numérisés et des fichiers PDF numériques. L’API Invoice va extraire la sortie structurée de toutes ces factures.

![Exemple de facture Contoso](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Faire un essai

Pour tester le service Invoice de Form Recognizer, accédez à l’outil d’exemple d’interface utilisateur en ligne :

> [!div class="nextstepaction"]
> [Essayer des modèles prédéfinis](https://fott-preview.azurewebsites.net/)

Vous aurez besoin d’un abonnement Azure ([créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services)), ainsi que d’un point de terminaison et d’une clé de [ressource Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) pour tester le service Invoice de Form Recognizer.

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Exemple de facture analysée" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Critères des entrées

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Paramètres régionaux pris en charge

La **facture prédéfinie v2.1-preview.3** (préversion) prend en charge les factures dans les paramètres régionaux **en-us**.

## <a name="the-analyze-invoice-operation"></a>Opération d’analyse d’une facture

L’opération d’[analyse d’une facture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) prend une image ou un fichier PDF d’une facture en entrée, puis en extrait les valeurs intéressantes. L’appel retourne un champ d’en-tête de réponse appelé `Operation-Location`. La valeur `Operation-Location` est une URL qui contient l’ID de résultat à utiliser à l’étape suivante.

|En-tête de réponse| URL de résultat |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>Opération d’obtention du résultat de l’analyse de la facture

La deuxième étape consiste à appeler l’opération d’[obtention du résultat de l’analyse de la facture](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83). Cette opération prend en entrée l’ID de résultat créé par l’opération d’analyse de la facture. Elle retourne une réponse JSON qui contient un champ **État** avec les possibles valeurs suivantes. Vous appelez cette opération de façon itérative jusqu’à ce qu’elle retourne avec la valeur **succeeded**. Utilisez un intervalle de 3 à 5 secondes pour éviter de dépasser le taux de demandes par seconde (RPS).

|Champ| Type | Valeurs possibles |
|:-----|:----:|:----|
|status | string | notStarted : L’opération d’analyse n’a pas commencé.<br /><br />running : L’opération d’analyse est en cours.<br /><br />failed : L’opération d’analyse a échoué.<br /><br />succeeded : L’opération d’analyse a réussi.|

Quand le champ **status** a la valeur de **succeeded**, la réponse JSON inclut les résultats de la compréhension de la facture, les tables extraites et les résultats de la reconnaissance de texte facultative, si nécessaire. Le résultat de la compréhension de la facture est organisé sous la forme d’un dictionnaire de valeurs de champ nommées, où chaque valeur contient le texte extrait, la valeur normalisée, le cadre englobant, la confiance et les éléments de mot correspondants. Il inclut également les lignes extraites contenant chacune le montant, la description, le prix unitaire, la quantité, etc. Le résultat de reconnaissance de texte est organisé sous la forme d’une hiérarchie de lignes et de mots, avec du texte, un cadre englobant et des informations relatives à la confiance.

### <a name="sample-json-output"></a>Exemple de sortir JSON

La réponse à l’opération d’obtention du résultat de l’analyse de la facture va correspondre à la représentation structurée de la facture avec toutes les informations extraites.
Reportez-vous à cet [exemple de fichier de facture](media/sample-invoice.jpg) et cet [exemple de sortie de facture](media/invoice-example-new.jpg) structurée.

La sortie JSON comporte trois parties :
* Le nœud `"readResults"` contient tout le texte reconnu et toutes les marques de sélection. Le texte est organisé par page, puis par ligne, puis par mots individuels.
* Le nœud `"pageResults"` contient les tables et les cellules extraites avec leurs cadres englobants, la confiance et une référence aux lignes et aux mots dans « readResults ».
* Le nœud `"documentResults"` contient les valeurs et les éléments de ligne spécifiques à la facture que le modèle a découverts. C’est là que vous trouverez tous les champs de la facture, tels que l’ID de la facture, l’adresse d’expédition, l’adresse de facturation, le client, le total, les éléments de ligne et bien plus encore.

## <a name="example-output"></a>Exemple de sortie

Le service de facture extrait le texte, les tables, ainsi que 26 champs de facture. Voici les champs extraits d’une facture dans la réponse de sortie JSON (la sortie ci-dessous utilise cet [exemple de facture](media/sample-invoice.jpg)).

|Name| Type | Description | Texte | Valeur (sortie standardisée) |
|:-----|:----|:----|:----| :----|
| CustomerName | string | Client destinataire de la facture | Microsoft Corp |  |
| CustomerId | string | ID de référence du client | CID-12345 |  |
| PurchaseOrder | string | Numéro de référence du bon de commande | PO-3333 | |
| InvoiceId | string | ID de cette facture (souvent appelé « numéro de facture ») | INV-100 | |
| InvoiceDate | Date | Date d’émission de la facture | 15/11/2019 | 2019-11-15 |
| DueDate | Date | Date d’échéance du paiement de cette facture | 15/12/2019 | 2019-12-15 |
| VendorName | string | Fournisseur qui a créé cette facture | CONTOSO LTD. | |
| VendorAddress | string | Adresse postale du fournisseur | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | string | Nom associé à l’adresse postale du fournisseur | Siège social de Contoso | |
| CustomerAddress | string | Adresse postale du client | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | string | Nom associé à l’adresse postale du client | Microsoft Corp | |
| BillingAddress | string | Adresse de facturation explicite du client | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | string | Nom associé à l’adresse de facturation | Services Microsoft | |
| ShippingAddress | string | Adresse d’expédition explicite du client | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | string | Nom associé à l’adresse d’expédition | Livraison Microsoft | |
| SubTotal | nombre | Champ de sous-total identifié sur cette facture | 100,00 $ | 100 |
| TotalTax | nombre | Champ du total des taxes identifié sur cette facture | 10,00 $ | 10 |
| InvoiceTotal | nombre | Total des nouveaux frais associés à cette facture | 110,00 $ | 110 |
| AmountDue |  nombre | Montant total dû au fournisseur | 610,00 $ | 610 |
| ServiceAddress | string | Adresse du service ou de la propriété explicite du client | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | string | Nom associé à l’adresse du service | Services Microsoft | |
| RemittanceAddress | string | Adresse d’envoi du paiement explicite pour le client | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | string | Nom associé à l’adresse d’envoi du paiement explicite | Facturation Contoso |  |
| ServiceStartDate | Date | Date de début de la période de service (par exemple, pour une période de service de facturation d’utilitaire) | 14/10/2019 | 2019-10-14 |
| ServiceEndDate | Date | Date de fin de la période de service (par exemple, pour une période de service de facturation d’utilitaire) | 14/11/2019 | 2019-11-14 |
| PreviousUnpaidBalance | nombre | Solde précédent impayé explicite | 500,00 $ | 500 |

Voici les élément de ligne extraits d’une facture dans la réponse de la sortie JSON (la sortie ci-dessous utilise cet [exemple de facture](./media/sample-invoice.jpg)) :

|Name| Type | Description | Texte (élément de ligne no 1) | Valeur (sortie standardisée) |
|:-----|:----|:----|:----| :----|
| Éléments | string | Texte complet de l’élément de ligne | 04/03/2021 A123 Services de conseil 2 heures 30,00 $ 10 % 60,00 $ | |
| Montant | nombre | Montant de l’élément de ligne | 60,00 $ | 100 |
| Description | string | Description textuelle de l’élément de ligne de la facture | Service de conseil | Service de conseil |
| Quantité | nombre | Quantité pour cet élément de ligne de la facture | 2 | 2 |
| UnitPrice | nombre | Prix net ou brut (en fonction du paramètre de facturation brute de la facture) d’une unité de cet élément | 30,00 $ | 30 |
| ProductCode | string| Code du produit, numéro de produit ou référence SKU associé à l’élément de ligne spécifique | A123 | |
| Unité | string| L’unité de l’élément de ligne (par exemple : kg, lb, etc.) | heures | |
| Date | date| Date correspondant à chaque élément de ligne. Il s’agit souvent de la date d’expédition de l’élément de ligne | 04/03/2021| 04-03-2021 |
| Taxe | nombre | Taxe associée à chaque élément de ligne. Les valeurs possibles incluent le montant des taxes, le pourcentage de la taxe et la valeur d’application de la taxe (O/N) | 10 % | |


## <a name="next-steps"></a>Étapes suivantes

- Essayez vos propres factures et exemples dans l’[exemple d’interface utilisateur de Form Recognizer](https://fott-preview.azurewebsites.net/).
- Suivez un [démarrage rapide de Form Recognizer](quickstarts/client-library.md) pour commencer à écrire une application de traitement de factures avec Form Recognizer dans le langage de développement de votre choix.

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que Form Recognizer ?](./overview.md)
* [Documentation de référence sur l’API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
