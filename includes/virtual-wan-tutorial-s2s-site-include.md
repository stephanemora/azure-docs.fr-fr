---
title: Fichier include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627699"
---
1. Sur la page du portail de votre réseau étendu virtuel, dans la section **Connectivité**, sélectionnez **Sites VPN** pour ouvrir la page correspondante.
2. Sur la page **Sites VPN**, cliquez sur **+Créer un site**.

   ![Capture d’écran montrant la fenêtre de site à site, avec le volet Créer un site VPN ouvert.](./media/virtual-wan-tutorial-site-include/basics.png "Concepts de base")
3. Sur la page **Créer un site VPN**, sous l’onglet **De base**, renseignez les champs suivants :

    * **Région** : précédemment appelée « Emplacement ». Il s’agit de l’emplacement auquel vous souhaitez créer cette ressource de site.
    * **Nom** : nom par lequel vous souhaitez faire référence à votre site local.
    * **Fournisseur de périphériques** : nom du fournisseur de périphériques VPN (par exemple :  Citrix, Cisco, Barracuda). L’ajout du fournisseur de périphériques peut aider l’équipe Azure à mieux comprendre votre environnement afin d’y ajouter des possibilités d’optimisation supplémentaires ou pour vous aider à résoudre les problèmes.
    * **Border Gateway Protocol** : si vous choisissez **Activer**, toutes les connexions effectuées à partir du site prendront en charge le protocole BGP. Vous allez finalement configurer les informations BGP pour chaque lien à partir du site VPN dans la section Liens. La configuration du protocole BGP sur un réseau Virtual WAN équivaut à configurer le protocole BGP sur une passerelle VPN Azure. Votre adresse d’homologue BGP local ne doit pas être identique à l’adresse IP publique de votre réseau VPN vers l’appareil ou à l’espace d’adresse du réseau virtuel du site VPN. Utilisez une adresse IP différente sur le périphérique VPN de votre adresse IP BGP homologue. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Spécifiez cette adresse sur le site VPN correspondant, représentant l’emplacement. Pour les conditions préalables BGP, consultez [À propos de BGP avec la passerelle VPN Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Vous pouvez à tout moment modifier une connexion VPN pour mettre à jour ses paramètres BGP (IP de peering du lien et numéro AS) une fois que le paramètre BGP du site VPN est activé.
    * **Espace d’adressage privé** : espace d’adressage IP situé sur votre site local. Le trafic destiné à cet espace d’adressage est acheminé vers votre site local. Cette option est requise lorsque le protocole BGP n’est pas activé pour le site.
    
      >[!NOTE]
      >Si vous modifiez l’espace d’adressage après avoir créé le site (par exemple, en ajoutant un espace d’adressage), la mise à jour des itinéraires effectifs pendant la recréation des composants peut prendre de 8 à 10 minutes.
      >
    * **Hubs** : concentrateur auquel votre site doit se connecter. Un site ne peut être connecté qu’aux hubs disposant d’une passerelle VPN. Si vous ne voyez pas de hub, créez d’abord une passerelle VPN dans ce hub.
4. Sélectionnez **Liens** pour ajouter des informations sur les liens physiques au niveau de la branche. Si vous disposez de l’appareil CPE Virtual WAN d’un partenaire, vérifiez auprès de ce dernier que ces informations sont échangées avec Azure dans le cadre de la configuration du chargement des informations de branche à partir de ses systèmes.

   ![Capture d’écran montrant le volet Créer un site VPN avec l’onglet Liens sélectionné.](./media/virtual-wan-tutorial-site-include/links.png "Liens")

    * **Nom du lien** : nom que vous souhaitez fournir pour le lien physique sur le site VPN. Exemple : mylink1.
    * **Nom du fournisseur** : nom du lien physique sur le site VPN. Exemple : ATT, Verizon.
    * **Vitesse** : vitesse du périphérique VPN à l’emplacement de la branche. Exemple : 50, ce qui signifie que 50 Mbits/s est la vitesse du périphérique VPN à l’emplacement de la branche.
    * **Adresse IP/FQDN** : adresse IP publique de l’appareil local utilisant ce lien. Si vous le souhaitez, vous pouvez fournir l’adresse IP privée de votre périphérique VPN local qui se trouve derrière ExpressRoute. Vous pouvez également inclure un nom de domaine complet. Par exemple, *quelquechose.contoso.com*. Le nom de domaine complet doit pouvoir être résolu à partir de la passerelle VPN. Cela est possible si le serveur DNS qui héberge ce nom de domaine complet est accessible via Internet. L’adresse IP est prioritaire lorsque l’adresse IP et le nom de domaine complet sont tous deux spécifiés.

      >[!NOTE]
      >
      >* Prend en charge une adresse IPv4 par nom de domaine complet. Si le nom de domaine complet doit être résolu en plusieurs adresses IP, la passerelle VPN récupère la première adresse IPv4 dans la liste. Les adresses IPv6 ne sont pas prises en charge pour l’instant.
      >
      >* La passerelle VPN gère un cache DNS qui est actualisé toutes les 5 minutes. La passerelle tente de résoudre les noms de domaine complets pour les tunnels déconnectés seulement. Une réinitialisation de la passerelle ou une modification de la configuration peuvent également déclencher la résolution du nom de domaine complet.
      >
5. Vous pouvez utiliser la case à cocher pour supprimer ou ajouter des liens. Quatre liens par site VPN sont pris en charge. Par exemple, si quatre fournisseurs de services Internet (ISP) se trouvent à l’emplacement de la branche, vous pouvez créer quatre liens (un pour chaque fournisseur), puis fournir des informations pour chacun de ces liens.
6. Une fois que vous avez terminé de renseigner les champs, sélectionnez **Vérifier et créer** pour vérifier et créer le site.
7. Affichez l’état sur la page Sites VPN. Le site passera à l’état **Connexion nécessaire** car le site n’a pas encore été connecté au hub.
