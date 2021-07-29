---
title: Configurer des unités de traitement pour un espace de noms Azure Event Hubs Premium
description: Fournit des instructions sur la configuration des unités de traitement pour un espace de nom Event Hubs de niveau Premium.
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 443832c9fcd4ee8ce8e314a80251f2575aed003d
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631601"
---
# <a name="configure-processing-units-for-a-premium-tier-azure-event-hubs-namespace"></a>Configurer des unités de traitement pour un espace de noms Azure Event Hubs de niveau Premium
Cet article fournit des instructions sur la configuration des unités de traitement pour un espace de nom Azure Event Hubs de niveau Premium. Pour en savoir plus sur le niveau **Premium**, consultez [Niveau Premium d'Event Hubs](event-hubs-premium-overview.md).

## <a name="configure-processing-units-when-creating-a-namespace"></a>Configurer des unités de traitement lors de la création d’un espace de noms
Lorsque vous créez un espace de noms de niveau **Premium** dans le portail Azure, la fonctionnalité d’augmentation automatique est activée par défaut. Vous pouvez configurer le nombre d’unités de traitement pour votre espace de noms (1, 2, 4, 8 ou 16), que vous pouvez mettre à jour après avoir créé l’espace de noms. 

:::image type="content" source="./media/configure-processing-units-premium-namespace/event-hubs-auto-inflate-prem.png" alt-text="Capture d’écran de l’activation lors de la création d’un espace de noms Premium":::

## <a name="configure-processing-units-for-an-existing-namespace"></a>Configurer des unités de traitement pour un espace de noms
Pour mettre à jour le nombre de PU pour un espace de noms Premium existant, procédez comme suit : 

1. Sur la page **Espace de noms Event Hubs** pour votre espace de noms, accédez au menu de gauche et sélectionnez **Mettre à l'échelle** sous **Paramètres**. 
1. Mettez à jour la valeur des **unités de traitement**, puis sélectionnez **Enregistrer**. 

    :::image type="content" source="./media/configure-processing-units-premium-namespace/scale-settings-prem.png" alt-text="Capture d'écran illustrant l'activation de la fonctionnalité Majoration automatique pour un espace de noms premium existant":::

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les unités de traitement et le niveau premium d’Event Hubs, consultez les articles suivants.

- [Event Hubs Premium](event-hubs-premium-overview.md)
- [Évolutivité d’Event Hubs](event-hubs-scalability.md)
