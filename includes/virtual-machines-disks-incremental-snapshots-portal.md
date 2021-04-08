---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e207866b61d21334bc9923d0d784b900906b0045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539721"
---
1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez au disque dont vous voulez prendre une capture instantanée.
1. Sur votre disque, sélectionnez **Créer un instantané**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Capture d’écran. Le panneau de votre disque, avec **+Créer un instantané** mis en surbrillance, car c’est ce que vous devez sélectionner.":::

1. Sélectionnez le groupe de ressources que vous souhaitez utiliser, puis entrez un nom.
1. Sélectionnez **Incrémentiel**, puis **Vérifier + créer**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Capture d’écran. Créez un panneau d’instantané, indiquez le nom, sélectionnez Incrémentiel, puis créez votre instantané.":::

1. Sélectionnez **Créer**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Capture d’écran. Page de validation de votre instantané, confirmez vos sélections, puis créez l’instantané.":::