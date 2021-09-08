---
title: Aide sur la migration avec unités réservées Multimédia
description: Cet article fournit des recommandations basées sur un scénario d’unités réservées Multimédia, qui vous aideront à opérer une migration de la version V2 à la version V3 d’Azure Media Services.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 01d62055de8e8327b518bc3638cab85426917247
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864640"
---
# <a name="media-reserved-units-migration-guidance"></a>Aide sur la migration avec unités réservées Multimédia

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des recommandations basées sur un scénario d’unités réservées Multimédia, qui vous aideront à opérer une migration de la version V2 à la version V3 d’Azure Media Services.

> [!Important]
> Les unités réservées Multimédia ne sont plus nécessaires pour les comptes Media Services, car le système effectue automatiquement un scale-up ou un scale-down en fonction de la charge. 

## <a name="scenario-guidance"></a>Guide pour le scénario

Procédez à la migration de vos unités réservées Multimédias en fonction des scénarios suivants :

* Pour tous les comptes Media Services, vous n’avez plus besoin de définir des unités réservées Multimédias. Le système effectue un scale-up et un scale-down automatiquement en fonction de la charge.
* Si vous avez un compte qui a été créé à l’aide d’une version antérieure à l’API 2020-05-01, vous avez toujours accès à l’API pour la gestion des unités réservées Multimédias. Toutefois, aucune des configurations d’unités réservées Multimédias que vous définissez ne sera utilisée pour contrôler la concurrence ou les performances d’encodage. Pour plus d’informations, consultez [Mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md). Vous pouvez gérer les unités réservées Multimédia à l’aide de CLI 2.0 pour Media Services V3 ou du portail Azure.
* Si vous ne voyez pas l’option permettant de gérer les unités réservées Multimédia dans le portail Azure, cela signifie que vous exécutez un compte créé avec l’API 2020-05-01 ou version ultérieure.
* Si vous êtes familiarisé avec la définition de votre type d’unité réservée Multimédia sur S3, vos performances s’amélioreront ou resteront les mêmes avec la suppression des unités réservées multimédias.
* Si vous êtes un client V2 existant, vous devez créer un compte V3 pour prendre en charge votre application existante avant de terminer la migration. 
* Vous devrez peut-être réactiver l’indexeur V1 ou d’autres processeurs multimédias qui ne sont pas encore entièrement déconseillés. 

Pour plus d’informations sur les unités réservées Multimédia, consultez [Unités réservées Multimédia](concept-media-reserved-units.md) et [Mise à l'échelle des unités réservées Multimédia](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques liés aux unités réservées Multimédia

### <a name="concepts"></a>Concepts

[Unités réservées Multimédia](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guides pratiques

[Mise à l'échelle des unités réservées Multimédia](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).
