---
title: Fichier Include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ef4edb99ab2d6d4a25f2151c5b63c1c31ff91ef4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724983"
---
* Vous avez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. 

   * Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. 
   * Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../articles/virtual-network/quick-create-portal.md).

* Votre réseau virtuel ne doit pas déjà avoir de passerelle de réseau virtuel. 

   * Si votre réseau virtuel comprend déjà des passerelles (VPN ou ExpressRoute), vous devez toutes les supprimer avant de continuer. 
   * Cette configuration nécessite que les réseaux virtuels soient connectés uniquement à la passerelle du hub Virtual WAN.

* Un hub virtuel est un réseau virtuel qui est créé et utilisé par Virtual WAN. Il s’agit du cœur de votre réseau Virtual WAN au sein d’une région donnée. 

   * Obtenez une plage d’adresses IP pour la région de votre hub virtuel. 
   * La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. 
   * La plage d’adresses ne peut pas chevaucher les plages d’adresses locales auxquelles vous vous connectez. 
   * Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.
