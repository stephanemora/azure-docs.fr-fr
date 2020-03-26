---
title: Fichier include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488949"
---
1. Sur la page du portail de votre réseau étendu virtuel, dans la section **Connectivité**, sélectionnez **Sites VPN** pour ouvrir la page correspondante.
2. Sur la page **Sites VPN**, cliquez sur **+Créer un site**.

   ![Concepts de base](./media/virtual-wan-tutorial-site-include/basics.png "Concepts de base")
3. Sur la page **Créer un site VPN**, sous l’onglet **De base**, renseignez les champs suivants :

    * **Région** : précédemment appelée « Emplacement ». Il s’agit de l’emplacement auquel vous souhaitez créer cette ressource de site.
    * **Nom** : nom par lequel vous souhaitez faire référence à votre site local.
    * **Fournisseur de périphériques** : nom du fournisseur de périphériques VPN (par exemple :  Citrix, Cisco, Barracuda). Cela peut aider l’équipe Azure à mieux comprendre votre environnement afin d’ajouter des possibilités d’optimisation supplémentaires à l’avenir ou pour vous aider à résoudre les problèmes.
    * **Border Gateway Protocol** : si ce champ est activé, toutes les connexions à partir du site seront compatibles avec le protocole BGP. Vous allez finalement configurer les informations BGP pour chaque lien à partir du site VPN dans la section Liens. La configuration du protocole BGP sur un réseau Virtual WAN équivaut à configurer le protocole BGP sur une passerelle VPN Azure. Votre adresse d’homologue BGP local ne doit pas être identique à l’adresse IP publique de votre réseau VPN vers l’appareil ou à l’espace d’adresse du réseau virtuel du site VPN. Utilisez une adresse IP différente sur le périphérique VPN de votre adresse IP BGP homologue. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Toutefois, il ne peut pas s’agir d’une adresse APIPA (169.254.x.x). Spécifiez cette adresse sur le site VPN correspondant, représentant l’emplacement. Pour les conditions préalables BGP, consultez [À propos de BGP avec la passerelle VPN Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Vous pouvez à tout moment modifier une connexion VPN pour mettre à jour ses paramètres BGP (adresse IP de peering sur le lien et n° de système autonome) une fois que le paramètre BGP du site VPN est activé.
    * **Espace d’adressage privé** : espace d’adressage IP situé sur votre site local. Le trafic destiné à cet espace d’adressage est acheminé vers votre site local. Cette option est requise lorsque le protocole BGP n’est pas activé pour le site.
    * **Hubs** : concentrateur auquel votre site doit se connecter. Un site ne peut être connecté qu’aux hubs disposant d’une passerelle VPN. Si vous ne voyez pas de hub, créez d’abord une passerelle VPN dans ce bub.
4. Sélectionnez **Liens** pour ajouter des informations sur les liens physiques au niveau de la branche. Si vous disposez de l’appareil CPE Virtual WAN d’un partenaire, vérifiez auprès de ce dernier que ces informations sont échangées avec Azure dans le cadre de la configuration du chargement des informations de branche à partir de ses systèmes.

   ![links](./media/virtual-wan-tutorial-site-include/links.png "Liens")

    * **Nom du lien** : nom que vous souhaitez fournir pour le lien physique sur le site VPN. Exemple : mylink1.
    * **Nom du fournisseur** : nom du lien physique sur le site VPN. Exemple : ATT, Verizon.
    * **Vitesse** : vitesse du périphérique VPN à l’emplacement de la branche. Exemple : 50, ce qui signifie que 50 Mbits/s est la vitesse du périphérique VPN à l’emplacement de la branche.
    * **Adresse IP** : adresse IP publique du périphérique local utilisant ce lien. Si vous le souhaitez, vous pouvez fournir l’adresse IP privée de votre périphérique VPN local qui se trouve derrière ExpressRoute.
5. Vous pouvez utiliser la case à cocher pour supprimer ou ajouter des liens. Quatre liens par site VPN sont pris en charge. Par exemple, si quatre fournisseurs de services Internet (ISP) se trouvent à l’emplacement de la branche, vous pouvez créer quatre liens. Ce qui signifie un lien par ISP pour lequel vous devez fournir des informations.
6. Une fois que vous avez terminé de renseigner les champs, sélectionnez **Vérifier et créer** pour vérifier et créer le site.
7. Affichez l’état sur la page Sites VPN. Le site passera à l’état **Connexion nécessaire** car le site n’a pas encore été connecté au hub.