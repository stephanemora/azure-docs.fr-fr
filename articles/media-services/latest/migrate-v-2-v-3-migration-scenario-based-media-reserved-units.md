---
title: Recommandations en matière de migration basées sur un scénario d’unités réservées Multimédia | Microsoft Docs
description: Cet article fournit des recommandations basées sur un scénario d’unités réservées Multimédia, qui vous aideront à opérer une migration de la version V2 à la version V3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 1a6bcf5d75d5331875e38e473f115f32737e42a4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689427"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Recommandations en matière de migration basées sur un scénario d’unités réservées Multimédia

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des recommandations basées sur un scénario d’unités réservées Multimédia, qui vous aideront à opérer une migration de la version V2 à la version V3 d’Azure Media Services.

- Pour les nouveaux comptes v3 créés dans le portail Azure, ou avec la version 2020-05-01 de l’API V3, vous n’avez plus besoin de définir d’unités réservées Multimédia. Le système effectue un scale-up et un scale-down automatiquement en fonction de la charge.
- Si vous avez un compte V3 ou V2 créé avant la version 2020-05-01 de l’API, vous pouvez toujours contrôler la concurrence et les performances de vos travaux à l’aide d’unités réservées Multimédia. Pour plus d’informations, consultez Mise à l’échelle du traitement multimédia. Vous pouvez gérer les unités réservées Multimédia à l’aide de CLI 2.0 pour Media Services V3 ou du portail Azure.  
- Si vous ne voyez pas l’option permettant de gérer les unités réservées Multimédia dans le portail Azure, cela signifie que vous exécutez un compte créé avec l’API 2020-05-01 ou version ultérieure.
- Si vous êtes familiarisé avec la définition de votre type d’unité réservée Multimédia sur S3, vos performances s’amélioreront ou resteront les mêmes.
- Si vous êtes un client V2 existant, vous devez créer un compte V2 pour prendre en charge votre application existante avant de terminer la migration. 
- Vous devrez peut-être réactiver l’indexeur V1 ou d’autres processeurs multimédias qui ne sont pas encore entièrement déconseillés. 

Pour plus d’informations sur les unités réservées Multimédia, consultez [Unités réservées Multimédia](concept-media-reserved-units.md) et [Mise à l'échelle des unités réservées Multimédia](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques liés aux unités réservées Multimédia

### <a name="concepts"></a>Concepts

[Unités réservées Multimédia](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guides pratiques

[Mise à l'échelle des unités réservées Multimédia](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
