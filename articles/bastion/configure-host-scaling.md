---
title: Ajouter des unités d’échelle pour la mise à l’échelle de l’hôte
titleSuffix: Azure Bastion
description: Découvrez comment ajouter des instances supplémentaires (unités d’échelle) à Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: d5087994a72b52ce091dac98987b8e0bc60e287e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224737"
---
# <a name="configure-host-scaling-preview"></a>Configurer la mise à l’échelle de l’hôte (préversion)

Cet article vous aide à ajouter des unités d’échelle (instances) supplémentaires à Azure Bastion afin de prendre en charge des connexions clientes simultanées supplémentaires. Dans la préversion, ce paramètre ne peut être configuré que dans le Portail Azure. Pour plus d’informations sur la mise à l’échelle des hôtes, consultez [Paramètres de configuration](configuration-settings.md#instance). 

## <a name="configuration-steps"></a>Étapes de configuration

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com).
1. Accédez à votre hôte Bastion dans le Portail Azure.
1. Le nombre d’instances de mise à l’échelle d’hôte requiert le niveau standard. Sur la page **Configuration**, vérifiez que la valeur du champ **Niveau** est bien **Standard**. Si le niveau est le niveau de base, sélectionnez **Standard** dans la liste déroulante. 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="Capture d’écran de la sélection du niveau." lightbox="./media/configure-host-scaling/select-sku.png":::
1. Pour configurer la mise à l’échelle, ajustez le nombre d’instances. Chaque instance correspond à une unité d’échelle.

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="Capture d’écran du curseur du nombre d’instances." lightbox="./media/configure-host-scaling/instance-count.png":::
1. Cliquez sur **Appliquer** pour appliquer les modifications.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations, lisez les [questions fréquentes (FAQ) sur Bastion](bastion-faq.md).
