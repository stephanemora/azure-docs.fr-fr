---
title: 'Démarrage rapide : Bibliothèque cliente Analyse de texte v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour connecter vos applications à l’API Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c6883b9b79adfc4f91af354147d45ea27d806d78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614495"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Démarrage rapide : Utiliser la bibliothèque de client Analyse de texte

Découvrez comment utiliser la bibliothèque de client Analyse de texte. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.

Utilisez la bibliothèque de client Analyse de texte dans le cadre des opérations suivantes :

* analyse de sentiments
* Détection de la langue
* Reconnaissance d’entité
* Extraction d’expressions clés

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * La dernière préversion de l’API Analyse de texte est `3.0-preview`. Elle inclut une préversion publique des fonctionnalités [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) et [Reconnaissance d’entité nommée (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) améliorées. La dernière version stable est la version `2.1`.
>    * Veillez à suivre uniquement les instructions concernant la version que vous utilisez.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Pour les scénarios de production, nous vous recommandons d’utiliser les méthodes asynchrones par lots afin d’optimiser les performances et l’extensibilité. Consultez la documentation de référence ci-dessous.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Ce guide de démarrage rapide concerne uniquement la version `3.0-preview` de la bibliothèque de client Analyse de texte, qui comprend une préversion publique des fonctionnalités [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) et [Reconnaissance d’entité nommée (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) améliorées.
>    * Veillez à suivre uniquement les instructions concernant la version que vous utilisez.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Pour les scénarios de production, nous vous recommandons d’utiliser les méthodes asynchrones par lots afin d’optimiser les performances et l’extensibilité. Consultez la documentation de référence ci-dessous.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * La dernière préversion de l’API Analyse de texte est `3.0-preview`. Elle inclut une préversion publique des fonctionnalités [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) et [Reconnaissance d’entité nommée (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) améliorées. La dernière version stable est la version `2.1`.
>    * Veillez à suivre uniquement les instructions concernant la version que vous utilisez.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Pour les scénarios de production, nous vous recommandons d’utiliser les méthodes asynchrones par lots afin d’optimiser les performances et l’extensibilité. Consultez la documentation de référence ci-dessous.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * La dernière préversion de l’API Analyse de texte est `3.0-preview`. Elle inclut une préversion publique des fonctionnalités [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) et [Reconnaissance d’entité nommée (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) améliorées. La dernière version stable est la version `2.1`.
>    * Veillez à suivre uniquement les instructions concernant la version que vous utilisez.
> * Le code indiqué dans cet article utilise des méthodes synchrones et un stockage d’informations d’identification non sécurisé pour des raisons de simplicité. Pour les scénarios de production, nous vous recommandons d’utiliser les méthodes asynchrones par lots afin d’optimiser les performances et l’extensibilité. Consultez la documentation de référence ci-dessous. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Prise en charge de langages supplémentaires

Si vous avez cliqué sur cet onglet, c’est que vous n’avez probablement pas vu de démarrage rapide dans votre langage de programmation favori. Ne vous inquiétez pas, nous disposons d’autres guides de démarrage rapide. Utilisez le tableau pour rechercher l’exemple convenant à votre langage de programmation.

| Langage | Version disponible | 
|----------|------------------------|
| Ruby     | [Version 2.1](ruby-sdk.md) | 
| Go       | [Version 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer une solution](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconnaissance d’entités](../how-tos/text-analytics-how-to-entity-linking.md)
* [Détecter la langue](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconnaissance de la langue](../how-tos/text-analytics-how-to-language-detection.md)
