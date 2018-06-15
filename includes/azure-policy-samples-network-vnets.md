---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664480"
---
### <a name="virtual-networks"></a>Virtual Network

|  |  |
|---------|---------|
| [Références SKU de passerelle d’application autorisées](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | Exige que les passerelles d’application utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Aucun appairage de réseau avec un réseau ER](../articles/azure-policy/scripts/no-peering-er-net.md) | Interdit un appairage de réseau avec un réseau dans un groupe de ressources spécifié. À utiliser pour empêcher la connexion avec l’infrastructure réseau gérée centrale. Spécifiez le nom du groupe de ressources pour empêcher l’association. |
| [Aucune table d’itinéraires définie par l’utilisateur](../articles/azure-policy/scripts/no-user-def-route-table.md)  |Interdit le déploiement de réseaux virtuels avec une table d’itinéraires définie par l’utilisateur. |
| [NSG X sur chaque sou-réseau](../articles/azure-policy/scripts/nsg-on-subnet.md) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque sous-réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |