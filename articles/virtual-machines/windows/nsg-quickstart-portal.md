---
title: Ouvrir des ports sur une machine virtuelle avec le Portail Azure
description: Apprenez à ouvrir un port et créer un point de terminaison sur votre machine virtuelle à l'aide du portail Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/27/2021
ms.author: cynthn
ms.openlocfilehash: 78fce318d07e8603830fe04b41df387e6a25f8d9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227240"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles 

[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

1. Recherchez et sélectionnez un groupe de ressources pour la machine virtuelle, choisissez **Ajouter**, puis recherchez et sélectionnez **Groupe de sécurité de réseau**.

1. Sélectionnez **Create** (Créer).

    La fenêtre **Créer un groupe de sécurité réseau** s’ouvre.

    :::image type="content" source="media/nsg-quickstart-portal/create-nsg.png" alt-text="Créer un groupe de sécurité réseau.":::

1. Entrez un nom pour votre groupe de sécurité réseau. 

1. Sélectionnez ou créez un groupe de ressources, puis sélectionnez un emplacement.

1. Sélectionnez **Créer** pour créer le groupe de sécurité réseau.

## <a name="create-an-inbound-security-rule"></a>Créer une règle de sécurité de trafic entrant

1. Sélectionnez votre nouveau groupe de sécurité réseau.

1. Sélectionnez **Règles de sécurité de trafic entrant** dans le menu gauche, puis sélectionnez **Ajouter**.

    :::image type="content" source="media/nsg-quickstart-portal/advanced.png" alt-text="Ajoutez une règle de sécurité de trafic entrant.":::

1. Vous pouvez limiter la **Source** et les **Plages de ports source** en fonction des besoins, ou conserver la valeur par défaut *Any*.
1. Vous pouvez limiter la **Destination** en fonction des besoins, ou conserver la valeur par défaut *Any*.
1. Choisissez un **Service** courant dans le menu déroulant, tel que **HTTP**. Vous pouvez aussi sélectionner **Personnalisé** si vous souhaitez indiquer un port spécifique à utiliser. 

1. Si vous le souhaitez, modifiez la **priorité** ou le **nom**. La priorité affecte l’ordre dans lequel les règles sont appliquées : plus la valeur numérique est faible, plus la règle est appliquée précocement.

1. Sélectionnez **Ajouter** pour créer la règle.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associer votre groupe de sécurité réseau à un sous-réseau

L’étape finale consiste à associer un sous-réseau ou une interface réseau spécifique à votre groupe de sécurité réseau. Pour cet exemple, nous associerons le groupe de sécurité réseau à un sous-réseau. 

1. Sélectionnez **Sous-réseaux** dans le menu gauche, puis sélectionnez **Associer**.

1. Sélectionnez votre réseau virtuel, puis sélectionnez le sous-réseau approprié.

    ![Association d’un groupe de sécurité réseau à un réseau virtuel](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Lorsque vous avez terminé, sélectionnez **OK**.

## <a name="additional-information"></a>Informations supplémentaires

Vous pouvez également [effectuer les étapes de cet article avec Azure PowerShell](nsg-quickstart-powershell.md).

Les commandes décrites dans cet article permettent de faire arriver rapidement le trafic entrant sur votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Pour plus d’informations, consultez [Filtrer le trafic réseau avec un groupe de sécurité réseau](../../virtual-network/tutorial-filter-network-traffic.md).

Pour les applications Web hautement disponibles, envisagez de placer vos machines virtuelles derrière un équilibreur de charge Azure. L’équilibreur de charge répartit le trafic entre les machines virtuelles, avec un groupe de sécurité réseau qui assure le filtrage du trafic. Pour plus d’informations, consultez [Équilibrer la charge des machines virtuelles Windows dans Azure pour créer une application hautement disponible](tutorial-load-balancer.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un groupe de sécurité réseau et une règle de trafic entrant qui autorise le trafic HTTP sur le port 80, que vous avez associée à un sous-réseau. 

Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :
- [Présentation d’Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Groupes de sécurité](../../virtual-network/network-security-groups-overview.md)
