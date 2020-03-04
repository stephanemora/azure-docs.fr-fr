---
title: Configurer des règles d’équilibrage de charge et des règles de trafic sortant à l’aide du Portail Azure
titleSuffix: Azure Load Balancer
description: Cet article explique comment configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide du Portail Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589692"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide du Portail Azure

Cet article explique comment configurer des règles de trafic sortant dans Standard Load Balancer à l’aide du Portail Azure.  

La ressource d’équilibreur de charge contient deux front-ends et les règles qui leur sont associées. Vous disposez d’un front-end pour le trafic entrant et d’un autre front-end pour le trafic sortant.  

Chaque front-end fait référence à une adresse IP publique. Dans ce scénario, l’adresse IP publique pour le trafic entrant est différente de l’adresse pour le trafic sortant.   La règle d’équilibrage de charge fournit uniquement l’équilibrage de charge du trafic entrant. La règle de trafic sortant contrôle la traduction d’adresses réseau (NAT) sortante pour la machine virtuelle.  

Le scénario utilise deux pools back-end : un pour le trafic entrant et un pour le trafic sortant. Ces pools illustrent la fonctionnalité et offrent une certaine flexibilité pour le scénario.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Créer un équilibrage de charge

Dans cette section, vous créez un équilibreur de charge qui équilibrera la charge des machines virtuelles. Vous pouvez créer un équilibreur de charge public ou un équilibreur de charge interne. Lorsque vous créez un équilibreur de charge public, vous créez une adresse IP publique configurée en tant que front-end pour l’équilibreur de charge. Le front-end est nommé **LoadBalancerFrontEnd** par défaut.

1. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Load Balancer**.
2. Dans l’onglet **Fonctions de base** de la page **Créer un équilibreur de charge**, entrez ou sélectionnez les informations suivantes :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Abonnement               | Sélectionnez votre abonnement.    |    
    | Resource group         | Sélectionnez **Créer**, puis entrez **myResourceGroupSLB** dans la zone de texte.|
    | Nom                   | **myLoadBalancer**                                   |
    | Région         | Sélectionnez **Europe Ouest**.                                        |
    | Type          | Sélectionnez **Public**.                                        |
    | SKU           | Sélectionnez **Standard** ou **De base**. Microsoft recommande de sélectionner Standard pour les charges de travail de production. |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. Si vous avez une adresse IP publique existante que vous souhaitez utiliser, sélectionnez **Utiliser l’existant**.  L’IP publique existante doit être une SKU **Standard**.  Les IP publiques De base ne sont pas compatibles avec l’équilibreur de charge **Standard**.  |
    | Nom de l’adresse IP publique              | Tapez **myPublicIP** dans la zone de texte.|
    | Zone de disponibilité | Sélectionnez **Redondant interzone** pour créer un équilibreur de charge résilient. Pour créer un équilibreur de charge zonal, sélectionnez une zone spécifique parmi 1, 2 ou 3. |

3. Acceptez les valeurs par défaut pour le reste de la configuration.
4. Sélectionner **Vérifier + créer**

    > [!IMPORTANT]
    > Le reste de ce guide de démarrage rapide suppose que la référence SKU **Standard** est choisie pendant le processus de sélection de SKU ci-dessus.

5. Sous l’onglet **Review + create (Vérifier + créer)** , sélectionnez **Créer**.   

    ![Créer un équilibreur de charge standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Créer les ressources d’équilibreur de charge

Dans cette section, vous allez configurer les paramètres de l’équilibreur de charge pour un pool d’adresses principal, mettre en place une sonde d’intégrité, puis spécifier une règle d’équilibrage.

### <a name="create-a-backend-pool"></a>Créer un pool principal

Le pool d’adresses principal contient les adresses IP des cartes d’interface réseau virtuelles dans le pool principal. Créez le pool d’adresses principal **myBackendPool** afin d’inclure des machines virtuelles pour l’équilibrage de charge du trafic Internet.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.
3. Dans la page **Ajouter un pool de backends**, entrez **myBackendPool** comme nom de votre pool principal, puis sélectionnez **Ajouter**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Une sonde d’intégrité est utilisée pour surveiller l’état de votre application. La sonde d’intégrité ajoute ou supprime des machines virtuelles dans l’équilibreur de charge en fonction de leur réponse aux contrôles d’intégrité. Créez une sonde d’intégrité **myHealthProbe** pour surveiller l’intégrité des machines virtuelles.

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Sondes d’intégrité**, puis **Ajouter**.
    
    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHealthProbe**. |
    | Protocol | Sélectionnez **HTTP**. |
    | Port | Entrez **80**.|
    | Intervalle | Entrez **15** pour **l’intervalle** en secondes entre les tentatives de la sonde. |
    | Seuil de défaillance sur le plan de l’intégrité | Sélectionnez **2** pour le **Seuil de défaillance sur le plan de l’intégrité**, soit le nombre d’échecs de sonde consécutifs qui peuvent se produire avant qu’une machine virtuelle soit considérée comme non saine.|
    | | |
4. Sélectionnez **OK**.

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge
Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. 

Vous définissez les éléments suivants :
 - La configuration IP frontale pour le trafic entrant.
 - Le pool d’adresses IP principal qui reçoit le trafic.
 - Les ports source et de destination requis. 

Dans la section suivante, vous allez créer :
 - Une règle d’équilibreur de charge **myHTTPRule** pour écouter le port 80.
 - Un front-end **LoadBalancerFrontEnd**.
 - Un pool d’adresses principal **myBackEndPool** qui utilise également le port 80. 

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.
2. Sous **Paramètres**, sélectionnez **Règles d’équilibrage de charge**, puis **Ajouter**.
3. Pour configurer la règle d’équilibrage de charge, utilisez les valeurs suivantes :
    
    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **MyHTTPRule**. |
    | Protocol | Sélectionnez **TCP**. |
    | Port | Entrez **80**.|
    | Port principal | Entrez **80**. |
    | Pool principal | Sélectionnez **MyBackendPool**.|
    | Sonde d’intégrité | Sélectionnez **myHealthProbe**. |
    | Créer des règles de trafic sortant implicites | Sélectionnez **Non**. Nous allons créer des règles de trafic sortant dans une section ultérieure à l’aide d’une IP publique dédiée. |
4. Laissez les autres valeurs par défaut, puis sélectionnez **OK**.

## <a name="create-outbound-rule-configuration"></a>Créer une configuration de règle de trafic sortant
Les règles de trafic sortant de l’équilibreur de charge configurent la SNAT sortante pour les machines virtuelles dans le pool principal. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Créer une adresse IP publique sortante et un front-end

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Configuration d’adresses IP frontales**, puis **Ajouter**.

3. Utilisez ces valeurs pour configurer la configuration d’adresses IP frontales du trafic sortant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **LoadBalancerFrontEndOutbound**. |
    | Version de l’adresse IP | Sélectionnez **IPv4**. |
    | Type IP | Sélectionnez **Adresse IP**.|
    | Adresse IP publique | Sélectionnez **Créer nouveau**. Dans la page **Ajouter une adresse IP publique**, entrez **myPublicIPOutbound**.  Sélectionnez **OK**. |

4. Sélectionnez **Ajouter**.

### <a name="create-an-outbound-backend-pool"></a>Créer un pool principal de trafic sortant

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Pools principaux**, puis **Ajouter**.

3. Dans la page **Ajouter un pool principal**, entrez **myBackendPoolOutbound** comme nom de votre pool principal, puis sélectionnez **Ajouter**.

### <a name="create-outbound-rule"></a>Créer une règle de trafic sortant

1. Sélectionnez **Tous les services** dans le menu de gauche, **Toutes les ressources**, puis **myLoadBalancer** dans la liste des ressources.

2. Sous **Paramètres**, sélectionnez **Règles de trafic sortant**, puis **Ajouter**.

3. Pour configurer les règles de trafic sortant, utilisez les valeurs suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez **myOutboundRule**. |
    | Adresse IP du serveur frontal | Sélectionnez **LoadBalancerFrontEndOutbound**. |
    | Délai d’inactivité (minutes) | Déplacez le curseur sur **15 minutes.|
    | Réinitialisation du protocole TCP | Sélectionnez **Enabled**.|
    | Pool principal | Sélectionnez **myBackendPoolOutbound** |
    | Allocation de port -> Allocation de port | Sélectionnez **Choisir manuellement le nombre de ports de sortie** |
    | Ports de sortie -> Choisir par | Sélectionnez **Ports par instance** |
    | Ports de sortie -> Ports par instance | Entrez **10 000**. |

4. Sélectionnez **Ajouter**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Sélectionnez le groupe de ressources **myResourceGroupSLB** qui contient l’équilibreur de charge, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Contenu de cet article :
 - Vous avez créé un équilibreur de charge standard.
 - Configuration des règles de trafic entrant et sortant de l’équilibreur de charge.
 - Configuration d’une sonde d’intégrité pour les machines virtuelles dans le pool principal. 

Pour en savoir plus, passez aux [tutoriels concernant Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
