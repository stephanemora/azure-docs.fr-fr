---
title: Mettre à niveau un SKU
titleSuffix: Azure Bastion
description: Découvrez comment passer des niveaux de base à la référence SKU standard.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: 5ed0dd6ad86b5f7758f0e1972867876eedc3b51a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225939"
---
# <a name="upgrade-a-sku-preview"></a>Mettre à niveau une référence SKU

Cet article vous aide à effectuer la mise à niveau du niveau de base (SKU) vers le niveau standard. Une fois la mise à niveau effectuée, vous ne pouvez plus revenir à la référence SKU de base sans supprimer et reconfigurer Bastion. Dans la préversion, ce paramètre ne peut être configuré que dans le portail Azure. Pour plus d’informations sur la mise à l’échelle des hôtes, consultez [Paramètres de configuration – Références SKU](configuration-settings.md#skus). 

## <a name="configuration-steps"></a>Étapes de configuration

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com).
1. Accédez à votre hôte Bastion dans le portail Azure.
1. Sur la page **Configuration**, pour **Niveau**, sélectionnez **Standard** dans le menu déroulant.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="Capture d’écran de la liste déroulante Sélectionner le niveau avec l’option standard sélectionnée." lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. Cliquez sur **Appliquer** pour appliquer les modifications.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la configuration, consultez [Paramètres de configuration](configuration-settings.md).
* Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
