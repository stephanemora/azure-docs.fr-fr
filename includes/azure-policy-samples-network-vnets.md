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
ms.openlocfilehash: df9c291136fc6a48effb08cd59eeb66486eb641c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003668"
---
### <a name="virtual-networks"></a>Virtual Network

|  |  |
|---------|---------|
| [Références SKU de passerelle d’application autorisées](../articles/governance/policy/samples/allowed-app-gate-sku.md) | Exige que les passerelles d’application utilisent une référence SKU approuvée. Spécifiez un tableau de références SKU approuvées. |
| [Aucun appairage de réseau avec un réseau ER](../articles/governance/policy/samples/no-peering-er-net.md) | Interdit un appairage de réseau avec un réseau dans un groupe de ressources spécifié. À utiliser pour empêcher la connexion avec l’infrastructure réseau gérée centrale. Spécifiez le nom du groupe de ressources pour empêcher l’association. |
| [Aucune table d’itinéraires définie par l’utilisateur](../articles/governance/policy/samples/no-user-def-route-table.md)  |Interdit le déploiement de réseaux virtuels avec une table d’itinéraires définie par l’utilisateur. |
| [NSG X sur chaque sou-réseau](../articles/governance/policy/samples/nsg-on-subnet.md) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque sous-réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |