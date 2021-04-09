---
title: Balisage d’une machine virtuelle avec le portail Azure
description: En savoir plus sur le balisage d’une machine virtuelle à l’aide du portail Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897406"
---
# <a name="tagging-a-vm-using-the-portal"></a>Balisage d’une machine virtuelle à l’aide du portail

Cet article explique comment ajouter des balises à une machine virtuelle à l’aide du portail. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 50 étiquettes par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante.


1. Accédez à votre machine virtuelle dans le portail.
1. Dans **Essentials**, sélectionnez **Cliquer ici pour ajouter des balises**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Capture d’écran de la section Essentials sur la page de la machine virtuelle.":::

1. Spécifiez une valeur pour les champs **Nom** et **Valeur**, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Capture d’écran de la page d’ajout d’une paire de valeurs de clés en tant que balise.":::

### <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le balisage de vos ressources Azure, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/management/overview.md) et [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/management/tag-resources.md).
- Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure](../cost-management-billing/understand/review-individual-bill.md).
