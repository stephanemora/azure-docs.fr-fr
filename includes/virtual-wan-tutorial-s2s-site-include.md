---
title: Fichier include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3652fbeee10f8ddad538446087f399ca625f6b07
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578260"
---
1. Sur la page du portail de votre réseau étendu virtuel, dans la section **Connectivité**, sélectionnez **Sites VPN** pour ouvrir la page correspondante.
1. Sur la page **Sites VPN**, cliquez sur **+Créer un site**.
1. Sur la page **Créer un site VPN**, sous l’onglet **De base**, renseignez les champs suivants :

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Capture d’écran montrant la page Créer un site VPN avec l’onglet Informations de base ouvert." lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Région** : précédemment appelée « Emplacement ». Il s’agit de l’emplacement auquel vous souhaitez créer cette ressource de site.
    * **Nom** : nom par lequel vous souhaitez faire référence à votre site local.
    * **Fournisseur de périphériques** : nom du fournisseur de périphériques VPN (par exemple :  Citrix, Cisco, Barracuda). L’ajout du fournisseur de périphériques peut aider l’équipe Azure à mieux comprendre votre environnement afin d’y ajouter des possibilités d’optimisation supplémentaires ou pour vous aider à résoudre les problèmes.
    * **Espace d’adressage privé** : espace d’adressage IP situé sur votre site local. Le trafic destiné à cet espace d’adressage est acheminé vers votre site local. Cette option est requise lorsque le protocole BGP n’est pas activé pour le site.
    
      >[!NOTE]
      >Si vous modifiez l’espace d’adressage après avoir créé le site (par exemple, en ajoutant un espace d’adressage), la mise à jour des itinéraires effectifs pendant la recréation des composants peut prendre de 8 à 10 minutes.
      >
1. Sélectionnez **Liens** pour ajouter des informations sur les liens physiques au niveau de la branche. Si vous disposez de l’appareil CPE Virtual WAN d’un partenaire, vérifiez auprès de ce dernier que ces informations sont échangées avec Azure dans le cadre de la configuration du chargement des informations de branche à partir de ses systèmes.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Capture d’écran montrant la page Créer un site VPN avec l’onglet Liaisons ouvert." lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Nom du lien** : nom que vous souhaitez fournir pour le lien physique sur le site VPN. Exemple : mylink1.
   * **Vitesse de liaison** – Vitesse de l’appareil VPN à l’emplacement de la branche. Exemple : 50, ce qui signifie que 50 Mbits/s est la vitesse du périphérique VPN à l’emplacement de la branche.
   * **Nom du fournisseur de liaison** – Nom de la liaison physique sur le site VPN. Exemple : ATT, Verizon.
   * **Adresse IP/FQDN de liaison** – Adresse IP publique de l’appareil local utilisant cette liaison. Si vous le souhaitez, vous pouvez fournir l’adresse IP privée de votre périphérique VPN local qui se trouve derrière ExpressRoute. Vous pouvez également inclure un nom de domaine complet. Par exemple, *quelquechose.contoso.com*. Le nom de domaine complet doit pouvoir être résolu à partir de la passerelle VPN. Cela est possible si le serveur DNS qui héberge ce nom de domaine complet est accessible via Internet. L’adresse IP est prioritaire lorsque l’adresse IP et le nom de domaine complet sont tous deux spécifiés.

     >[!NOTE]
     >
     >* Prend en charge une adresse IPv4 par nom de domaine complet. Si le nom de domaine complet doit être résolu en plusieurs adresses IP, la passerelle VPN récupère la première adresse IPv4 dans la liste. Les adresses IPv6 ne sont pas prises en charge pour l’instant.
     >
     >* La passerelle VPN gère un cache DNS qui est actualisé toutes les 5 minutes. La passerelle tente de résoudre les noms de domaine complets pour les tunnels déconnectés seulement. Une réinitialisation de la passerelle ou une modification de la configuration peuvent également déclencher la résolution du nom de domaine complet.
     >
   * **Protocole BGP (Border Gateway Protocol) de liaison** – La configuration du protocole BGP sur une liaison Virtual WAN équivaut à configurer le protocole BGP sur une passerelle VPN Azure. Votre adresse d’homologue BGP local ne doit pas être identique à l’adresse IP publique de votre réseau VPN vers l’appareil ou à l’espace d’adresse du réseau virtuel du site VPN. Utilisez une adresse IP différente sur le périphérique VPN de votre adresse IP BGP homologue. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Spécifiez cette adresse sur le site VPN correspondant, représentant l’emplacement.  Pour les conditions préalables BGP, consultez [À propos de BGP avec la passerelle VPN Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Vous pouvez à tout moment modifier une connexion de liaison VPN pour mettre à jour ses paramètres BGP (adresse IP de peering de la liaison et numéro AS).
1. Vous pouvez ajouter ou supprimer d’autres liaisons. Quatre liens par site VPN sont pris en charge. Par exemple, si quatre fournisseurs de services Internet (ISP) se trouvent à l’emplacement de la branche, vous pouvez créer quatre liens (un pour chaque fournisseur), puis fournir des informations pour chacun de ces liens.
1. Une fois que vous avez terminé de renseigner les champs, sélectionnez **Vérifier et créer** pour vérifier et créer le site.
1. Accédez au hub virtuel de votre choix, puis désélectionnez **Association de hub** pour connecter votre site VPN au hub.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Capture d’écran montrant l’option Se connecter à ce hub." lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
