---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318218"
---
### <a name="virtual-networks"></a>Virtual Network

|  |  |
|---------|---------|
| [Références SKU de passerelle d’application autorisées](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Exige que les passerelles d’application utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Références SKU de passerelle de réseau virtuel autorisées](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Exige que les passerelles de réseau virtuel utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Références SKU d’équilibreur de charge autorisées](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Exige que les équilibreurs de charge réseau virtuel utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Aucun appairage de réseau au réseau Express Route](../articles/governance/policy/samples/no-peering-express-route-network.md) | Interdit un appairage de réseau avec un réseau dans un groupe de ressources spécifié. À utiliser pour empêcher la connexion avec l’infrastructure réseau gérée centrale. Spécifiez le nom du groupe de ressources pour empêcher l’association. |
| [Aucune table d’itinéraires définie par l’utilisateur](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Interdit le déploiement de réseaux virtuels avec une table d’itinéraires définie par l’utilisateur. |
| [NSG X sur chaque sou-réseau](../articles/governance/policy/samples/nsg-on-subnet.md) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque sous-réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |