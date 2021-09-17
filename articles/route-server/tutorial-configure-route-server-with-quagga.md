---
title: 'Tutoriel : Configuration du peering entre le Serveur de routes Azure et une appliance virtuelle réseau Quagga'
description: Ce tutoriel montre comment configurer un Serveur de routes Azure et établir un peering avec une appliance virtuelle réseau Quagga.
author: duongau
ms.author: duau
ms.service: route-server
ms.topic: tutorial
ms.date: 08/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbeee212b8ed7284780083166af34285f2d44a5e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823950"
---
# <a name="tutorial-configure-peering-between-azure-route-server-and-quagga-network-virtual-appliance"></a>Tutoriel : Configuration du peering entre le Serveur de routes Azure et une appliance virtuelle réseau Quagga

Ce tutoriel montre comment déployer un Serveur de routes Azure dans un réseau virtuel et établir une connexion de peering BGP avec une appliance virtuelle réseau Quagga. Vous allez déployer un réseau virtuel comportant cinq sous-réseaux. L’un deux sera dédié au Serveur de routes Azure, un autre à l’appliance virtuelle réseau Quagga. Celle-ci sera configurée pour échanger des itinéraires avec le Serveur de routes. Enfin, vous effectuerez des tests pour vérifier que les itinéraires sont correctement échangés sur le Serveur de routes Azure et l’appliance virtuelle réseau Quagga.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un réseau virtuel comportant cinq sous-réseaux
> * Déployer un Serveur de routes Azure
> * Déployer une machine virtuelle exécutant Quagga
> * Configurer le peering du Serveur de routes
> * Vérifier les itinéraires appris

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Il vous faut un réseau virtuel pour y déployer le Serveur de routes Azure et l’appliance virtuelle réseau Quagga. Chaque déploiement possède un sous-réseau dédié.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** et recherchez **Réseau virtuel**. Sélectionnez ensuite **Créer**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-new-virtual-network.png" alt-text="Capture d’écran de la création d’une ressource de réseau virtuel.":::

1. Dans l’onglet *Informations de base* de la page *Créer un réseau virtuel*, entrez ou sélectionnez les informations suivantes, puis sélectionnez **Suivant : Adresses IP >** :

    | Paramètres | Valeur |
    | -------- | ----- | 
    | Abonnement | Sélectionnez l’abonnement associé à ce déploiement. |
    | Resource group | Sélectionnez un groupe de ressources ou créez-en un pour ce déploiement. | 
    | Nom | Donnez un nom au réseau virtuel (*myVirtualNetwork* dans ce tutoriel).
    | Région | Sélectionnez la région dans laquelle ce réseau virtuel sera déployé (*USA Ouest* dans ce tutoriel).

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-basics-tab.png" alt-text="Capture d’écran des paramètres de l’onglet Informations de base du réseau virtuel.":::

1. Dans l’onglet **Adresses IP**, configurez *Espace d’adressage du réseau virtuel* sur **10.1.0.0/16**. Configurez ensuite les cinq sous-réseaux suivants :

    | Nom du sous-réseau | Plage d’adresses de sous-réseau |
    | ----------- | -------------------- |
    | RouteServerSubnet | 10.1.1.0/25 |
    | subnet1 | 10.1.2.0/24 |
    | subnet2 | 10.1.3.0/24 |
    | subnet3 | 10.1.4.0/24 |
    | GatewaySubnet | 10.1.5.0/24 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-ip-addresses.png" alt-text="Capture d’écran des paramètres d’adresse IP du réseau virtuel.":::

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie.

## <a name="create-the-azure-route-server"></a>Création du Serveur de routes Azure

Le Serveur de routes permet de communiquer avec votre appliance virtuelle réseau et d’échanger des itinéraires de réseau virtuel à l’aide d’une connexion de peering BGP.

1. Atteindre https://aka.ms/routeserver.

1. Sélectionnez **+ Créer un serveur de routage**.

   :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Capture d’écran de la page d’accueil de Route Server.":::

1. Sur la page **Créer un Serveur de routes**, entrez ou sélectionnez les informations suivantes :

    | Paramètres | Valeur |
    | -------- | ----- |
    | Abonnement | Sélectionnez l’abonnement dans lequel le réseau virtuel a été créé dans la section précédente. | 
    | Resource group | Sélectionnez le groupe de ressources *myRouteServerRG*. |
    | Nom | Entrez le nom du Serveur de routes *myRouteServer*. |
    | Région | Sélectionnez la région **USA Ouest**. |
    | Réseau virtuel | Sélectionnez le réseau virtuel *myVirtualNetwork*. |
    | Subnet | Sélectionnez le sous-réseau *RouteServerSubnet (10.1.0.0/25)* créé précédemment. |
    | Adresse IP publique | Créez une adresse IP publique standard ou sélectionnez-en une à utiliser avec le Serveur de routes. Elle permet de garantir la connectivité au service back-end qui gère la configuration du Serveur de routes. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/route-server-basics-tab.png" alt-text="Capture d’écran de l’onglet Informations de base pour la création d’un Serveur de routes.":::

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie. Le déploiement du Serveur de routes prend environ 15 minutes.

## <a name="create-quagga-network-virtual-appliance"></a>Création d’une appliance virtuelle réseau Quagga

Pour configurer l’appliance virtuelle réseau Quagga, vous devez déployer une machine virtuelle Linux.

1. Sur le Portail Azure, sélectionnez **+ Créer une ressource > Calcul > Machine virtuelle**. Sélectionnez ensuite **Créer**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-virtual-machine.png" alt-text="Capture d’écran de la page de création d’une machine virtuelle.":::

1. Sous l’onglet *Informations de base*, entrez ou sélectionnez les informations suivantes :

    | Paramètres | Valeur |
    | -------- | ----- |
    | Abonnement | Sélectionnez le même abonnement que pour le réseau virtuel déjà déployé. |
    | Resource group | Sélectionnez le groupe de ressources *myRouteServerRG*. |
    | Nom de la machine virtuelle | Entrez le nom **Quagga**. |
    | Région | Sélectionnez la région **USA Ouest**. |
    | Image | Sélectionnez **Ubuntu 18.04 LTS – Gen 1**. |
    | Taille | Sélectionnez **Standard_B2s – 2 processeurs virtuels, 4 Gio de mémoire**. |
    | Type d'authentification | Sélectionnez **Mot de passe** |
    | Nom d’utilisateur | entrez *azureuser*. N’utilisez pas *quagga* comme nom d’utilisateur. Sinon, le script d’installation échouera à une étape ultérieure. |
    | Mot de passe | Entrez et confirmez le mot de passe de votre choix. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **SSH (22)** . |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab.png" alt-text="Capture d’écran de l’onglet Informations de base pour la création d’une machine virtuelle." lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab-expanded.png":::
    
1. Dans l’onglet **Mise en réseau**, sélectionnez les paramètres réseau suivants :

    | Paramètres | Valeur |
    | -------- | ----- |
    | Réseau virtuel | Sélectionnez **myVirtualNetwork**. |
    | Subnet | Sélectionnez **subnet3 (10.1.4.0/24)** . |
    | Adresse IP publique | Laissez la valeur par défaut. |
    | Groupe de sécurité réseau de la carte réseau | Laissez la valeur par défaut. **De base**. |
    | Aucun port d’entrée public | Laissez la valeur par défaut. **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Conservez la valeur par défaut : **SSH (22)** . |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab.png" alt-text="Capture d’écran de l’onglet Mise en réseau pour la création d’une machine virtuelle." lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab-expanded.png":::

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie. Le déploiement de la machine virtuelle prend environ 10 minutes.

1. Une fois la machine virtuelle Quagga déployée, accédez à ses paramètres de mise en réseau et sélectionnez l’interface réseau.

     :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-network-settings.png" alt-text="Capture d’écran de la page Mise en réseau de la machine virtuelle Quagga.":::

1. Sélectionnez **Configuration IP** sous *Paramètres*, puis **ipconfig1**.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-ip-configuration.png" alt-text="Capture d’écran de la page de configuration IP de la machine virtuelle Quagga.":::

1. Remplacez l’affectation *Dynamique* par **Statique**, puis l’adresse IP *10.1.4.4* par **10.1.4.10**. Cette adresse IP est utilisée dans ce [script](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh), qui sera exécuté plus tard. Si vous souhaitez en utiliser une autre, veillez à la mettre à jour dans le script. Sélectionnez **Enregistrer** pour mettre à jour la configuration IP de la machine virtuelle.

1. À l’aide de [Putty](https://www.putty.org/), connectez-vous à la machine virtuelle Quagga avec l’adresse IP publique et les informations d’identification utilisées pour créer la machine virtuelle.

1. Une fois la connexion établie, entrez `sudo su` pour basculer en mode super utilisateur et ainsi éviter les erreurs d’exécution du script. Copiez ce [script](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh) et collez-le dans la session Putty. Il configure la machine virtuelle avec Quagga, ainsi que d’autres paramètres réseau. Mettez à jour le script en fonction de votre environnement réseau avant de l’exécuter sur la machine virtuelle. Il lui faut quelques minutes pour effectuer l’installation.

## <a name="configure-route-server-peering"></a>Configurer le peering du Serveur de routes

1. Accédez au Serveur de routes que vous avez créé à l’étape précédente.

1. Sélectionnez **Pairs** sous *Paramètres*. Sélectionnez ensuite **+ Ajouter** pour ajouter un nouveau pair.

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peers.png" alt-text="Capture d’écran de la page Pairs du Serveur de routes.":::

1. Sur la page *Ajouter un pair*, entrez les informations suivantes, puis sélectionnez **Enregistrer** pour enregistrer la configuration :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez un nom pour identifier ce pair : **Quagga**. |
    | ASN | Entrez le numéro ASN de l’appliance virtuelle réseau Quagga : **65001** tel qu’il est défini dans le script. |
    | Adresse IPv4 | Entrez l’adresse IP privée de la machine virtuelle de l’appliance virtuelle réseau Quagga. |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/add-peer.png" alt-text="Capture d’écran de la page Ajouter un pair.":::

1. La page Pairs se présente comme suit une fois que vous avez ajouté un pair :

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peer-configured.png" alt-text="Capture d’écran d’un pair configuré.":::
    
## <a name="check-learned-routes"></a>Vérifier les itinéraires appris
1. Pour vérifier les itinéraires appris par le Serveur de routes, utilisez cette commande :

    ```azurepowershell-interactive
    $routes = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        PeerName = 'Quagga'
    }
    Get-AzRouteServerPeerLearnedRoute @routes | ft
    ```

    La sortie doit se présenter comme suit : 

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/routes-learned.png" alt-text="Capture d’écran des itinéraires appris par le Serveur de routes.":::

1. Pour vérifier les itinéraires appris par l’appliance virtuelle réseau Quagga, entrez `vtysh`, puis `show ip bgp`. La sortie se présente comme suit :

    ```
    root@Quagga:/home/azureuser# vtysh

    Hello, this is Quagga (version 1.2.4).
    Copyright 1996-2005 Kunihiro Ishiguro, et al.

    Quagga# show ip bgp
    BGP table version is 0, local router ID is 10.1.4.10
    Status codes: s suppressed, d damped, h history, * valid, > best, = multipath,
                  i internal, r RIB-failure, S Stale, R Removed
    Origin codes: i - IGP, e - EGP, ? - incomplete

        Network          Next Hop            Metric LocPrf Weight Path
        10.1.0.0/16      10.1.1.4                               0 65515 i
                         10.1.1.5                               0 65515 i
    *> 10.100.1.0/24    0.0.0.0                  0         32768 i
    *> 10.100.2.0/24    0.0.0.0                  0         32768 i
    *> 10.100.3.0/24    0.0.0.0                  0         32768 i
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du Serveur de routes ni des ressources associées, vous pouvez supprimer le groupe de ressources **myRouteServerRG**.

:::image type="content" source="./media/tutorial-configure-route-server-with-quagga/delete-resource-group.png" alt-text="Capture d’écran du bouton Supprimer le groupe de ressources.":::

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment résoudre les problèmes liés au Serveur de routes.
> [!div class="nextstepaction"]
> [Résoudre les problèmes liés au Serveur de routes](troubleshoot-route-server.md)
