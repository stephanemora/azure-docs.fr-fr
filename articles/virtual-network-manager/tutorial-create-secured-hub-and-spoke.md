---
title: 'Didacticiel : Créer un réseau en étoile sécurisé'
description: Ce didacticiel explique comment créer un réseau en étoile avec Azure Virtual Network Manager. Ensuite, vous allez sécuriser tous vos réseaux virtuels avec une stratégie de sécurité.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 672b1218a52ee94e226c5b7c763c8e7622b60267
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097795"
---
# <a name="tutorial-create-a-secured-hub-and-spoke-network"></a>Didacticiel : Créer un réseau en étoile sécurisé

Dans le cadre de ce didacticiel, vous allez créer une topologie de réseau en étoile à l’aide d’Azure Virtual Network Manager. Vous allez ensuite déployer une passerelle de réseau virtuel dans le réseau virtuel hub pour permettre aux ressources des réseaux virtuels formant les rayons de l’étoile de communiquer avec des réseaux distants à l’aide d’un VPN. Vous allez également définir une configuration de sécurité pour bloquer le trafic réseau sortant vers Internet sur les ports 80 et 443. Enfin, vous allez vérifier que les configurations ont été appliquées correctement en examinant les paramètres de réseau virtuel et de machine virtuelle.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer plusieurs réseaux virtuels.
> * Déployer une passerelle de réseau virtuel.
> * Créer une topologie de réseau en étoile.
> * Créer une configuration de sécurité bloquant le trafic sur les ports 80 et 443.
> * Vérifier que les configurations ont été appliquées.

## <a name="prerequisite"></a>Configuration requise

* Avant de pouvoir suivre la procédure décrite dans ce didacticiel, vous devez [créer une instance Azure Virtual Network Manager](create-virtual-network-manager-portal.md).

## <a name="create-virtual-networks"></a>Créer des réseaux virtuels

Cette procédure vous guide dans la création de trois réseaux virtuels. L’un sera dans la région *USA Ouest* et les deux autres seront dans la région *USA Est*.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **+ Créer une ressource**, puis recherchez **Réseau virtuel**. Sélectionnez ensuite **Créer** pour commencer à configurer le réseau virtuel.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet.png" alt-text="Capture d’écran de la page Créer un réseau virtuel.":::

1. Sous l’onglet *Informations de base*, entrez ou sélectionnez les informations suivantes :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-basic.png" alt-text="Capture d’écran de l’onglet de base pour un réseau virtuel en étoile.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez l’abonnement dans lequel vous souhaitez déployer ce réseau virtuel. |
    | Resource group | Sélectionnez ou créez un groupe de ressources pour stocker le réseau virtuel. Ce guide de démarrage rapide utilise un groupe de ressources nommé **myAVNMResourceGroup**.
    | Nom | Entrez **VNet-A-westUS** en guise de nom pour le réseau virtuel. |
    | Région | Sélectionnez la région **USA Ouest**. |

 1. Sous l’onglet *Adresses IP* , configurez l’espace d’adressage réseau suivant :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-hub-vnet-addresses.png" alt-text="Capture d’écran de l’onglet Adresses IP pour le réseau en étoile.":::

    | Paramètre | Valeur |
    | -------- | ----- |
    | Espace d’adressage IPv4 | Entrez **10.3.0.0/16** en guise d’espace d’adressage. |
    | Nom du sous-réseau | Entrez le nom **par défaut** pour le sous-réseau. |
    | Espace d’adressage du sous-réseau | Entrez l’espace d’adressage de sous-réseau **10.3.0.0/24**. |

1. Sélectionnez **Vérifier + créer**, puis **Créer** pour déployer le réseau virtuel.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-vnet-validation.png" alt-text="Capture d’écran de la page de validation pour le réseau virtuel en étoile.":::

1. Répétez les étapes 2 à 5 pour créer deux réseaux virtuels supplémentaires dans le même groupe de ressources avec les informations suivantes :

    **Deuxième réseau virtuel** :
    * Nom : **VNet-A-EastUS**
    * Région : **USA Est**
    * Espace d’adressage IPv4 : **10.4.0.0/16**
    * Nom du sous-réseau : **par défaut**
    * Espace d’adressage du sous-réseau : **10.4.0.0/24**

    **Troisième réseau virtuel** :
    * Nom : **VNet-B-EastUS**
    * Région : **USA Est**
    * Espace d’adressage IPv4 : **10.5.0.0/16**
    * Nom du sous-réseau : **par défaut**
    * Espace d’adressage du sous-réseau : **10.5.0.0/24**

## <a name="deploy-a-virtual-network-gateway"></a>Déployer une passerelle de réseau virtuel

Déployez une passerelle de réseau virtuel dans le réseau virtuel hub. Cette passerelle de réseau virtuel est nécessaire pour que réseaux virtuels formant les rayons de l’étoile *utilisent le hub en tant que paramètre de passerelle*.

1. Sélectionnez **+ Créer une ressource** et recherchez **Passerelle de réseau virtuel**. Sélectionnez ensuite **Créer** pour commencer à configurer la passerelle de réseau virtuel.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-gateway.png" alt-text="Capture d’écran de la page Créer une passerelle de réseau virtuel.":::

1. Dans l’onglet *De base*, entrez ou sélectionnez les paramètres suivants :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-basics.png" alt-text="Capture d’écran de l’onglet Informations de base dans la page Créer une passerelle de réseau virtuel." lightbox="./media/tutorial-create-secured-hub-and-spoke/gateway-basics-expanded.png":::

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation effectuée. Le déploiement d’une passerelle de réseau virtuel peut prendre environ 30 minutes. Vous pouvez passer à la section suivante tout en attendant que ce déploiement se termine.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/gateway-validation.png" alt-text="Capture d’écran de la page de validation de la création d’une passerelle de réseau virtuel.":::

## <a name="create-a-network-group"></a>Créer un groupe de réseaux

1. Accédez à votre instance Azure Virtual Network Manager. Ce didacticiel suppose que vous avez déjà créé une instance à l’aide du Guide de [démarrage rapide](create-virtual-network-manager-portal.md).

1. Sélectionnez **Groupes de réseaux** sous *Paramètres*, puis choisissez **+ Ajouter** pour créer un groupe de réseaux.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-network-group.png" alt-text="Capture d’écran du bouton Ajouter un groupe de réseaux.":::

1. Sous l’onglet *Informations de base*, entrez les informations suivantes :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-basics.png" alt-text="Capture d’écran de l’onglet Informations de base dans la page Créer un groupe de réseaux.":::

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myNetworkGroupB** en guise de nom pour le groupe de réseaux. |
    | Description | Fournissez une description de ce groupe de réseaux. |

1. Sélectionnez l’onglet **Instructions conditionnelles**. Pour le *Paramètre*, sélectionnez **Nom** dans la liste déroulante. Pour l’*Opérateur*, sélectionnez **Contient**. Pour la *Condition*, entrez **VNet-** . Cette instruction conditionnelle ajoute les trois réseaux virtuels créés précédemment à ce groupe de réseaux.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-conditional.png" alt-text="Capture d’écran de l’onglet Instructions conditionnelles dans la page Créer un groupe de réseaux.":::

1. Sélectionnez **Évaluer** si vous devez vérifier les réseaux virtuels sélectionnés.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/evaluate-vnet.png" alt-text="Capture d’écran de la page Réseaux virtuels effectifs.":::

1. Sélectionnez **Vérifier + créer**, puis choisissez **Créer** une fois la validation réussie.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-validation.png" alt-text="Capture d’écran de la page de validation de création de groupe de réseaux.":::

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Créer une configuration de connectivité de réseau en étoile

1. Sous *Paramètres*, sélectionnez **Configuration**, puis choisissez **+ Ajouter une configuration**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-configuration.png" alt-text="Capture d’écran du bouton Ajouter une configuration pour le gestionnaire de réseau.":::

1. Dans le menu déroulant, sélectionnez **Connectivité**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Capture d’écran du menu déroulant de configuration.":::

1. Entrez et sélectionnez les informations suivantes pour la configuration de la connectivité :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/connectivity-configuration.png" alt-text="Capture d’écran de la page Ajouter une configuration de connectivité.":::

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **HubA** en guise de nom pour la configuration. |
    | Description | Fournissez une description de ce que fait cette configuration de connectivité. |
    | Topologie | Sélectionnez **Réseau en étoile**. |

1. Lorsque vous sélectionnez la topologie *Réseau en étoile*, d’autres champs s’affichent. Sélectionnez les paramètres suivants :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/hub-configuration.png" alt-text="Capture d’écran montrant la sélection d’un hub pour la configuration de la connectivité.":::

    | Paramètres | Valeur |
    | -------- | ----- |
    | Hub | Select **VNet-A-West** en guide de réseau virtuel hub. |
    | Peerings existants | Laissez cette option **désactivée**. |
    | Groupes de réseaux formant les rayons de l’étoile | Sélectionnez **Ajouter des groupes de réseaux**, puis ajoutez **myNetworkGroupB** à la configuration. |

1. Une fois que vous avez ajouté le groupe de réseaux, sélectionnez les options suivantes. Sélectionnez ensuite Ajouter pour créer la configuration de la connectivité.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/network-group-options.png" alt-text="Capture d’écran des paramètres de configuration du groupe de réseaux.":::

    | Paramètre | Value |
    | ------- | ----- |
    | Transitivité | Activez la case à cocher **Activer le peering au sein du groupe de réseaux**. Ce paramètre permettra aux réseaux virtuels formant les rayons de l’étoile dans le groupe de réseaux dans la même région de communiquer directement entre eux. |
    | Maille globale | Laissez cette option **désactivée**. Étant donné que les deux réseaux virtuels formant les rayons de l’étoile se trouvent dans la même région, ce paramètre n’est pas obligatoire. |
    | Passerelle | Sélectionnez **Utiliser un hub en tant que passerelle**. |

## <a name="deploy-the-connectivity-configuration"></a>Déployer la configuration de la connectivité

Assurez-vous que la passerelle de réseau virtuel a été déployée correctement avant de déployer la configuration de la connectivité. Si vous déployez une configuration en étoile avec l’option **Utiliser le hub en tant que passerelle** activée et qu’il n’y a aucune passerelle, le déploiement échouera. Pour plus d’informations, consultez [Utiliser un hub en tant que passerelle](concept-connectivity-configuration.md#use-hub-as-a-gateway).

1. Sous *Paramètres*, sélectionnez **Déploiements**, puis **Déployer une configuration**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Capture d’écran de la page Déploiements dans le gestionnaire de réseau.":::

1. Sélectionnez le type de configuration de la **Connectivité** et la configuration **Huba** que vous avez créés dans la dernière section. Sélectionnez ensuite **USA Ouest** et **USA Est** comme régions cibles, puis choisissez **Déployer**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-configuration.png" alt-text="Capture d’écran de la page Déployer une configuration.":::

1. Sélectionnez **OK** pour confirmer que vous souhaitez remplacer la configuration existante et déployer la configuration de l’administration de la sécurité. 

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="Capture d’écran du message de confirmation du déploiement.":::

1. Vous devez maintenant voir le déploiement s’afficher dans la liste pour ces régions. Le déploiement de la configuration peut prendre environ 15 à 20 minutes.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Capture d’écran du déploiement en cours dans la liste des déploiements.":::

## <a name="create-security-configuration"></a>Créer une configuration de la sécurité

1. Sous *Paramètres*, sélectionnez de nouveau **Configuration**, puis choisissez **+ Ajouter une configuration**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-security-configuration.png" alt-text="Capture d’écran montrant l’ajout d’une autre configuration pour le gestionnaire de réseau.":::

1. Sélectionnez **SecurityAdmin** dans le menu pour commencer à créer une configuration SecurityAdmin.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-drop-down.png" alt-text="Capture d’écran de SecurityAdmin dans le menu déroulant.":::

1. Entrez le nom **mySecurityConfig** pour la configuration, puis sélectionnez **+ Ajouter une collection de règles**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-admin-configuration.png" alt-text="Capture d’écran de la page Configuration de l’administration de la sécurité.":::

1. Entrez le nom **myRuleCollection** pour la collection de règles, puis sélectionnez **myNetworkGroupB** pour le groupe de réseaux cible. Sélectionnez ensuite **+ Ajouter une règle**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule-collection.png" alt-text="Capture d’écran de la page Ajouter une collection de règles.":::

1. Entrez et sélectionnez les paramètres suivants, puis choisissez **Ajouter** :

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/add-rule.png" alt-text="Capture d’écran de la page Ajouter une règle.":::

1. Sélectionnez **Enregistrer** pour ajouter la collection de règles à la configuration.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/save-rule-collection.png" alt-text="Capture d’écran du bouton Enregistrer pour une collection de règles.":::

1. Sélectionnez **Ajouter** pour créer la configuration de l’administration de la sécurité.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/create-configuration.png" alt-text="Capture d’écran du bouton Ajouter pour créer une configuration.":::

## <a name="deploy-the-security-admin-configuration"></a>Déployer la configuration de l’administration de la sécurité

1. Sous *Paramètres*, sélectionnez **Déploiements**, puis **Déployer une configuration**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployments.png" alt-text="Capture d’écran de la page Déploiements de sécurité dans le gestionnaire de réseau virtuel.":::

1. Sélectionnez le type de configuration **SecurityAdmin** et la configuration **mySecurityConfig** que vous avez créée dans la dernière section. Sélectionnez ensuite **USA Ouest** et **USA Est** comme régions cibles, puis choisissez **Déployer**.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deploy-security.png" alt-text="Capture d’écran montrant le déploiement d’une configuration de la sécurité.":::

1. Sélectionnez **OK** pour confirmer que vous souhaitez remplacer la configuration existante et déployer la configuration de l’administration de la sécurité.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="Capture d’écran du message de confirmation pour le déploiement d’une configuration de la sécurité.":::

1. Vous devez maintenant voir le déploiement s’afficher dans la liste pour la région sélectionnée. Le déploiement de la configuration peut prendre environ 15 à 20 minutes.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/security-in-progress.png" alt-text="Capture d’écran montrant un déploiement de la sécurité en cours dans la liste des déploiements.":::

## <a name="verify-deployment-of-configurations"></a>Vérifier le déploiement des configurations

### <a name="verify-from-a-virtual-network"></a>Vérifier à partir d’un réseau virtuel

1. Accédez au réseau virtuel **VNet-A-WestUS**, puis, sous *Paramètres*, sélectionnez **Gestionnaire de réseau**. Vous allez voir la configuration de la connectivité **Huba** appliquée.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-connectivity-configuration.png" alt-text="Capture d’écran de la configuration de la connectivité appliquée au réseau virtuel.":::

1. Sélectionnez **Peerings** sous *Paramètres*. Vous allez voir les appairages de réseaux virtuels créés par le gestionnaire de réseau virtuel avec *AVNM* dans le nom.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-peerings.png" alt-text="Capture d’écran des appairages de réseaux virtuels créés par le gestionnaire de réseau virtuel.":::

1. Sélectionnez l’onglet **SecurityAdmin** pour afficher les règles d’administration de la sécurité appliquées à ce réseau virtuel.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vnet-admin-configuration.png" alt-text="Capture d’écran des règles d’administration de la sécurité appliquées au réseau virtuel.":::

### <a name="verify-from-a-vm"></a>Vérifier à partir d’une machine virtuelle

1. Déployez une machine virtuelle de test Windows dans **VNet-A-EastUS**. 

1. Accédez à la machine virtuelle de test créée dans *VNet-A-EastUS*, puis, sous *Paramètres*, sélectionnez **Mise en réseau**. Sélectionnez **Règles de port de sortie** pour voir la règle d’administration de la sécurité appliquée.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-security-rules.png" alt-text="Capture d’écran des règles de sécurité réseau de la machine virtuelle de test.":::

1. Sélectionnez le nom de l’interface réseau.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/vm-network-settings.png" alt-text="Capture d’écran des paramètres réseau de la machine virtuelle de test.":::

1. Sélectionnez ensuite **Itinéraires effectifs** sous *support + dépannage* afin d’afficher les itinéraires pour les appairages de réseaux virtuels. L’itinéraire `10.3.0.0/16` avec le tronçon suivant de `VNetGlobalPeering` est l’itinéraire vers le réseau virtuel hub. L’itinéraire `10.5.0.0/16` avec le tronçon suivant de `ConnectedGroup` est l’itinéraire vers l’autre réseau virtuel formant un rayon de l’étoile. Tous les réseaux virtuels formant les rayons de l’étoile se trouvent dans un *ConnectedGroup* quand l’option **Transitivité** est activée.

    :::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/effective-routes.png" alt-text="Capture d’écran des itinéraires effectifs à partir de l’interface réseau de l’ordinateur virtuel de test." lightbox="./media/tutorial-create-secured-hub-and-spoke/effective-routes-expanded.png" :::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du gestionnaire de réseau virtuel Azure, vous devez vous assurer que toutes les conditions suivantes sont remplies avant de pouvoir supprimer la ressource :

* Il n’existe aucun déploiement de configurations dans une région.
* Toutes les configurations ont été supprimées.
* Tous les groupes de réseaux ont été supprimés.

Utilisez la [liste de vérification de suppression des composants](concept-remove-components-checklist.md) pour vous assurer qu’aucune ressource enfant n’est encore disponible avant de supprimer le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-portal.md).
