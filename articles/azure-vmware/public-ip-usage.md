---
title: Comment utiliser la fonctionnalité d’adresse IP publique dans Azure VMware Solution
description: Cet article explique comment utiliser la fonctionnalité d’adresse IP publique dans Azure Virtual WAN.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: f51f00a9adc1fd122e723909ecaa6193c152d496
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912665"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Comment utiliser la fonctionnalité d’adresse IP publique dans Azure VMware Solution

L’adresse IP publique est une nouvelle fonctionnalité de la connectivité Azure VMware Solution. Elle rend les ressources, notamment les serveurs web, les machines virtuelles et les hôtes, accessibles via un réseau public. 

Vous activez l’accès Internet public de deux manières. 

- Les applications peuvent être hébergées et publiées dans l’équilibreur de charge d’Application Gateway pour le trafic HTTP/HTTPS.
- Elles peuvent être publiées par le biais des fonctionnalités d’adresse IP publique dans Azure Virtual WAN.

Dans le cadre du déploiement d’un cloud privé Azure VMware Solution, lors de l’activation de la fonctionnalité d’adresse IP publique, les composants requis avec l’automatisation sont créés et activés :

-  Virtual WAN

-  Hub Virtual WAN avec connectivité ExpressRoute

-  Services de pare-feu Azure avec adresse IP publique

Cet article explique comment utiliser la fonctionnalité d’adresse IP publique dans Virtual WAN.

## <a name="prerequisites"></a>Prérequis

- Environnement Azure VMware Solution
- Un serveur web s’exécutant dans un environnement Azure VMware Solution.
- Une nouvelle plage d’adresses IP non superposées pour le déploiement du hub Virtual WAN, en général `/24`.

## <a name="reference-architecture"></a>Architecture de référence

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagramme de l’architecture d’IP publique" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Le diagramme d’architecture montre un serveur web client hébergé dans l’environnement Azure VMware Solution et configuré avec des adresses IP privées RFC1918.  Ce service web est mis à la disposition d’Internet via la fonctionnalité d’adresse IP publique de Virtual WAN.  L’adresse IP publique est généralement une traduction d’adresses NAT de destination dans le pare-feu Azure. Avec les règles DNAT, la stratégie de pare-feu traduit les demandes d’adresses IP publiques en adresses privées (serveur web) avec un port.

Les demandes de l’utilisateur atteignent le pare-feu sur une adresse IP publique qui, à son tour, est traduite en adresse IP privée à l’aide des règles DNAT dans le pare-feu Azure. Le pare-feu vérifie la table NAT et, si la demande correspond à une entrée, il transfère le trafic vers l’adresse et le port traduits dans l’environnement Azure VMware Solution.

Le serveur web reçoit la demande et répond avec les informations ou la page demandées au pare-feu, puis le pare-feu transmet les informations à l’utilisateur sur l’adresse IP publique.

## <a name="test-case"></a>Cas de test
Dans ce scénario, vous devez publier le serveur web IIS sur Internet. Utilisez la fonctionnalité d’adresse IP publique dans Azure VMware Solution pour publier le site web sur une adresse IP publique.  Nous allons configurer des règles NAT sur le pare-feu et accéder à la ressource Azure VMware Solution (machines virtuelles avec serveur de ressources) avec une adresse IP publique.

## <a name="deploy-virtual-wan"></a>Déployer Azure Virtual WAN.

1. Connectez-vous au portail Azure, puis recherchez et sélectionnez **Azure VMware Solution**.

1. Sélectionnez le cloud privé Azure VMware Solution.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Sélectionnez le cloud privé Azure VMware Solution." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. Sous **Gérer** , sélectionnez **Connectivité**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Sous Gérer, sélectionnez Connectivité." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Sélectionnez l’onglet **IP publique** , puis sélectionnez **Configurer**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Sélectionnez l’onglet IP publique, puis sélectionnez Configurer." border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Acceptez les valeurs par défaut ou modifiez-les, puis sélectionnez **Créer**.

   - Groupe de ressources du réseau étendu virtuel

   - Nom du réseau étendu virtuel

   - Bloc d’adresses du hub virtuel (utilisant une nouvelle plage d’adresses IP non superposées)

   - Nombre d'adresses IP publiques (1-100)

Il faut environ une heure pour terminer le déploiement de tous les composants. Ce déploiement n’a besoin de se produire qu’une fois pour prendre en charge toutes les adresses IP publiques futures pour cet environnement Azure VMware Solution.  

>[!TIP]
>Vous pouvez surveiller l’état à partir de la zone **Notification**. 

## <a name="view-and-add-public-ip-addresses"></a>Afficher et ajouter des adresses IP publiques

Nous pouvons vérifier et ajouter d’autres adresses IP publiques en suivant les étapes ci-dessous.

1. Dans le portail Azure, recherchez et sélectionnez **Pare-feu**.

1. Sélectionnez un pare-feu déployé, puis sélectionnez **Visiter Azure Firewall Manager pour configurer et gérer ce pare-feu**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Sélectionnez un pare-feu déployé." border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Sélectionnez **Hubs virtuels sécurisés** , puis, dans la liste, sélectionnez un hub virtuel.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Sélectionnez Hubs virtuels sécurisés, puis sélectionnez un hub virtuel dans la liste." lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Dans la page Hub virtuel, sélectionnez **Configuration IP publique** et, pour ajouter une adresse IP publique, sélectionnez **Ajouter**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Dans la page Hub virtuel, sélectionnez Configuration IP publique." border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Indiquez le nombre d’adresses IP requises et sélectionnez **Ajouter**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Sélectionnez Ajouter et indiquez le nombre d’adresses IP requises, puis sélectionnez Ajouter." border="true":::


## <a name="create-firewall-policies"></a>Créer des stratégies de pare-feu

Une fois que tous les composants sont déployés, vous pouvez les voir dans le groupe de ressources ajouté. L’étape suivante consiste à ajouter une stratégie de pare-feu.

1. Dans le portail Azure, recherchez et sélectionnez **Pare-feu**.

1. Sélectionnez un pare-feu déployé, puis sélectionnez **Visiter Azure Firewall Manager pour configurer et gérer ce pare-feu**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Sélectionnez un pare-feu déployé." border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Sélectionnez **Stratégies de pare-feu Azure** , puis sélectionnez **Créer une stratégie de pare-feu Azure**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Sélectionnez Stratégies de pare-feu Azure, puis sélectionnez Créer une stratégie de pare-feu Azure." border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Sous l’onglet **Général** , indiquez les informations requises et sélectionnez **Suivant : Paramètres DNS**. 

1. Sous l’onglet **DNS** , sélectionnez **Désactiver** , puis sélectionnez **Suivant : Règles**.

1. Sélectionnez **Ajouter une collection de règles** , indiquez les informations ci-dessous, sélectionnez **Ajouter** , puis sélectionnez **Suivant : Renseignement sur les menaces**.

   -  Nom
   -  Type de collection de règles : DNAT
   -  Priority
   -  Action de collection de règles : Autoriser
   -  Nom de la règle
   -  Type de source : **Adresse IP**
   -  Source :* *\** _
   -  Protocole :_ *TCP**
   -  Port de destination : **80**
   -  Type de destination : **Adresse IP**
   -  Destination : **Adresse IP publique**
   -  Adresse traduite : **Adresse IP privée du serveur web Azure VMware Solution**
   -  Port traduit : **Port du serveur web Azure VMware Solution**

1. Laissez la valeur par défaut, puis sélectionnez **Suivant : Hubs**.

1. Sélectionnez **Associer un hub virtuel**.

   :::image type="content" source="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png" alt-text="Sous l’onglet Hubs, sélectionnez Associer des hubs virtuels." border="true" lightbox="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png":::

1. Sélectionnez un hub dans la liste, puis sélectionnez **Ajouter**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Sélectionnez un hub dans la liste, puis sélectionnez Ajouter." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Sélectionnez **Suivant : Balises**. 

1. (Facultatif) Créez des paires nom/valeur pour classer vos ressources. 

1. Sélectionnez **Suivant : Vérifier + créer** , puis **Créer**.

## <a name="limitations"></a>Limites

Vous pouvez avoir 100 adresses IP publiques par SDDC.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’utilisation des adresses IP publiques à l’aide d’[Azure Virtual WAN](../virtual-wan/virtual-wan-about.md).

