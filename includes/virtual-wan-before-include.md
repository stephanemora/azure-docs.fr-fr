---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a3b9160f1fb28c68bdbaa540c78b2890dbcab82
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812739"
---
* Vous avez un réseau virtuel auquel vous souhaitez vous connecter. Vérifiez qu’aucun des sous-réseaux de votre réseau local ne chevauche les réseaux virtuels auxquels vous souhaitez vous connecter. Pour créer un réseau virtuel dans le portail Azure, consultez le [guide de démarrage rapide](../articles/virtual-network/quick-create-portal.md).

* Votre réseau virtuel n’a pas de passerelle de réseau virtuel. Si votre réseau virtuel dispose d’une passerelle (VPN ou ExpressRoute), vous devez supprimer toutes les passerelles. Cette configuration nécessite que les réseaux virtuels soient connectés à la passerelle hub Virtual WAN.

* Obtenez une plage d’adresses IP pour la région de votre hub. Le hub est un réseau virtuel qui est créé et utilisé par Virtual WAN. La plage d’adresses que vous spécifiez pour le hub ne peut pas chevaucher les réseaux virtuels existants auxquels vous vous connectez. Elle ne peut pas non plus chevaucher vos plages d’adresses auxquelles vous vous connectez en local. Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, contactez une personne en mesure de vous aider.

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
