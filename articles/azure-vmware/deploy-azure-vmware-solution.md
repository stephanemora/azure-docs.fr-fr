---
title: Déployer et configurer Azure VMware Solution
description: Découvrez comment utiliser les informations collectées au cours de la phase de planification pour déployer et configurer le cloud privé Azure VMware Solution.
ms.topic: tutorial
ms.custom: contperf-fy22q1, devx-track-azurecli
ms.date: 07/28/2021
ms.openlocfilehash: 0df1634d047dfe6abfaa717fd10b75b99f100076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623928"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Déployer et configurer Azure VMware Solution

Une fois que vous avez [planifié votre déploiement](plan-private-cloud-deployment.md), déployez et configurez votre cloud privé Azure VMware Solution. 

Le diagramme illustre le flux de travail de déploiement d’Azure VMware Solution. 

:::image type="content" source="media/deploy-azure-vmware-solution-workflow.png" alt-text="Diagramme montrant le workflow de déploiement d’Azure VMware Solution." lightbox="media/deploy-azure-vmware-solution-workflow.png" border="false":::

Dans ce guide pratique, vous allez :

> [!div class="checklist"]
> * inscrire le fournisseur de ressources et créer un cloud privé
> * vous connecter à une passerelle de réseau virtuel ExpressRoute nouvelle ou existante
> * valider la configuration du réseau

Une fois que vous avez terminé, suivez les étapes suivantes recommandées à la fin pour continuer avec les étapes de ce guide de démarrage.

## <a name="register-the-microsoftavs-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.AVS

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="create-an-azure-vmware-solution-private-cloud"></a>Créer un cloud privé Azure VMware Solution

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Pour une vue d’ensemble de bout en bout de cette étape, consultez la vidéo [Azure VMware Solution : Déploiement](https://www.youtube.com/embed/gng7JjxgayI).


## <a name="connect-to-azure-virtual-network-with-expressroute"></a>Se connecter à un réseau virtuel Azure avec ExpressRoute

Pendant la phase de planification, vous avez décidé d’utiliser une passerelle de réseau virtuel ExpressRoute *existante* ou *nouvelle*.  

:::image type="content" source="media/connect-expressroute-vnet-workflow.png" alt-text="Diagramme montrant le flux de travail pour connecter Réseau virtuel Microsoft Azure à ExpressRoute dans Azure VMware Solution." border="false":::

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>Utiliser une nouvelle passerelle de réseau virtuel ExpressRoute

>[!IMPORTANT]
>Vous devez disposer d’un réseau virtuel avec un GatewaySubnet qui **n’a pas** encore de passerelle de réseau virtuel.

| Si | Alors  |
| --- | --- |
| Vous n’avez pas encore de réseau virtuel...     |  Créez ceci :<ol><li><a href="tutorial-configure-networking.md#create-a-vnet-manually">Réseau virtuel</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">Passerelle de réseau virtuel</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">Connecter ExpressRoute à la passerelle</a></li></ol>        |
| Vous avez déjà un réseau virtuel **sans** GatewaySubnet...   | Créez ceci : <ol><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">Passerelle de réseau virtuel</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">Connecter ExpressRoute à la passerelle</a></li></ol>          |
| Vous avez déjà un réseau virtuel **avec** un GatewaySubnet... | Créez ceci : <ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">Passerelle de réseau virtuel</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">Connecter ExpressRoute à la passerelle</a></li></ol>    |

### <a name="use-an-existing-virtual-network-gateway"></a>Utiliser une passerelle de réseau virtuel existante

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="validate-the-connection"></a>Valider la connexion

Vous devez avoir une connexion entre le réseau virtuel Azure sur lequel ExpressRoute s’arrête et le cloud privé Azure VMware Solution. 

1. Utilisez une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) au sein du réseau virtuel Azure sur lequel ExpressRoute Azure VMware Solution s’arrête. Pour plus d’informations, consultez [Se connecter à un réseau virtuel Azure avec ExpressRoute](#connect-to-azure-virtual-network-with-expressroute).  

   1. Connectez-vous au [portail](https://portal.azure.com) Azure.

   1. Accédez à une machine virtuelle en cours d’exécution et, sous **Paramètres**, sélectionnez **Réseau**, puis la ressource de l’interface réseau.

      :::image type="content" source="../virtual-network/media/diagnose-network-routing-problem/view-nics.png" alt-text="Capture d’écran montrant les paramètres de l’interface réseau virtuelle.":::

   1. Sur la gauche, sélectionnez **Itinéraires effectifs**. Vous allez voir la liste des préfixes d’adresse contenus dans le bloc CIDR `/22` que vous avez entré pendant la phase de déploiement.

1. Pour vous connecter à vCenter et à NSX-T Manager, ouvrez un navigateur web et connectez-vous à la même machine virtuelle que celle utilisée pour la validation de la route réseau.  

   Vous pouvez identifier les adresses IP et les informations d’identification de vCenter et de la console NSX-T Manager dans le portail Azure.  Sélectionnez votre cloud privé, puis **Gérer** > **Identité**.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Capture d’écran montrant des URL et informations d’identification vCenter et NSX Manager du cloud privé." border="true":::


## <a name="next-steps"></a>Étapes suivantes

Dans le tutoriel suivant, vous allez connecter Azure VMware Solution à votre réseau local via ExpressRoute.

> [!div class="nextstepaction"]
> [Se connecter à votre environnement local](tutorial-expressroute-global-reach-private-cloud.md)
