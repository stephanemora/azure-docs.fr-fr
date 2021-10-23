---
title: Créer des machines virtuelles dans un groupe identique Flexible à l’aide du portail Azure
description: Découvrez comment créer un groupe de machines virtuelles identiques en mode d’orchestration Flexible dans le portail Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 9804b8cd773eec6df8a8b7a5b06e61ee110987b3
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163560"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Créer des machines virtuelles dans un groupe identique Flexible à l’aide du portail Azure

**S’applique à :** :heavy_check_mark: Groupes identiques flexibles

Cet article décrit l’utilisation du portail Azure pour créer un groupe identique de machines virtuelles mode d’orchestration Flexible. Pour plus d’informations sur les groupes identiques Flexibles, consultez  [mode d’orchestration Flexible pour les groupes identiques de machines virtuelles](flexible-virtual-machine-scale-sets.md). 


> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="get-started-with-flexible-orchestration-mode"></a>Démarrer avec le mode d’orchestration Flexible

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Créez un groupe de machines virtuelles identiques en mode d’orchestration Flexible avec le portail Azure.

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
1. Dans la barre de recherche, recherchez et sélectionnez **Groupes de machines virtuelles identiques**.
1. Sélectionnez **Créer** dans la page **Groupes de machines virtuelles identiques**.
1. Dans la page **Créer un groupe de machines virtuelles identiques**, consultez la section **Orchestration**.
1. Sous **Mode d’orchestration**, sélectionnez l’option **Flexible**.
1. Spécifiez la valeur **Nombre de domaines d’erreur**.
1. Terminez la création de votre groupe identique. Pour plus d’informations sur la création d’un groupe identique, consultez [Créer un groupe identique dans le portail Azure](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set).


### <a name="optional-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>(Facultatif) Ajoutez une machine virtuelle au groupe identique en mode d’orchestration Flexible.

1. Dans la barre de recherche, recherchez et sélectionnez **Machines virtuelles**.
1. Sélectionnez **Ajouter** dans la page **Machines virtuelles**.
1. Dans l’onglet **Informations de base**, consultez la section **Détails de l’instance**.
1. Ajoutez votre machine virtuelle au groupe identique en mode d’orchestration Flexible en sélectionnant le groupe identique sous **Options de disponibilité**. Vous pouvez ajouter la machine virtuelle à un groupe identique dans la même région, la même zone et le même groupe de ressources.
1. Accédez à l’onglet **Mise en réseau** et définissez votre connectivité sortante de manière explicite.

    > [!IMPORTANT]
    > Une connectivité sortante définie de manière explicite est requise pour les groupes de machines virtuelles identiques avec une orchestration flexible. Consultez la [connectivité réseau sortante explicite ](flexible-virtual-machine-scale-sets-migration-resources.md#explicit-network-outbound-connectivity-required) pour plus d’informations.

1. Terminez la création de votre machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez comment créer un groupe identique Flexible avec l’interface CLI Azure.](flexible-virtual-machine-scale-sets-cli.md)