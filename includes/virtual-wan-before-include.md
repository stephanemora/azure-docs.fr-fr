---
title: fichier descriptif
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4b1f2b5edf2c9efb79f84c123c0df921dbf9c259
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909397"
---
* Vous avez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. 

   * Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. 
   * Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../articles/virtual-network/quick-create-portal.md).

* Votre réseau virtuel ne doit pas déjà avoir de passerelle de réseau virtuel. 

   * Si votre réseau virtuel comprend déjà des passerelles (VPN ou ExpressRoute), vous devez toutes les supprimer avant de continuer. 
   * Cette configuration nécessite que les réseaux virtuels soient connectés uniquement à la passerelle du hub Virtual WAN.

* Déterminez la plage d’adresses IP que vous souhaitez utiliser pour l’espace d’adressage privé de votre hub virtuel. Cette information est utilisée lors de la configuration de votre hub virtuel. Un hub virtuel est un réseau virtuel qui est créé et utilisé par Virtual WAN. Il s’agit du cœur de votre réseau Virtual WAN au sein d’une région donnée. La plage d’espace d’adressage doit respecter les règles suivantes :

   * La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. 
   * La plage d’adresses ne peut pas chevaucher les plages d’adresses locales auxquelles vous vous connectez. 
   * Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.
