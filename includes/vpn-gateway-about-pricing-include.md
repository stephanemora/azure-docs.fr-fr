---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170560"
---
Vous payez deux choses : les coûts horaires de calcul de la passerelle de réseau virtuel, et les coûts de transfert des données sortantes à partir de la passerelle de réseau virtuel. Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Coûts de calcul de la passerelle de réseau virtuel**<br>Chaque passerelle de réseau virtuel a un coût horaire de calcul. Le prix est basé sur la référence SKU de passerelle que vous spécifiez lorsque vous créez une passerelle de réseau virtuel. Le coût est celui de la passerelle elle-même. Il s’ajoute au coût du transfert des données qui transitent par la passerelle. Le coût d’une configuration actif-actif est identique à celui d’une configuration actif-passif.

**Coût de transfert des données**<br>Les coûts de transfert de données sont calculés en fonction du trafic sortant à partir de la passerelle de réseau virtuel source.

* Si vous envoyez du trafic vers votre périphérique VPN local, il est facturé avec le taux de transfert des données sortantes sur Internet.
* Si vous envoyez le trafic entre les réseaux virtuels dans différentes régions, la tarification est basée sur la région.
* Si vous envoyez le trafic seulement entre les réseaux virtuels qui sont dans la même région, il n’y a aucun coût de données. Le trafic entre les réseaux virtuels dans la même région est gratuit.