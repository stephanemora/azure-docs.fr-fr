---
title: 'Tutoriel : Configurer le réacheminement de port - Portail Azure'
titleSuffix: Azure Load Balancer
description: Ce didacticiel montre comment configurer la redirection de port à l’aide d’Azure Load Balancer pour créer des connexions à des machines virtuelles dans un réseau virtuel Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 8/26/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0d36be9f0ba0fc9e1b29e3fdaf0b3f9857db2204
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112097"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-azure-portal"></a>Tutoriel : Configurer la redirection de port dans Azure Load Balancer à l’aide du portail Azure

La redirection de port vous permet de vous connecter à des machines virtuelles dans un réseau virtuel Azure à l’aide de l’adresse IP publique et du numéro de port d’un Azure Load Balancer. 

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel et des machines virtuelles
> * Créer une passerelle NAT pour l’accès Internet sortant pour le pool de back-ends
> * Créer un équilibreur de charge public de référence SKU standard avec une adresse IP de front-end, une sonde d’intégrité, une configuration de back-end, une règle d’équilibrage de charge et des règles NAT de trafic entrant
> * Installer et configurer un serveur web sur les machines virtuelles afin d’illustrer les règles de réacheminement de port et d’équilibrage de charge

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network-and-virtual-machines"></a>Créer un réseau virtuel et des machines virtuelles

Un réseau virtuel et un sous-réseau sont nécessaires pour les ressources du tutoriel. Dans cette section, vous allez créer un réseau virtuel et des machines virtuelles pour les étapes ultérieures.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

3. Dans **Machines virtuelles**, sélectionnez **+ Créer** > **Machine virtuelle**.
   
4. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **TutorialLBPF-rg**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |    |
    | Nom de la machine virtuelle | Entrez **myVM1**. |
    | Région | Entrez **(États-Unis) USA Ouest 2**. |
    | Options de disponibilité | Sélectionnez **Zone de disponibilité**. |
    | Zone de disponibilité | tapez **1**. |
    | Image | Sélectionnez **Ubuntu Server 20.04 LTS - Gen2**. |
    | Instance Azure Spot | Conservez la valeur par défaut (case non cochée). |
    | Taille | Sélectionnez une taille de machine virtuelle. |
    | **Compte administrateur** |    |
    | Type d'authentification | sélectionnez **Clé publique SSH**. |
    | Nom d’utilisateur | entrez **azureuser**. |
    | Source de la clé publique SSH | Sélectionnez **Générer une nouvelle paire de clés**. |
    | Nom de la paire de clés | Entrez **myKey**. |
    | **Règles des ports d’entrée** |    |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/create-vm-portal.png" alt-text="Capture d’écran de la création d’une machine virtuelle.":::

5. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.

6. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Value |
    | ------- | ----- |
    | **Interface réseau** |   |
    | Réseau virtuel | Sélectionnez **Créer nouveau**. </br> Entrez **myVNet** dans **Nom**. </br> Dans **Espace d’adressage**, sous **Plage d’adresses**, entrez **10.1.0.0/16**. </br> Dans **Sous-réseaux**, sous **Nom de sous-réseau**, entrez **myBackendSubnet**. </br> Dans **plage d’adresses**, entrez **10.1.0.0/24**. </br> Sélectionnez **OK**. |
    | Subnet | Sélectionnez **myBackendSubnet**. |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**. |
    | Configurer un groupe de sécurité réseau | Sélectionnez **Créer nouveau**. </br> Entrez **myNSG** dans **Nom**. </br> Sélectionnez **+ Ajouter une règle de trafic entrant** sous **Règles de trafic entrant**. </br> Dans **Service**, sélectionnez **HTTP**. </br> Entrez **100** dans **Priorité**. </br> Entrez **myNSGRule** pour **Nom**. </br> Sélectionnez **Ajouter**. </br> Sélectionnez **OK**. |

7. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer** situé au bas de la page.

8. Sélectionnez **Create** (Créer).

9. À l’invite **Générer une nouvelle paire de clés**, sélectionnez **Télécharger la clé privée et créer une ressource**. Votre fichier de clé sera téléchargé sous myKey.pem. Vérifiez que vous savez où le fichier .pem a été téléchargé ; vous aurez besoin du chemin du fichier de clé dans les étapes ultérieures.

8. Effectuez les étapes 1 à 8 pour créer une autre machine virtuelle avec les valeurs suivantes et tous les autres paramètres identiques à ceux de **myVM1** :

    | Paramètre | Machine virtuelle 2 |
    | ------- | ----- |
    | **Concepts de base** |    |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | **myVM2** |
    | Zone de disponibilité | **2** |
    | **Compte administrateur** |   |
    | Type d'authentification | **Clé publique SSH** |
    | Source de la clé publique SSH | Sélectionnez **Utiliser une clé existante stockée dans Azure**. |
    | Clés stockées | Sélectionnez **myKey**. |
    | **Mise en réseau** |   |
    | **Interface réseau** |  |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**. |
    | Configurer un groupe de sécurité réseau | Sélectionnez le groupe **myNSG** existant |

## <a name="create-nat-gateway"></a>Créer une passerelle NAT

Dans cette section, vous allez créer une passerelle NAT pour l’accès Internet sortant des ressources dans le réseau virtuel. 

1. Dans la zone de recherche située en haut du portail, entrez **Passerelle NAT**. Sélectionnez **Passerelle NAT** dans les résultats de la recherche.

2. Dans **Passerelles NAT**, sélectionnez **+ Créer**.

3. Dans **Créer une passerelle NAT (traduction d’adresses réseau)** , entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **TutorialLBPF-rg**. |
    | **Détails de l’instance** |    |
    | Nom de la passerelle NAT | Entrez **myNATgateway**. |
    | Zone de disponibilité | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez **15**. |

4. Sélectionnez l’onglet **IP sortante** ou le bouton **Suivant : IP sortante** situé au bas de la page.

5. Dans **IP sortante**, sélectionnez **Créer une adresse IP publique** à côté d’**Adresses IP publiques**.

6. Entrez **myNATGatewayIP** pour le **Nom** dans **Ajouter une adresse IP publique**.

7. Sélectionnez **OK**.

8. Sélectionnez l’onglet **Sous-réseau** ou le bouton **Suivant : Sous-réseau** situé au bas de la page.

9. Dans **Réseau virtuel**, dans l’onglet **Sous-réseau**, sélectionnez **myVNet**.

10. Sélectionnez **myBackendSubnet** sous **Nom du sous-réseau**.

11. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page, ou l’onglet **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-load-balancer"></a>Créer un équilibreur de charge

Vous allez créer un équilibreur de charge dans cette section. L’adresse IP du front-end, le pool de back-ends, l’équilibrage de charge et les règles NAT de trafic entrant sont configurés dans le cadre de la création.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Dans la page **Équilibreur de charge**, sélectionnez **Créer**.

3. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | **Détails du projet** |   |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **TutorialLBPF-rg**. |
    | **Détails de l’instance** |   |
    | Nom                   | Entrez **myLoadBalancer**                                   |
    | Région         | Sélectionnez **(USA) USA Ouest 2**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Conservez la valeur par défaut **Standard**. |
    | Niveau          | Conservez la valeur par défaut **Régional**. |


4. Sélectionnez **Suivant : configuration de l’adresse IP front-end** au bas de la page.

5. Dans **Configuration de l’adresse IP front-end**, sélectionnez **+ Ajouter une adresse IP front-end**.

6. Entrez **LoadBalancerFrontend** dans **Nom**.

7. Sélectionnez **IPv4** ou **IPv6** pour la **Version IP**.

    > [!NOTE]
    > IPv6 n’est actuellement pas pris en charge avec la Préférence de routage ou l’équilibrage de charge Inter-régions (niveau Global).

8. Sélectionnez **Adresse IP** pour **Type IP**.

    > [!NOTE]
    > Pour plus d’informations sur les préfixes IP, consultez [Préfixe d’adresse IP publique Azure](../virtual-network/public-ip-address-prefix.md).

9. Sélectionnez **Créer nouvelle** dans **Adresse IP publique**.

10. Dans **Ajouter une adresse IP publique**, entrez **myPublicIP** pour **Nom**.

11. Sélectionnez **Zone-redondante** dans la **Zone de disponibilité**.

    > [!NOTE]
    > Dans les régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), vous avez la possibilité de sélectionner aucune zone (option par défaut), une zone spécifique ou redondant dans une zone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Dans les régions sans Zones de disponibilité, ce champ n’apparaît pas. </br> Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

12. Laissez la valeur par défaut **Réseau Microsoft** pour **Préférence de routage**.

13. Sélectionnez **OK**.

14. Sélectionnez **Ajouter**.

15. Sélectionnez **Suivant : Pools de back-ends** au bas de la page.

16. Sous l’onglet **Pools de back-ends**, sélectionnez **+ Ajouter un pool de back-end**.

17. Entrez ou sélectionnez les informations suivantes dans **Ajouter un pool de back-ends**.

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myBackendPool**. |
    | Réseau virtuel | Sélectionnez **myVNet (TutorialLBPF-rg)** . |
    | Configuration d’un pool de back-ends | Sélectionner **Carte réseau**. |
    | Version de l’adresse IP | Sélectionnez **IPv4**. |

18. Sélectionnez **+ Ajouter** dans **Machines virtuelles**.

19. Cochez les cases en regard de **myVM1** et **myVM2** dans **Ajouter des machines virtuelles au pool de back-ends**.

20. Sélectionnez **Ajouter**.

21. Sélectionnez **Ajouter**.

22. Sélectionnez le bouton **Suivant : Règles de trafic entrant** au bas de la page.

23. Dans **Règle d’équilibrage de la charge** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle d’équilibrage de charge**.

24. Dans **Ajouter une règle d’équilibrage de charge**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **MyHTTPRule** |
    | Version de l’adresse IP | Sélectionnez **IPv4** ou **IPv6** en fonction de vos besoins. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontend**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**. |
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **MyBackendPool**. |
    | Sonde d’intégrité | Sélectionnez **Créer nouveau**. </br> Dans **Nom**, entrez **myHealthProbe**. </br> Sélectionnez **HTTP** dans **Protocole**. </br> Laissez les autres valeurs par défaut et sélectionnez **OK**. |
    | Persistance de session | Sélectionnez **Aucun**. |
    | Délai d’inactivité (minutes) | Entrez ou sélectionnez **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | IP flottante | Sélectionnez **Désactivé**. |
    | Traduction d’adresses réseau (SNAT) sources sortante | Conservez la valeur par défaut **(Recommandé) Utiliser des règles de trafic sortant pour fournir aux membres du pool de back-ends l’accès à Internet**. |

25. Sélectionnez **Ajouter**.

26. Dans **Règle NAT de trafic entrant** sous l’onglet **Règles de trafic entrant**, sélectionnez **+ Ajouter une règle NAT de trafic entrant**.

27. Dans **Ajouter une règle NAT de trafic entrant**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myNATRuleVM1-221**. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontend**. |
    | Service | Sélectionnez **Personnalisé**. |
    | Protocol | Conservez la valeur par défaut **TCP**. |
    | Délai d’inactivité (minutes) | Entrez ou sélectionnez **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | Port | Entrez **221**. |
    | Machine virtuelle cible | Sélectionnez **myVM1**. |
    | Configuration IP réseau | Sélectionnez **ipconfig1 (10.1.0.4)** . |
    | Mappage de ports | Sélectionnez **Personnalisé**. |
    | IP flottante | Conservez la valeur par défaut **Désactivé**. |
    | Port cible | Entrez **22**. |

28. Sélectionnez **Ajouter**.

29. Sélectionnez **Ajouter une règle NAT de trafic entrant**.

30. Dans **Ajouter une règle NAT de trafic entrant**, entrez ou sélectionnez les informations suivantes.

    | Paramètre | Value |
    | ------- | ----- |
    | Nom | Entrez **myNATRuleVM2-222**. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontend**. |
    | Service | Sélectionnez **Personnalisé**. |
    | Protocol | Conservez la valeur par défaut **TCP**. |
    | Délai d’inactivité (minutes) | Entrez ou sélectionnez **15**. |
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**. |
    | Port | Entrez **222**. |
    | Machine virtuelle cible | Sélectionnez **myVM2**. |
    | Configuration IP réseau | Sélectionnez **ipconfig1 (10.1.0.5)** . |
    | Mappage de ports | Sélectionnez **Personnalisé**. |
    | IP flottante | Conservez la valeur par défaut **Désactivé**. |
    | Port cible | Entrez **22**. |

31. Sélectionnez **Ajouter**.

32. Sélectionnez le bouton bleu **Vérifier + créer** au bas de la page.

33. Sélectionnez **Create** (Créer).

## <a name="install-web-server"></a>Installer le serveur web

Dans cette section, vous allez vous connecter en mode SSH aux machines virtuelles via les règles NAT de trafic entrant et installer un serveur web.

1. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

2. Sélectionnez **myLoadBalancer**.

3. Dans la page **Vue d’ensemble** de **myLoadBalancer**, notez l’**adresse IP Publique**.  Dans cet exemple, il s’agit de **20.190.2.163**.

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/get-public-ip.png" alt-text="Capture d’écran de l’adresse IP publique dans le portail Azure.":::

4. Si vous utilisez un ordinateur Mac ou Linux, ouvrez une invite Bash. Si vous utilisez un ordinateur Windows, ouvrez une invite de commandes PowerShell.

5. À l’invite, ouvrez une connexion SSH à **myVM1**. Remplacez l’adresse IP par l’adresse que vous avez récupérée à l’étape précédente et le port **221** que vous avez utilisé pour la règle NAT de trafic entrant myVM1. Remplacez le chemin du fichier .pem par l’emplacement où le fichier de clé a été téléchargé.

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 221
    ```

    > [!TIP]
    > Vous pourrez utiliser la clé SSH que vous avez créée la prochaine fois que vous créerez une machine virtuelle dans Azure. Il vous suffira de sélectionner **Utiliser une clé stockée dans Azure** pour **Source de la clé publique SSH** la prochaine fois que vous créerez une machine virtuelle. Vous disposerez déjà de la clé privée sur votre ordinateur, et n’aurez donc pas besoin de télécharger quoi que ce soit.

6. Dans votre session SSH, mettez à jour vos sources de package, puis installez le dernier package NGINX.

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

7. Entrez `Exit` pour quitter la session SSH.

8. À l’invite, ouvrez une connexion SSH à **myVM2**. Remplacez l’adresse IP par l’adresse que vous avez récupérée à l’étape précédente et le port **222** que vous avez utilisé pour la règle NAT de trafic entrant myVM2. Remplacez le chemin du fichier .pem par l’emplacement où le fichier de clé a été téléchargé.

    ```console
    ssh -i .\Downloads\myKey.pem azureuser@20.190.2.163 -p 222
    ```

9. Dans votre session SSH, mettez à jour vos sources de package, puis installez le dernier package NGINX.

    ```bash
    sudo apt-get -y update
    sudo apt-get -y install nginx
    ``` 

10. Entrez `Exit` pour quitter la session SSH.

## <a name="test-the-web-server"></a>Tester le serveur web

Dans cette section, vous allez ouvrir votre navigateur web et entrer l’adresse IP de l’équilibreur de charge que vous avez récupérée à l’étape précédente.

1. Ouvrez votre navigateur web.

2. Dans la barre d’adresses, entrez l’adresse IP de l’équilibreur de charge. Dans cet exemple, il s’agit de **20.190.2.163**.

3. Le site web NGINX par défaut s’affiche.

    :::image type="content" source="./media/tutorial-load-balancer-port-forwarding-portal/web-server-test.png" alt-text="Capture d’écran du test du serveur web NGINX.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez les machines virtuelles et l’équilibreur de charge en effectuant les étapes suivantes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.  Sélectionnez **Groupes de ressources** dans les résultats de la recherche.

2. Sélectionnez **TutorialLBPF-rg** dans **Groupes de ressources**.

3. Sélectionnez **Supprimer le groupe de ressources**.

4. Entrez **TutorialLBPF-rg** dans **TAPEZ LE NOM DU GROUPE DE RESSOURCES :** . Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment créer un équilibreur de charge interrégional :

> [!div class="nextstepaction"]
> [Créer un équilibreur de charge entre les régions à l’aide du portail Azure](tutorial-cross-region-portal.md)
