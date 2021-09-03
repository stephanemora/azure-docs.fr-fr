---
title: Client d’ingestion - service vocal
titleSuffix: Azure Cognitive Services
description: Dans cet article, nous décrivons un outil publié sur GitHub qui permet aux clients d’envoyer facilement et rapidement des fichiers audio vers le Service vocal.
services: cognitive-services
author: panosperiorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 069c0b587fd3ff69ca8e883850d44b84ab12486f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532412"
---
# <a name="ingestion-client-for-the-speech-service"></a>Client d’ingestion pour le service vocal

Le Client d’ingestion est un outil publié sur [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/ingestion) qui permet aux clients de transcrire rapidement des fichiers audio par le biais de services vocaux, avec peu ou pas d’effort de développement. Il fonctionne en connectant un compte de [stockage Azure](https://azure.microsoft.com/product-categories/storage/) dédié à des fonctions[Azure Functions](https://azure.microsoft.com/services/functions/) personnalisées qui utilisent l'[API REST](rest-speech-to-text.md) ou le [SDK](speech-sdk.md) sans serveur pour transmettre les demandes de transcription au service.  

## <a name="architecture"></a>Architecture

L’outil aide les clients qui souhaitent avoir une idée de la qualité de la transcription sans faire face aux investissements de développement. L’outil connecte quelques ressources pour transcrire les fichiers audio contenus dans le [conteneur stockage Azure](https://azure.microsoft.com/product-categories/storage/) dédié.

En interne, l’outil utilise l’API ou le kit de développement logiciel (SDK) V 3.0 batch et suit les meilleures pratiques pour gérer la montée en puissance, les nouvelles tentatives et le basculement. Le schéma suivant décrit les ressources et les connexions.

:::image type="content" source="media/ingestion-client/architecture-1.png" alt-text="Architecture du client d’ingestion.":::

Le [Guide de démarrage du client d'ingestion](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md) décrit comment configurer et utiliser l'outil.

> [!IMPORTANT]
> La tarification varie en fonction du mode d’opération (traitement par lots ou temps réel), ainsi que de la référence SKU Azure Function sélectionnée. Par défaut, l’outil crée une référence (SKU) de fonction Azure Premium pour gérer un volume important. Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/functions/).

Le [kit SDK Microsoft Speech](speech-sdk.md) et [l'API REST Speech-to-text v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) peuvent tous deux être utilisés pour obtenir des transcriptions. La décision influe sur les coûts globaux, comme expliqué dans le guide. 

> [!TIP]
> Vous pouvez utiliser l’outil et la solution obtenue en production pour traiter un volume élevé de données audio.

## <a name="tool-customization"></a>Personnalisation des outils

L’outil est conçu pour afficher rapidement les résultats des clients. Vous pouvez personnaliser l’outil sur vos références SKU préférées et le programme d’installation. Les références SKU peuvent être modifiés à partir du [portail Azure](https://portal.azure.com) et le [code lui-même est disponible sur GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch).

> [!NOTE]
> Nous vous suggérons de créer les ressources dans le même groupe de ressources dédié pour comprendre et suivre les coûts plus facilement.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [En savoir plus sur le Kit SDK Speech](./speech-sdk.md)
* [En savoir plus sur l’outil CLI Speech](./spx-overview.md)
