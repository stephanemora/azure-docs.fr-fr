---
title: Utiliser Azure Application Gateway avec des machines virtuelles VMware
description: Explique comment utiliser Azure Application Gateway pour gérer le trafic web entrant des serveurs web exécutés sur des machines virtuelles VMware dans l'environnement de cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94cc6e40b88fe631d525f41001034f5dada05397
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015454"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-avs-private-cloud-environment"></a>Utiliser Azure Application Gateway avec des machines virtuelles VMware dans l'environnement de cloud privé AVS

Vous pouvez utiliser Azure Application Gateway pour gérer le trafic web entrant des serveurs web exécutés sur des machines virtuelles VMware dans l'environnement de cloud privé AVS.

Dans un déploiement hybride public-privé, Azure Application Gateway vous permet de gérer le trafic web vers vos applications, de bénéficier d'un serveur frontal sécurisé et de décharger le traitement SSL des services exécutés dans l'environnement VMware. Azure Application Gateway achemine le trafic web entrant vers des instances de pool principal résidant dans des environnements VMware conformément aux règles et sondes d'intégrité configurées.

Cette solution Azure Application Gateway nécessite de répondre aux conditions suivantes :

* J’ai un abonnement Azure.
* Créer et configurer un réseau virtuel Azure et un sous-réseau au sein du réseau virtuel.
* Créez et configurez des règles NSG et appairez votre réseau virtuel avec votre cloud privé AVS à l'aide d'ExpressRoute.
* Créez et configurez votre cloud privé AVS.
* Créez et configurez votre passerelle Azure Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Scénario de déploiement d’Azure Application Gateway

Dans ce scénario, Azure Application Gateway s’exécute dans votre réseau virtuel Azure. Le réseau virtuel est connecté à votre cloud privé AVS via un circuit ExpressRoute. Tous les sous-réseaux du cloud privé AVS sont accessibles par IP à partir des sous-réseaux du réseau virtuel.

![Azure Load Balancer dans un réseau virtuel Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Procédure : déployer la solution

Le processus de déploiement comprend les tâches suivantes :

1. [Vérifier que les conditions préalables sont remplies](#1-verify-prerequisites)
2. [Connecter votre connexion virtuelle Azure au cloud privé AVS](#2-connect-your-azure-virtual-network-to-your-avs-private-cloud)
3. [Déployer une passerelle d’application Azure](#3-deploy-an-azure-application-gateway)
4. [Créer et configurer un pool de machines virtuelles de serveur web dans votre cloud privé AVS](#4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Vérifier la configuration requise

Vérifiez que les conditions préalables suivantes sont remplies :

* Une instance Azure Resource Manager et un réseau virtuel ont déjà été créés.
* Un sous-réseau dédié (pour Application Gateway) a déjà été créé au sein de votre réseau virtuel Azure.
* Un cloud privé AVS a déjà été créé.
* Il n’existe aucun conflit d’adresse IP entre les sous-réseaux IP dans le réseau virtuel et les sous-réseaux dans le cloud privé.

## <a name="2-connect-your-azure-virtual-network-to-your-avs-private-cloud"></a>2. Connecter votre réseau virtuel Azure à votre cloud privé AVS

Pour connecter votre réseau virtuel Azure à votre cloud privé AVS, suivez ce processus.

1. [Sur le portail AVS, copiez les informations de peering ExpressRoute](virtual-network-connection.md).

2. [Configurez une passerelle de réseau virtuel pour votre réseau virtuel Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Liez votre réseau virtuel au circuit ExpressRoute AVS](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Utilisez les informations de peering que vous avez copiées pour lier votre réseau virtuel au circuit ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Déployer une passerelle d’application Azure

Les instructions détaillées pour cela sont disponibles dans [Créer une passerelle d’application avec des règles d’acheminement par chemin d’accès à l’aide du portail Azure](../application-gateway/create-url-route-portal.md). Voici un résumé de la procédure :

1. Créez un réseau virtuel dans votre abonnement et votre groupe de ressources.
2. Créez un sous-réseau (à utiliser comme sous-réseau dédié) au sein de votre réseau virtuel.
3. Créez une instance Application Gateway standard (activez éventuellement WAF) : Sur la page d'accueil du portail Azure, cliquez sur **Ressource** > **Réseau** > **Application Gateway** dans le coin supérieur gauche de la page. Sélectionnez la référence SKU et la taille standard, et fournissez l'abonnement Azure, le groupe de ressources et les informations relatives à l'emplacement. Si nécessaire, créez une nouvelle adresse IP publique pour cette passerelle d’application et fournissez des détails sur le réseau virtuel et le sous-réseau dédié pour la passerelle d’application.
4. Ajoutez un pool principal avec des machines virtuelles et ajoutez-le à votre passerelle d’application.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud"></a>4. Créer et configurer un pool de machines virtuelles de serveur web dans votre cloud privé AVS

Dans vCenter, créez des machines virtuelles avec le système d’exploitation et le serveur web de votre choix (par exemple, Windows/IIS ou Linux/Apache). Choisissez un sous-réseau/réseau VLAN désigné pour la couche web dans votre cloud privé AVS. Vérifiez qu’au moins une carte réseau virtuelle des machines virtuelles du serveur web se trouve sur le sous-réseau de couche web.
