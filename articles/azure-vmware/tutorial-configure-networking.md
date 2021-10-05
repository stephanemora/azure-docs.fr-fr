---
title: Tutoriel - Configurer le réseau pour votre cloud privé VMware dans Azure
description: Découvrir comment créer et configurer le réseau nécessaire au déploiement de votre cloud privé dans Azure
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/30/2021
ms.openlocfilehash: 99389c55ab13b6c6c181a4d7d1bcf14c3b8ba08d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597794"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutoriel : Configurer le réseau pour votre cloud privé VMware dans Azure

Un cloud privé Azure VMware Solution nécessite un réseau virtuel Azure. Comme Azure VMware Solution ne prend pas en charge votre vCenter local, des étapes supplémentaires sont requises pour l’intégration avec votre environnement local. La configuration d’un circuit ExpressRoute et d’une passerelle de réseau virtuel est également nécessaire.

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un réseau virtuel 
> * Créer une passerelle de réseau virtuel
> * Connecter votre circuit ExpressRoute à la passerelle

>[!NOTE]
>Avant de créer un nouveau réseau virtuel, évaluez si vous disposez déjà d’un réseau virtuel dans Azure et envisagez de l’utiliser pour vous connecter à Azure VMware Solution, ou si vous souhaitez créer un nouveau réseau virtuel entièrement.  
>* Pour utiliser un réseau virtuel existant dans le même abonnement Azure que celui d’Azure VMware Solution, utilisez l’onglet **[Connexion à Azure vNet](#select-an-existing-vnet)** sous **Connectivité**. 
>* Pour utiliser un réseau virtuel existant dans un autre abonnement Azure que celui d’Azure VMware Solution, utilisez les conseils de **[Se connecter au cloud privé manuellement](#connect-to-the-private-cloud-manually)** . 
>* Pour créer un nouveau réseau virtuel dans le même abonnement Azure que celui d’Azure VMware Solution, utilisez l’onglet **[Connexion à Azure vNet](#create-a-new-vnet)** ou créez-en un [manuellement](#create-a-vnet-manually).

## <a name="connect-with-the-azure-vnet-connect-feature"></a>Se connecter avec la fonctionnalité Azure vNet Connect

Vous pouvez utiliser la fonctionnalité **Azure vNet Connect** pour utiliser un réseau virtuel existant ou créer un nouveau réseau virtuel pour vous connecter à Azure VMware Solution.   

>[!NOTE]
>L’espace d’adressage du réseau virtuel ne peut pas se chevaucher avec le CIDR du cloud privé d’Azure VMware Solution.


### <a name="select-an-existing-vnet"></a>Sélectionner un réseau virtuel existant

Lorsque vous sélectionnez un réseau virtuel existant, le modèle Azure Resource Manager (ARM) qui crée le réseau virtuel et d’autres ressources est redéployé. Les ressources, dans ce cas, correspondent à l’adresse IP publique, la passerelle, la connexion de passerelle et la clé d’autorisation ExpressRoute. Si tout est configuré, le déploiement ne changera rien. Toutefois, si quelque chose manque, cet élément est créé automatiquement. Par exemple, si le GatewaySubnet est manquant, il est ajouté au cours du déploiement.

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Connectivité**.

2. Sélectionnez l’onglet **Azure vNet Connect**, puis le réseau virtuel existant.

   :::image type="content" source="media/networking/azure-vnet-connect-tab.png" alt-text="Capture d’écran montrant l’onglet Azure vNet Connect sous Connectivité avec un réseau virtuel existant sélectionné.":::

3. Sélectionnez **Enregistrer**.

   À ce stade, le réseau virtuel valide si des espaces d’adressage IP qui se chevauchent entre Azure VMware Solution et le réseau virtuel sont détectés. En cas de détection, modifiez l’adresse réseau du cloud privé ou du réseau virtuel pour que les adresses ne se chevauchent pas. 


### <a name="create-a-new-vnet"></a>Créer un nouveau réseau virtuel

Lorsque vous créez un réseau virtuel, les composants requis pour se connecter à Azure VMware Solution sont créés automatiquement.

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Connectivité**.

2. Sélectionnez l’onglet **Azure vNet Connect**, puis **Créer**.

   :::image type="content" source="media/networking/azure-vnet-connect-tab-create-new.png" alt-text="Capture d’écran montrant l’onglet Conexion au réseau virtuel Azure sous Connectivité.":::

3. Fournissez ou mettez à jour les informations du nouveau réseau virtuel, puis sélectionnez **OK**.

   À ce stade, le réseau virtuel valide si des espaces d’adressage IP qui se chevauchent entre Azure VMware Solution et le réseau virtuel sont détectés. En cas de détection, modifiez l’adresse du cloud privé ou du réseau virtuel pour que les adresses ne se chevauchent pas. 

   :::image type="content" source="media/networking/create-new-virtual-network.png" alt-text="Capture d’écran montrant la fenêtre Créer un réseau virtuel.":::

Le réseau virtuel avec la plage d’adresses et le GatewaySubnet fournis est créé dans votre abonnement et votre groupe de ressources.  


## <a name="connect-to-the-private-cloud-manually"></a>Se connecter au cloud privé manuellement

### <a name="create-a-vnet-manually"></a>Créer un réseau virtuel manuellement

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez au groupe de ressources que vous avez créé dans le cadre du [tutoriel Créer un cloud privé](tutorial-create-private-cloud.md), puis sélectionnez **+ Ajouter** pour définir une nouvelle ressource. 

1. Dans la zone de texte **Rechercher dans la Place de marché**, tapez **Réseau virtuel**. Recherchez la ressource Réseau virtuel et sélectionnez-la.

1. Dans la page **Réseau virtuel**, sélectionnez **Créer** pour configurer votre réseau virtuel pour votre cloud privé.

1. Dans la page **Créer un réseau virtuel**, entrez les détails de votre réseau virtuel.

1. Sous l’onglet **Informations de base**, entrez un nom pour le réseau virtuel et sélectionnez la région appropriée, puis sélectionnez **Suivant : Adresses IP**.

1. Sous l’onglet **Adresses IP**, sous **Espace d’adressage IPv4**, entrez l’espace d’adressage que vous avez créé dans le tutoriel précédent.

   > [!IMPORTANT]
   > Vous devez utiliser un espace d’adressage qui **ne chevauche pas** l’espace d’adressage que vous avez utilisé lors de la création de votre cloud privé dans le tutoriel précédent.

1. Sélectionnez **+ Ajouter un sous-réseau** puis, dans la page **Ajouter un sous-réseau**, spécifiez un nom et une plage d’adresses appropriée pour le sous-réseau. Quand vous avez terminé, sélectionnez **Ajouter**.

1. Sélectionnez **Revoir + créer**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Capture d’écran montrant les paramètres du nouveau réseau virtuel." border="true":::

1. Vérifiez les informations et sélectionnez **Créer**. Une fois le déploiement terminé, vous voyez votre réseau virtuel dans le groupe de ressources.



### <a name="create-a-virtual-network-gateway"></a>Créer une passerelle de réseau virtuel

À présent que vous avez créé un réseau virtuel, vous allez créer une passerelle de réseau virtuel.

1. Dans votre groupe de ressources, sélectionnez **+ Ajouter** pour ajouter une nouvelle ressource.

1. Dans la zone de texte **Rechercher dans la Place de marché**, tapez **Passerelle de réseau virtuel**. Recherchez la ressource Réseau virtuel et sélectionnez-la.

1. Dans la page **Passerelle de réseau virtuel**, sélectionnez **Créer**.

1. Sous l’onglet De base de la page **Créer une passerelle de réseau virtuel**, spécifiez des valeurs pour les champs, puis sélectionnez **Vérifier + créer**. 

   | Champ | Valeur |
   | --- | --- |
   | **Abonnement** | Une valeur déjà renseignée avec l’abonnement auquel appartient le groupe de ressources. |
   | **Groupe de ressources** | Une valeur déjà renseignée pour le groupe de ressources actuel. La valeur doit être le groupe de ressources que vous avez créé dans un test précédent. |
   | **Nom** | Entrez un nom unique pour la passerelle de réseau virtuel. |
   | **Région** | Sélectionnez l’emplacement géographique de la passerelle de réseau virtuel. |
   | **Type de passerelle** | Sélectionnez **ExpressRoute**. |
   | **Référence (SKU)** | Laissez la valeur par défaut : **Standard**. |
   | **Réseau virtuel** | Sélectionnez le réseau virtuel que vous avez créé précédemment. Si vous ne voyez pas le réseau virtuel, vérifiez que la région de la passerelle correspond à la région de votre réseau virtuel. |
   | **Plage d’adresses du sous-réseau de passerelle** | Cette valeur est renseignée quand vous sélectionnez le réseau virtuel. Ne modifiez pas la valeur par défaut. |
   | **Adresse IP publique** | Sélectionnez **Créer nouveau**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Capture d’écran montrant les détails de la passerelle de réseau virtuel." border="true":::

1. Vérifiez que les détails sont corrects, puis sélectionnez **Créer** pour démarrer le déploiement de votre passerelle de réseau virtuel.

1. Une fois le déploiement terminé, passez à la section suivante pour connecter votre connexion ExpressRoute à la passerelle de réseau virtuel contenant votre cloud privé Azure VMware Solution.

### <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Connecter ExpressRoute à la passerelle de réseau virtuel

À présent que vous avez déployé une passerelle de réseau virtuel, vous allez ajouter une connexion entre celle-ci et votre cloud privé Azure VMware Solution.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un réseau virtuel à l’aide de la fonctionnalité vNet Connect
> * Créer un réseau virtuel manuellement
> * Créer une passerelle de réseau virtuel
> * Connecter votre circuit ExpressRoute à la passerelle


Passez au didacticiel suivant pour apprendre à créer les segments de réseau NSX-T utilisés pour les machines virtuelles dans vCenter.

> [!div class="nextstepaction"]
> [Créer un segment réseau NSX-T](./tutorial-nsx-t-network-segment.md)
