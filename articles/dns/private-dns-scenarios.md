---
title: Scénarios pour Private Zones – Azure DNS
description: Dans cet article, vous découvrez des scénarios courants pour l’utilisation d’Azure DNS Private Zones.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/27/2021
ms.author: rohink
ms.openlocfilehash: 5a2572af0fd312efeacb8544b653db1b35809244
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127818"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scénarios Azure DNS Private Zones

Azure DNS Private Zones fournit la résolution de noms au sein d’un réseau virtuel et entre des réseaux virtuels. Dans cet article, nous allons nous pencher sur plusieurs scénarios courants utilisant cette fonctionnalité.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scénario : Résolution de noms pour un seul réseau virtuel

Dans ce scénario, vous disposez d’un réseau virtuel dans Azure doté de nombreuses ressources parmi lesquelles des machines virtuelles. Il vous faut résoudre toutes les ressources du réseau virtuel à l’aide d’un nom de domaine spécifique (zone DNS). Il vous faut également veiller à ce que la résolution de noms soit privée et non accessible sur Internet. Enfin, vous avez besoin d’Azure pour inscrire automatiquement les machines virtuelles dans la zone DNS.

Ce scénario est illustré ci-dessous. Nous avons donc un virtuel nommé « A » contient deux machines virtuelles (VNETA-VM1 et VNETA-VM2). Chaque machine virtuelle est associée à une adresse IP privée. Après avoir créé une zone privée, par exemple `contoso.com`, liez le réseau virtuel « A » en tant que réseau virtuel d’inscription. Azure DNS crée automatiquement deux enregistrements A dans la zone référençant les deux machines virtuelles. Les requêtes DNS de VNETA-VM1 peuvent désormais résoudre `VNETA-VM2.contoso.com` et reçoivent une réponse DNS contenant l’adresse IP privée de VNETA-VM2.
Vous pouvez également effectuer une requête DNS inversée (PTR) pour l’adresse IP privée de VNETA-VM1 (10.0.0.1) à partir de VNETA-VM2. La réponse DNS contiendra le nom VNETA-VM1, comme attendu. 

![Résolution pour un seul réseau virtuel](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scénario : Résolution de noms sur plusieurs réseaux virtuels

Dans ce scénario, vous devez associer une zone privée à plusieurs réseaux virtuels. Vous pouvez implémenter cette solution dans différentes architectures de réseau, telles que le modèle Hub-and-Spoke. Cette configuration intervient lorsqu’un réseau virtuel hub central est utilisé pour connecter plusieurs réseaux virtuels Spoke. Le réseau virtuel hub central peut être associé en tant que réseau virtuel d’inscription, et les réseaux virtuels Spoke peuvent être associés en tant que réseaux virtuels de résolution. 

Le schéma suivant présente une version simplifiée de ce scénario avec uniquement deux réseaux virtuels : A et B. A est défini en tant que réseau virtuel d’inscription et B en tant que réseau virtuel de résolution. L’objectif consiste ici à faire en sorte que les deux réseaux virtuels partagent une même zone `contoso.com`. Lorsque la zone est créée, les réseaux virtuels définis pour l’inscription inscrivent automatiquement les enregistrements DNS correspondant aux machines virtuelles dans le réseau virtuel (VNETA-VM1 et VNETA-VM2). Vous pouvez aussi ajouter manuellement des enregistrements DNS à la zone des machines virtuelles dans le réseau virtuel de résolution B. Avec cette configuration, vous observerez le comportement suivant pour les requêtes DNS directes et inversées :
* Une requête DNS de VNETB-VM1 dans le réseau virtuel de résolution B, pour VNETA-VM1.contoso.com, reçoit une réponse DNS contenant l’adresse IP privée de VNETA-VM1.
* Une requête DNS inverse (PTR) de VNETB-VM2 dans le réseau virtuel de résolution B, pour 10.1.0.1, reçoit une réponse DNS contenant le nom de domaine complet VNETB-VM1.contoso.com.  
* Une requête DNS inverse (PTR) de VNETB-VM3 dans le réseau virtuel de résolution B, pour 10.0.0.1, reçoit NXDOMAIN. Cela s’explique par le fait que l’étendue des requêtes DNS inverses est uniquement le même réseau virtuel. 

![Résolution pour plusieurs réseaux virtuels](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scénario : Fonctionnalité de découpage d’horizon

Dans ce scénario, il vous faut une résolution de noms différente qui dépend de l’emplacement du client pour la même zone DNS. Vous avez peut-être une version privée et une version publique de votre application dotées de fonctionnalités ou de comportements différents. Vous avez demandé l’utilisation du même nom de domaine pour les deux versions. Ce scénario peut être mis en œuvre en créant une zone publique et privée dans Azure DNS portant le même nom. 

Le diagramme suivant illustre ce scénario. Vous avez un virtuel nommé « A » doté deux machines virtuelles (VNETA-VM1 et VNETA-VM2). Toutes deux possèdent une adresse IP privée et une adresse IP publique configurées. Une zone DNS publique nommée `contoso.com` a été créée et enregistre les adresses IP publiques pour ces machines virtuelles en tant qu’enregistrements DNS dans la zone. Une zone DNS privée nommée `contoso.com` a également été créée. Vous avez défini le réseau virtuel A en tant que réseau virtuel d’inscription. Azure enregistre automatiquement les machines virtuelles en tant qu’enregistrements A dans la zone privée, en pointant vers leurs adresses IP privées.

Désormais, lorsqu’un client Internet effectue une requête DNS `VNETA-VM1.contoso.com`, Azure renvoie l’enregistrement d’adresse IP publique de la zone publique. Si la même requête DNS est émise d’une autre machine virtuelle (par exemple, VNETA-VM2) dans le même réseau virtuel A, Azure renvoie l’enregistrement d’adresse IP privée de la zone privée. 

![Découpage - résolution Brian](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les zones DNS privées, consultez la session relative à [l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md).

Découvrez comment [créer une zone DNS privée](./private-dns-getstarted-powershell.md) dans Azure DNS.

Obteniez plus d’informations sur les zones et enregistrements DNS en consultant : [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

Découvrez certaines des autres [fonctionnalités de réseau](../networking/fundamentals/networking-overview.md) clés d’Azure.
