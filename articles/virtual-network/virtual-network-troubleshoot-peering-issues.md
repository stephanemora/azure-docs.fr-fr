---
title: Résoudre les problèmes d’appairage de réseaux virtuels
description: Étapes permettant de résoudre la plupart des problèmes d’appairage de réseaux virtuels.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901393"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Résoudre les problèmes d’appairage de réseaux virtuels

Ce guide de dépannage décrit les étapes de résolution de la plupart des problèmes d’[appairage de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

![IMAGE](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Scénario 1 : Configurer l’appairage entre deux réseaux virtuels

Les réseaux virtuels se trouvent-ils dans le même abonnement ou dans des abonnements différents ?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Type de connexion 1 : Les réseaux virtuels se trouvent dans le même abonnement

Pour configurer l’appairage de réseaux virtuels qui se trouvent dans le même abonnement, utilisez les méthodes fournies dans les articles suivants, selon le cas :

* Si les réseaux virtuels se trouvent dans la **même région**, suivez les étapes pour [créer un appairage de réseaux virtuels se trouvant dans le même abonnement](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Si les réseaux virtuels se trouvent dans des **régions différentes**, suivez les étapes pour [configurer l’appairage de réseaux virtuels globaux](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> La connectivité ne fonctionne pas sur Global VNet Peering pour les ressources suivantes : 
>
> * Machines virtuelles derrière une référence (SKU) ILB de base
> * Cache Redis (utilise la référence (SKU) ILB de base)
> * Application Gateway (utilise la référence (SKU) ILB de base)
> * Groupes identiques (utilise la référence (SKU) ILB de base)
> * Clusters Service Fabric (utilise la référence (SKU) ILB de base)
> * SQL Always-on (utilise la référence (SKU) ILB de base)
> * Environnements App Service (ASE) (utilise la référence (SKU) ILB de base)
> * Gestion des API (utilise la référence (SKU) ILB de base)
> * Azure Active Directory Domain Service (ADDS) (utilise la référence (SKU) ILB de base)

Pour plus d’informations, voir [la configuration requise et les contraintes](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) d’un appairage mondial.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Type de connexion 2 : Les réseaux virtuels se trouvent dans des abonnements ou locataires AD différents

Pour configurer l’appairage de réseaux virtuels se trouvant dans des abonnements ou locataires Active Directory différents, suivez les étapes décrites dans [créer un appairage dans des abonnements différents pour Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Pour configurer l’appairage de réseaux, vous devez disposer des autorisations **Contributeur de réseaux** dans les deux abonnements. Pour plus d’informations, voir [Autorisations d’appairage](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Scénario 2 : Configurer l’appairage de réseaux virtuels avec une topologie hub-and-spoke qui utilise des ressources locales

![IMAGE](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Type de connexion 1 : Pour une connexion site à site ou ExpressRoute

Suivez les étapes décrites dans : [Configurer le transit par passerelle VPN pour l’appairage de réseaux virtuels](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Type de connexion 2 : Pour des connexions point à site

1. Suivez les étapes décrites dans : [Configurer le transit par passerelle VPN pour l’appairage de réseaux virtuels](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Une fois que l’appairage de réseaux virtuels établi ou modifié, le package point à site doit être téléchargé et installé à nouveau afin que les clients point à site obtiennent les itinéraires mis à jour vers le réseau virtuel spoke.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Scénario 3 : Configuration de l’appairage de réseaux virtuels avec un topologie hub-and-spoke pour un réseau virtuel Azure

![IMAGE](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Type de connexion 1 : Les réseaux virtuels se trouvent dans la même région

Vous devez configurer une appliance virtuelle réseau dans le réseau virtuel hub et avoir des itinéraires définis par l’utilisateur avec le tronçon suivant « appliance virtuelle réseau » appliqué dans les réseaux virtuels spoke. Pour plus d’informations, voir [Chaînage de services](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Si vous avez besoin d’aide pour configurer un appliance virtuelle réseau, [contactez le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Pour plus d’informations sur la résolution des problèmes de configuration et de routage d’appliance virtuelle réseau, voir [Problèmes d’appliance virtuelle réseau dans Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Type de connexion 2 : Les réseaux virtuels se trouvent dans des régions différentes

Le transit via Global VNet Peering est désormais pris en charge. La connectivité ne fonctionne pas sur Global VNet Peering pour les ressources suivantes :

* Machines virtuelles derrière une référence (SKU) ILB de base
* Cache Redis (utilise la référence (SKU) ILB de base)
* Application Gateway (utilise la référence (SKU) ILB de base)
* Groupes identiques (utilise la référence (SKU) ILB de base)
* Clusters Service Fabric (utilise la référence (SKU) ILB de base)
* SQL Always-on (utilise la référence (SKU) ILB de base)
* Environnements App Service (ASE) (utilise la référence (SKU) ILB de base)
* Gestion des API (utilise la référence (SKU) ILB de base)
* Azure Active Directory Domain Service (ADDS) (utilise la référence (SKU) ILB de base)

Pour en savoir plus sur les exigences et les contraintes d’un appairage mondial, voir [Appairage de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Scénario 4 : Je rencontre un problème de connectivité entre deux réseaux virtuels appairés

Connectez-vous au [portail Azure](https://portal.azure.com/) avec un compte disposant des [rôles et autorisations](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions) nécessaires. Sélectionnez le réseau virtuel, sélectionnez **Appairage**, puis vérifiez le champ **État**. Qu’est-ce que l’état ?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Type de connexion 1 : L’état d’appairage est « Connecté »

Pour résoudre le problème, procédez comme suit :

1. Vérifiez les flux de trafic réseau :

   Utilisez les procédures [Résolution des problèmes de connexion](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) et de [Vérification du flux IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) de la machine virtuelle source à la machine virtuelle de destination pour déterminer s’il existe un groupe de sécurité réseau ou un UDR qui provoque des interférences dans les flux de trafic.

   Si vous utilisez un pare-feu ou une appliance virtuelle réseau, procédez comme suit : 
   1. Documentez les paramètres UDR afin de pouvoir les restaurer une fois cette étape terminée.
   2. Supprimez l’UDR du sous-réseau ou de la carte réseau de la machine virtuelle source qui pointe vers l’appliance virtuelle réseau en tant que tronçon suivant. Vérifiez la connectivité de la machine virtuelle source directement à la destination, qui contourne l’appliance virtuelle réseau. Si cette étape fonctionne, voir l’[utilitaire de résolution des problèmes d’appliance virtuelle réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Obtenez une trace réseau : 
   1. Commencez à capturer une trace réseau sur la machine virtuelle de destination. Pour Windows, vous pouvez utiliser la commande **Netsh**. Pour Linux, utilisez la commande **TCPDump**.
   2. Exécutez la commande **TcpPing** ou **PsPing** de la source à la l’adresse IP de destination.

   * Voici un exemple de commande **TcpPing** : `tcping64.exe -t <destination VM address> 3389`

   3. Une fois la commande **TcpPing** exécutée, arrêtez la capture de la trace réseau sur la destination.
   4. Si des paquets arrivent de la source, cela signifie qu’il n’y a aucun problème de réseau. Examinez le pare-feu de la machine virtuelle et l’application écoutant sur ce port pour localiser le problème de configuration.

   > [!Note]
   > Vous ne pouvez pas vous connecter aux types de ressources suivants via un appairage de réseaux virtuels globaux (réseaux virtuels se trouvant dans des régions différentes) :
   >
   > * Machines virtuelles derrière une référence (SKU) ILB de base
   > * Cache Redis (utilise la référence (SKU) ILB de base)
   > * Application Gateway (utilise la référence (SKU) ILB de base)
   > * Groupes identiques (utilise la référence (SKU) ILB de base)
   > * Clusters Service Fabric (utilise la référence (SKU) ILB de base)
   > * SQL Always-on (utilise la référence (SKU) ILB de base)
   > * Environnements App Service (ASE) (utilise la référence (SKU) ILB de base)
   > * Gestion des API (utilise la référence (SKU) ILB de base)
   > * Azure Active Directory Domain Service (ADDS) (utilise la référence (SKU) ILB de base)

Pour plus d’informations, voir [la configuration requise et les contraintes](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) d’un appairage mondial.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Type de connexion 2 : L’état d’appairage est « Déconnecté »

Vous devez supprimer les appairages des deux réseaux virtuels et les recréer.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Scénario 5 : Je rencontre un problème de connectivité entre un réseau virtuel hub-and-spoke et une ressource locale

Utilisez-vous une passerelle VPN ou d’appliance virtuelle réseau tierce ?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Type de connexion 1 : Mon réseau utilise une passerelle VPN ou d’appliance virtuelle réseau tierce

Pour résoudre les problèmes de connectivité qui affectent une passerelle VPN ou d’appliance virtuelle réseau tierce, voir les articles suivants :

* [Utilitaire de résolution des problèmes d’appliance virtuelle réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Chaînage de services](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Type de connexion 2 : Mon réseau ne fait pas partie d’une passerelle VPN ou d’appliance virtuelle réseau tierce

Les réseaux virtuels hub et spoke ont-ils tous deux une passerelle VPN ?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Les réseaux virtuels hub et spoke ont tous deux une passerelle VPN

L’utilisation d’une passerelle distante n’est pas prise en charge.

En raison d’une limitation d’appairage de réseaux virtuels, l’**utilisation d’une passerelle distante** n’est pas prise en charge sur le réseau virtuel spoke si celui-ci a déjà une passerelle VPN.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>Aucun des réseaux virtuels hub et spoke n’a de passerelle VPN

Pour des connexions site à site ou ExpressRoute, vérifiez les principales causes des problèmes de connectivité du site local au réseau virtuel distant.

* Vérifiez que la case à cocher **Autoriser le trafic transféré** est activée sur le réseau virtuel disposant d’une passerelle.
* Vérifiez que la case à cocher **Utiliser la passerelle distante** est activée sur le réseau virtuel dépourvu de passerelle.
* Demandez à votre administrateur réseau de vérifier vos appareils locaux pour s’assurer que l’espace d’adressage du réseau virtuel distant a bien été ajouté à tous.

Pour des connexions point à site :

* Vérifiez que la case à cocher **Autoriser le trafic transféré** est activée sur le réseau virtuel disposant d’une passerelle.
* Vérifiez que la case à cocher **Utiliser la passerelle distante** est activée sur le réseau virtuel dépourvu de passerelle.
* Téléchargez et installez à nouveau le package client point à site. Les itinéraires de réseau virtuel nouvellement appairés n’ajoutent pas automatiquement d’itinéraires aux clients point à site.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Scénario 6 : Je rencontre un problème de connectivité réseau hub-and-spoke entre des réseaux virtuels spoke se trouvant dans la même région

Vous devez disposer d’un appliance virtuelle réseau dans un réseau hub, configurer des UDR dans des spokes qui ont un appliance virtuelle réseau défini en tant que tronçon suivant, et activer l’option **Autoriser le trafic transféré** dans le réseau virtuel hub.

Pour plus d’informations, voir [Chaînage de services](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining) et discutez de ces exigences avec le [fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) de votre choix.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Scénario 7 : Je rencontre un problème de connectivité réseau hub-and-spoke entre des réseaux virtuels spoke se trouvant dans des régions différentes

Le transit via Global VNet Peering est désormais pris en charge. La connectivité ne fonctionne pas sur Global VNet Peering pour les ressources suivantes :

* Machines virtuelles derrière une référence (SKU) ILB de base
* Cache Redis (utilise la référence (SKU) ILB de base)
* Application Gateway (utilise la référence (SKU) ILB de base)
* Groupes identiques (utilise la référence (SKU) ILB de base)
* Clusters Service Fabric (utilise la référence (SKU) ILB de base)
* SQL Always-on (utilise la référence (SKU) ILB de base)
* Environnements App Service (ASE) (utilise la référence (SKU) ILB de base)
* Gestion des API (utilise la référence (SKU) ILB de base)
* Azure Active Directory Domain Service (ADDS) (utilise la référence (SKU) ILB de base)

Pour plus d’informations, voir [la configuration requise et les contraintes](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) d’un appairage mondial et les [différentes topologies VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Scénario 8 : Je rencontre un problème de connectivité réseau hub-and-spoke entre une application web et le réseau virtuel spoke

Pour résoudre le problème, procédez comme suit :

1. Connectez-vous au portail Azure. Accédez à l’application web, sélectionnez **Mise en réseau**, puis **Intégration de réseau virtuel**.
2. Vérifiez si vous pouvez voir le réseau virtuel distant. Entrez manuellement l’espace d’adressage du réseau virtuel distant (**Synchronisez le réseau** et **Ajouter des itinéraires**).

Pour plus d’informations, consultez les articles suivants :

* [Intégrer une application à un réseau virtuel Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [À propos du routage VPN point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Scénario 9 : Je reçois un message d’erreur lors de la configuration de l’appairage de réseaux virtuels

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Erreur 1 : Le locataire actuel `<TENANT ID>` n’est pas autorisé à accéder à l’abonnement lié

Pour résoudre ce problème, procédez de la manière décrite dans [Créer un appairage – Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="error-2-not-connected"></a>Erreur 2 : Non connecté

Vous devez supprimer les appairages des deux réseaux virtuels et les recréer.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Erreur 3 : Échec de l’appairage d’un réseau virtuel Databricks

Pour résoudre ce problème, configurez l’appairage de réseaux virtuels à partir du panneau **Azure Databricks**, puis spécifiez le réseau virtuel cible à l’aide de l’**ID de ressource**. Pour plus d’informations, voir [Appairer un réseau virtuel Databricks à un réseau virtuel distant](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes de connectivité entre machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)