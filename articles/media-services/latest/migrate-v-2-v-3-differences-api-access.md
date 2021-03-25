---
title: Différences d’accès à l’API entre Media Services v2 et v3
description: Cet article décrit les différences d’accès à l’API entre Azure Media Services v2 et v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 831bc737fbd54685cdc8358642bc37e7b2df2c42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953714"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Différences d’accès à l’API entre Azure Media Services v2 et v3

![Logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Étape 2 de la migration](./media/migration-guide/steps-2.svg)

Cet article décrit les différences d’accès à l’API entre Azure Media Services v2 et v3.

## <a name="api-access"></a>Accès à l’interface de programmation d’applications

Tous les comptes Media Services auront accès à l’API v3. Toutefois, nous recommandons fortement d’effectuer le développement de la migration sur un nouveau compte avant d’appliquer du code mis à jour à un compte v2 existant. En effet, les entités v3 ne sont pas à compatibilité descendante avec la v2. Certaines entités v2 comme les ressources sont en revanche à compatibilité ascendante avec la v3.
Vous pouvez continuer à utiliser des comptes existants si vous ne mélangez pas les API v2 et v3, puis essayer de revenir à v2, mais cette façon de faire est déconseillé.

L’accès à l’API v2 sera disponible jusqu’à sa mise hors service en 2024.

Pendant la migration, vous pouvez créer un compte v3 qui a toujours accès à la v2.  Il existe plusieurs moyens de créer un compte :

- Utiliser l’API REST et une version antérieure
- Cocher la case sur le portail

> [!div class="mx-imgBorder"]
> [ ![Création de compte sur le portail](./media/migration-guide/v-3-v-2-access-account-creation-small.png) ](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Tous les kits de développement logiciel (SDK) .NET, CLI et autres cibleront la dernière API 2020-05-01. Recherchez ou configurez les anciennes versions de l’API.

> [!NOTE]
> Les nouveaux comptes créés avec l’API 2020-05-01 ne peuvent pas utiliser les API v2.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
