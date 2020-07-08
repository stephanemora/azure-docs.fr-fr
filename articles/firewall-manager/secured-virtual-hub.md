---
title: Qu’est-ce qu’un hub virtuel sécurisé ?
description: Découvrir des informations sur les hubs virtuels sécurisés
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 56e0d40bcbfb97f57b63dc82da1a6604f83dffad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563628"
---
# <a name="what-is-a-secured-virtual-hub"></a>Qu’est-ce qu’un hub virtuel sécurisé ?

Un hub virtuel est un réseau virtuel géré par Microsoft qui permet la connectivité à partir d’autres ressources. Lorsqu’un hub virtuel est créé à partir d’un Virtual WAN dans le Portail Azure, un réseau virtuel et des passerelles (facultatif) de hub virtuel sont créés en tant que composants.

Un hub virtuel *sécurisé* est un [hub Azure Virtual WAN](../virtual-wan/virtual-wan-about.md#resources) avec des stratégies de sécurité et de routage associées, configurées par Azure Firewall Manager. Utilisez des hubs virtuels sécurisés pour créer facilement des architectures transitives et de réseau en étoile avec des services de sécurité natifs pour la gouvernance et la protection du trafic. 

Vous pouvez utiliser un hub virtuel sécurisé en tant que réseau virtuel central géré sans connectivité locale. Il remplace le réseau virtuel central précédemment requis pour le déploiement d’un Pare-feu Azure. Étant donné que le Hub virtuel sécurisé fournit un routage automatisé, il n’est pas nécessaire de configurer vos propres UDR (itinéraires définis par l’utilisateur) pour acheminer le trafic via votre pare-feu.

Il est également possible d’utiliser des hubs virtuels sécurisés dans le cadre d’une architecture Virtual WAN complète. Cette architecture fournit une connectivité de branche sécurisée, optimisée et automatisée à Azure et via Azure. Vous pouvez choisir les services pour protéger et gérer votre trafic réseau, notamment le Pare-feu Azure et d’autres fournisseurs SECaaS (Security as a service) tiers.

## <a name="create-a-secured-virtual-hub"></a>Créer un hub virtuel sécurisé

À l’aide de Firewall Manager dans le Portail Azure, vous pouvez créer un nouveau hub virtuel sécurisé ou convertir un hub virtuel existant que vous avez créé précédemment à l’aide d’Azure Virtual WAN.

## <a name="next-steps"></a>Étapes suivantes

Pour créer un hub virtuel sécurisé et l’utiliser pour sécuriser et gérer un réseau en étoile, consultez le tutoriel [ : Sécuriser votre réseau cloud avec Azure Firewall Manager à l’aide du Portail Azure](secure-cloud-network.md).