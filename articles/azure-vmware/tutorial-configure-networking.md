---
title: Tutoriel - Configurer le réseau pour votre cloud privé VMware dans Azure
description: Découvrir comment créer et configurer le réseau nécessaire au déploiement de votre cloud privé dans Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 41043b98a6e270d8d9f4373de7876b3fcae86747
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837615"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutoriel : Configurer le réseau pour votre cloud privé VMware dans Azure

Un cloud privé AVS (Azure VMware Solution) nécessite un réseau virtuel. Comme la préversion d’AVS ne prend pas en charge vos vCenter locaux, des étapes supplémentaires pour l’intégration à votre environnement local sont nécessaires. La configuration d’un circuit ExpressRoute et d’une passerelle de réseau virtuel est également nécessaire : elle est traitée dans ce tutoriel.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Création d'un réseau virtuel
> * Créer une passerelle de réseau virtuel
> * Connecter votre circuit ExpressRoute à la passerelle
> * Localiser les URL pour vCenter et NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Accédez au groupe de ressources que vous avez créé dans le [tutoriel précédent](tutorial-create-private-cloud.md), puis sélectionnez **+ Ajouter** pour définir une nouvelle ressource.

Dans la zone de texte **Rechercher dans la Place de marché**, tapez **Réseau virtuel**. Recherchez la ressource Réseau virtuel et sélectionnez-la.

Dans la page Réseau virtuel, sélectionnez **Créer** afin de configurer votre réseau virtuel pour votre cloud privé.

Dans la page **Créer un réseau virtuel**, entrez les informations appropriées pour votre réseau virtuel. Le tableau suivant contient une description des propriétés :

> [!IMPORTANT]
> Vous devez utiliser un espace d’adressage qui **ne chevauche pas** l’espace d’adressage que vous avez utilisé lors de la création de votre cloud privé dans le tutoriel précédent.

Sous l’onglet **De base**, entrez un nom pour le réseau virtuel et sélectionnez la région appropriée, puis sélectionnez **Suivant : Adresses IP**

Sous l’onglet **Adresses IP**, sous **Espace d’adressage IPv4**, entrez l’espace d’adressage que vous avez créé dans le tutoriel précédent.

Sélectionnez **+ Ajouter un sous-réseau** puis, dans la page **Ajouter un sous-réseau**, spécifiez un nom et une plage d’adresses appropriée pour le sous-réseau. Quand vous avez terminé, sélectionnez **Ajouter**.

Sélectionnez **Vérifier + créer**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Créer un réseau virtuel" border="true":::

Vérifiez les informations et sélectionnez **Créer**. Une fois le déploiement terminé, vous voyez votre réseau virtuel dans le groupe de ressources.

## <a name="create-a-virtual-network-gateway"></a>Créer une passerelle de réseau virtuel

Vous avez créé un réseau virtuel dans la section précédente : vous allez maintenant créer une passerelle de réseau virtuel.

Dans votre groupe de ressources, sélectionnez **+ Ajouter** pour ajouter une nouvelle ressource.

Dans la zone de texte **Rechercher dans la Place de marché**, tapez **Passerelle de réseau virtuel**. Recherchez la ressource Réseau virtuel et sélectionnez-la.

Dans la page **Passerelle de réseau virtuel**, sélectionnez **Créer**.

Dans l’onglet De base de la page **Créer une passerelle de réseau virtuel**, spécifiez des valeurs pour les champs. Le tableau suivant contient des descriptions des champs :

| Champ | Valeur |
| --- | --- |
| **Abonnement** | Cette valeur est déjà renseignée avec l’abonnement auquel appartient le groupe de ressources. |
| **Groupe de ressources** | Cette valeur est déjà renseignée pour le groupe de ressources actuel. Il doit s’agir du groupe de ressources que vous avez créé dans un test précédent. |
| **Nom** | Entrez un nom unique pour la passerelle de réseau virtuel. |
| **Région** | Sélectionnez l’emplacement géographique de la passerelle de réseau virtuel. |
| **Type de passerelle** | Sélectionnez **ExpressRoute**. |
| **Type de VPN** | Sélectionnez **Route-based**. |
| **Référence (SKU)** | Laissez la valeur par défaut : **Standard**. |
| **Réseau virtuel** | Sélectionnez le réseau virtuel que vous avez créé précédemment. Si vous ne voyez pas le réseau virtuel, vérifiez que la région de la passerelle correspond à la région de votre réseau virtuel. |
| **Plage d’adresses du sous-réseau de passerelle** | Cette valeur est renseignée quand vous sélectionnez le réseau virtuel. Ne modifiez pas la valeur par défaut. |
| **Adresse IP publique** | Sélectionnez **Créer nouveau**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Créer une passerelle" border="true":::

Sélectionnez **Vérifier + créer**. Dans la page suivante, vérifiez que les détails sont corrects, puis sélectionnez **Créer** pour démarrer le déploiement de votre passerelle de réseau virtuel. Une fois le déploiement terminé, passez à la section suivante de ce tutoriel pour connecter votre connexion ExpressRoute au réseau virtuel contenant votre cloud privé.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Connecter ExpressRoute à la passerelle de réseau virtuel

Cette section vous guide pour ajouter une connexion entre votre cloud privé AVS et la passerelle de réseau virtuel que vous avez créée.

Accédez au cloud privé que vous avez créé dans le tutoriel précédent et sélectionnez **Connectivité** sous **Gérer**, puis sélectionnez l’onglet **ExpressRoute**.

Copiez la clé d’autorisation. S’il n’existe pas de clé d’autorisation, vous devez en créer une. Pour cela, sélectionnez **+ Demander une clé d’autorisation**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Demander une clé d’autorisation" border="true":::

Accédez à la passerelle de réseau virtuel que vous avez créée à l’étape précédente et, sous **Paramètres**, sélectionnez **Connexions**. Dans la page **Connexions**, sélectionnez **+ Ajouter**.

Dans la page **Ajouter une connexion**, spécifiez des valeurs pour les champs. Le tableau suivant contient des descriptions des champs :

| Champ | Valeur |
| --- | --- |
| **Nom**  | Attribuez un nom à cette connexion.  |
| **Type de connexion**  | Sélectionnez **ExpressRoute**.  |
| **Utiliser l’autorisation**  | Vérifiez que cette case est sélectionnée.  |
| **Passerelle de réseau virtuel** | Passerelle de réseau virtuel que vous avez créée précédemment.  |
| **Clé d’autorisation**  | Copiez et collez la clé d’autorisation à partir de l’onglet ExpressRoute pour votre groupe de ressources. |
| **URI du circuit pair**  | Copiez et collez l’ID ExpressRoute à partir de l’onglet ExpressRoute pour votre groupe de ressources.  |

Sélectionnez **OK**. Ceci crée la connexion entre votre circuit ExpressRoute et votre réseau virtuel.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Ajouter une connexion" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Localiser les URL pour vCenter et NSX Manager

Pour vous connecter à vCenter et à NSX Manager, vous avez besoin des URL du client web vCenter et du site NSX-T Manager. Pour trouver les URL :

Accédez à votre cloud privé AVS puis, sous **Gérer**, sélectionnez **Identité** : vous trouvez ici les informations nécessaires.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Localiser les URL vCenter" border="true":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d'un réseau virtuel
> * Créer une passerelle de réseau virtuel
> * Connecter votre circuit ExpressRoute à la passerelle
> * Localiser les URL pour vCenter et NSX Manager

Passez au tutoriel suivant pour découvrir comment créer une jumpbox qui est utilisée pour se connecter à votre environnement, de façon à pouvoir gérer votre cloud privé localement.

> [!div class="nextstepaction"]
> [Accéder à un cloud privé](tutorial-access-private-cloud.md)
