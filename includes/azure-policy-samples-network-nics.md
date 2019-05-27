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
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155377"
---
### <a name="network-interfaces"></a>Interfaces réseau

|  |  |
|---------|---------|
| [NSG X sur chaque carte réseau](../articles/governance/policy/samples/nsg-on-nic.md) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque interface de réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |