---
title: 'Prise en main : API REST Azure Form Recognizer v2.1'
description: Utilisez l’API REST Form Recognizer v2.1 afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de tableau de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d53287f5ed83f87d21880605e80206527264dd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030219"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> Ce guide de démarrage rapide cible la version **2.1** de l’API REST Azure Form Recognizer et utilise cURL pour exécuter des appels d’API REST.

| [API REST Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Informations de référence de l’API REST Azure](/rest/api/azure/) |

Dans ce démarrage rapide, vous allez utiliser les API suivantes pour extraire des données structurées de formulaires et de documents :

* [Disposition](#try-it-layout-model)

* [Facture](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) installé.

* [PowerShell version 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows) ou une application de ligne de commande similaire.

* Une ressource Cognitive Services ou Form Recognizer. Une fois que vous avez votre abonnement Azure, créez une ressource Form Recognizer [monoservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

  > [!TIP]
  > Créez une ressource Cognitive Services si vous envisagez d’accéder à plusieurs services Cognitive Services sous un seul point de terminaison/clé. Pour l’accès à Form Recognizer uniquement, créez une ressource Form Recognizer. Notez que vous avez besoin d’une ressource monoservice si vous avez l’intention d’utiliser l’[authentification Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Après le déploiement de votre ressource, sélectionnez **Accéder à la ressource**. Vous avez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Form Recognizer. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans ce guide de démarrage rapide :

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : clés et emplacement du point de terminaison dans le portail Azure":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Sélectionnez un exemple de code à copier-coller dans votre application :

* [**Layout**](#try-it-layout-model)

* [**Facture prédéfinie**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. En production, utilisez des méthodes sécurisées pour stocker vos informations d’identification et y accéder. Pour plus d’informations, consultez l’article sur la [sécurité](/azure/cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-layout-model"></a>**Essayez** : modèle de disposition

> [!div class="checklist"]
>
> * Pour cet exemple, vous aurez besoin d’un **fichier de formulaire au niveau d’un URI**. Vous pouvez utiliser notre [exemple de formulaire](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) pour ce guide de démarrage rapide.

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `\"{your-document-url}` par un exemple d’URL de formulaire :

```http
https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf
```

#### <a name="request"></a>Requête

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{ID-résultats}** .

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` dans l’URL correspond à l’ID des résultats.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Obtenir les résultats de la disposition

Après avoir appelé l’API **[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** (Analyser la disposition), vous appelez l’API **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** (Obtenir le résultat de l’analyse de la disposition) pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Requête

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Examiner les résultats

Vous recevez une réponse `200 (success)` avec du contenu JSON.

Regardez l’image de facture suivante et sa sortie JSON correspondante.

* Le nœud `"readResults"` contient chaque ligne de texte avec sa position de cadre englobant respectif dans la page.
* Le nœud `selectionMarks` affiche chaque marque de sélection (case à cocher ou case d’option) et indique si son état est « sélectionné » ou « non sélectionné ».
* La section `"pageResults"` comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Document d’énoncé de projet Contoso avec un tableau.":::

#### <a name="response-body"></a>Response body

Vous pouvez consulter l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

## <a name="try-it-prebuilt-model"></a>**Essayez** : Modèle prédéfini

> [!div class="checklist"]
>
> * Pour cet exemple, nous analysons un document de facture à l’aide d’un modèle prédéfini. Vous pouvez utiliser notre [exemple de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) pour ce guide de démarrage rapide.

### <a name="choose-a-prebuilt-model"></a>Choisir un modèle prédéfini

Vous n’êtes pas limité aux factures – Vous avez le choix entre plusieurs modèles prédéfinis, chacun ayant son propre ensemble de champs pris en charge. Le modèle à utiliser pour l’opération d’analyse dépend du type de document à analyser. Voici les modèles prédéfinis actuellement pris en charge par le service Form Recognizer :

* [**Facture**](../../concept-invoice.md) : extrait le texte, les marques de sélection, les tableaux, les champs et les informations clés des factures.
* [**Reçu**](../../concept-receipt.md) : extrait le texte et les informations clés des reçus.
* [**Document d’identité**](../../concept-id-document.md) : extrait le texte et les informations clés des permis de conduire et des passeports internationaux.
* [**Carte de visite**](../../concept-business-card.md) : extrait le texte et les informations clés des cartes de visite.

Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.
1. Remplacez `{subscription key}` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `\"{your-document-url}` par un exemple d’URL de facture :

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>Requête

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Vous recevrez une réponse `202 (Success)` incluant un en-tête **Operation-Location**. La valeur de cet en-tête contient un ID de résultats qui vous permet d’interroger l’état de l’opération asynchrone et d’obtenir les résultats :

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{ID-résultats}**_

Dans l’exemple suivant, la chaîne qui suit `analyzeResults/` dans l’URL correspond à l’ID des résultats :

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Obtenir les résultats de la facture

Après avoir appelé l’API **Analyser une facture**, vous appelez l’API **[Obtenir le résultat de l’analyse de la facture](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** pour obtenir l’état de l’opération et les données extraites. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `{endpoint}` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `{resultId}` par l’ID de résultats de l’étape précédente.
1. Remplacez `{subscription key}` par votre clé d’abonnement.

#### <a name="request"></a>Requête

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examiner la réponse

Vous recevez une réponse `200 (Success)` avec une sortie JSON.

* Le champ `"readResults"` contient chaque ligne de texte extraite de la facture.
* Le champ `"pageResults"` comprend les tableaux et les marques de sélection extraits de la facture.
* Le champ `"documentResults"` contient les informations relatives aux paires clé/valeur pour les parties les plus pertinentes de la facture.

Consultez le document [Exemple de facture](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf).

#### <a name="response-body"></a>Response body

Consultez l’[exemple de sortie complet sur GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Dans ce guide de démarrage rapide, vous avez utilisé l’API REST Form Recognizer pour analyser divers formulaires de différentes manières. Explorez à présent la documentation de référence pour en savoir plus sur l’API Form Recognizer v3.0.

> [!div class="nextstepaction"]
> [Documentation de référence de l’API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
