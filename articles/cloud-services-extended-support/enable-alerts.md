---
title: Activer la surveillance dans des services cloud (support étendu) à l’aide du portail Azure
description: Activer la surveillance pour des instances de services cloud (support étendu) à l’aide du portail Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743952"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Activer la surveillance pour des services cloud (support étendu) à l’aide du portail Azure

Cet article explique comment activer des alertes sur des déploiements de services cloud existants (support étendu). 

## <a name="add-monitoring-rules"></a>Ajouter des règles d’analyse
1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Sélectionnez le déploiement de service cloud (support étendu) pour lequel vous souhaitez activer des alertes. 
3. Sélectionnez le panneau **Alertes**. 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="L’image affiche la sélection de l’onglet Alertes dans le portail Azure.":::

4. Sélectionnez l’icône **Nouvelle alerte**.
     :::image type="content" source="media/enable-alerts-2.png" alt-text="L’image affiche la sélection de l’option Ajouter une nouvelle alerte.":::

5. Entrez les conditions souhaitées et les actions requises en fonction des métriques que vous souhaitez suivre. Vous pouvez définir les règles en fonction de métriques individuelles ou du journal d’activité. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="L’image indique où ajouter des conditions aux alertes.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="L’image affiche la configuration de la logique du signal.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="L’image affiche la configuration de la logique du groupe d’actions.":::

6. Une fois que vous avez fini de configurer des alertes, enregistrez les modifications et, en fonction des métriques configurées, des alertes apparaîtront au fur et à mesure dans le panneau **Alertes**.

## <a name="next-steps"></a>Étapes suivantes 
- Consultez la [FAQ](faq.md) de Cloud Services (support étendu).
- Déployez une instance Cloud Services (support étendu) avec le [Portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).