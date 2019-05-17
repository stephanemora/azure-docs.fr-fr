---
title: Vue d’ensemble des enregistrements d’alias Azure DNS
description: Vue d’ensemble de la prise en charge des enregistrements d’alias dans Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 5/13/2019
ms.author: victorh
ms.openlocfilehash: 847ad271dac4afc8c8baa2faa8702b3a3ab6cefa
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596699"
---
# <a name="azure-dns-alias-records-overview"></a>Vue d’ensemble des enregistrements d’alias Azure DNS

Les enregistrements d’alias Azure DNS sont des qualifications sur un jeu d’enregistrements DNS. Ils peuvent référencer d’autres ressources Azure de votre zone DNS. Par exemple, vous pouvez créer un jeu d’enregistrements d’alias qui référence une adresse IP publique Azure, et non un enregistrement A. Votre jeu d’enregistrements d’alias pointe de façon dynamique vers une instance du service d’adresse IP publique Azure. Par conséquent, le jeu d’enregistrements d’alias se met à jour sans interruption pendant la résolution DNS.

Un jeu d’enregistrements d’alias est pris en charge pour les types d’enregistrements suivants dans une zone Azure DNS : 

- A
- AAAA
- CNAME

> [!NOTE]
> Si vous envisagez d'utiliser un enregistrement d'alias pour les types d'enregistrements A ou AAAA afin de pointer vers un profil [​Azure Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md), vous devez vous assurer que le profil Traffic Manager ne comporte que des [points de terminaison externes](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Vous devez fournir l'adresse IPv4 ou IPv6 des points de terminaison externes dans Traffic Manager. Vous ne pouvez pas utiliser des noms de domaine complet (FQDN) dans les points de terminaison. Idéalement, utilisez des adresses IP statiques.

## <a name="capabilities"></a>Fonctionnalités

- **Pointer vers une ressource d’adresse IP publique à partir d’un jeu d’enregistrements A/AAAA DNS.** Vous pouvez créer un jeu d’enregistrements A/AAAA et en faire un jeu d’enregistrements d’alias pour pointer vers une ressource d’adresse IP publique. Le jeu d'enregistrements DNS est automatiquement activé si l'adresse IP publique change ou est supprimée. Les enregistrements DNS non résolus qui pointent vers des adresses IP incorrectes sont évités.

- **Pointer vers un profil Traffic Manager à partir d’un jeu d’enregistrements A/AAAA/CNAME DNS.** Vous pouvez créer un jeu d'enregistrements A/AAAA ou CNAME et utiliser des enregistrements d'alias pour le diriger vers un profil Traffic Manager. Cela s'avère particulièrement utile lorsque vous devez acheminer le trafic à l'extrémité d'une zone, car les enregistrements CNAME traditionnels ne sont pas pris en charge pour une extrémité de zone. Supposons par exemple que votre profil Traffic Manager soit myprofile.trafficmanager.net et que la zone DNS de votre entreprise soit contoso.com. Vous pouvez créer un jeu d'enregistrements d'alias de type A/AAAA pour contoso.com (l'extrémité de la zone) et pointer vers myprofile.trafficmanager.net.
- **Pointez sur un point de terminaison Azure Content Delivery Network (CDN)**. Cela est utile lorsque vous créez des sites Web statiques à l’aide du stockage Azure et Azure CDN.
- **Pointer vers un autre jeu d’enregistrements DNS au sein de la même zone.** Les enregistrements d’alias peuvent référencer d’autres jeux d’enregistrements du même type. Par exemple, un jeu d'enregistrements DNS CNAME peut être un alias d'un autre jeu d'enregistrements CNAME. Cette disposition est utile si vous voulez que certains jeux d’enregistrements d’alias soient des alias et d’autres des non-alias.

## <a name="scenarios"></a>Scénarios

Voici quelques scénarios courants mettant en œuvre des enregistrements d’alias.

### <a name="prevent-dangling-dns-records"></a>Empêcher les enregistrements DNS non résolus

Les enregistrements non résolus constituent un problème courant avec les enregistrements DNS traditionnels. Par exemple, les enregistrements DNS qui n'ont pas été mis à jour pour refléter les modifications apportées aux adresses IP. Ce problème se produit en particulier avec les types d'enregistrements A/AAAA ou CNAME.

Avec un enregistrement de zone DNS traditionnel, si l'adresse IP ou l'enregistrement CNAME cible n'existe plus, l'enregistrement DNS qui lui est associé doit être mis à jour manuellement. Dans certaines organisations, une mise à jour manuelle peut ne pas avoir lieu à temps en raison de problèmes de processus ou de la séparation des rôles et des niveaux d'autorisation associés. Par exemple, un rôle peut avoir l’autorisation de supprimer un CNAME ou une adresse IP appartenant à une application. Mais il ne dispose pas d’autorisations suffisantes pour mettre à jour l’enregistrement DNS qui pointe vers ces cibles. Un retard de mise à jour de l'enregistrement DNS peut potentiellement provoquer une interruption pour les utilisateurs.

Les enregistrements d'alias empêchent les références non résolues en associant étroitement le cycle de vie d'un enregistrement DNS à une ressource Azure. Prenons l'exemple d'un enregistrement DNS qualifié en tant qu'enregistrement d'alias et pointant vers une adresse IP publique ou un profil Traffic Manager. Si ces ressources sous-jacentes sont supprimées, l’enregistrement d’alias DNS est supprimé en même temps.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Mise à jour automatique de l'enregistrement DNS lorsque les adresses IP de l'application changent

Ce scénario est semblable au précédent. Une application est peut-être déplacée ou la machine virtuelle sous-jacente redémarrée. Un enregistrement d’alias est alors automatiquement mis à jour quand l’adresse IP change pour la ressource d’adresse IP publique sous-jacente. Cela prévient les risques de sécurité liés à l'acheminement des utilisateurs vers une autre application à laquelle l'ancienne adresse IP publique a été attribuée.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Héberger des applications à charge équilibrée au niveau de l’apex de zone

Le protocole DNS empêche l'attribution d'enregistrements CNAME à l'extrémité de la zone. Par exemple, si votre domaine est contoso.com, vous pouvez créer des enregistrements CNAME pour somelable.contoso.com ; mais vous ne pouvez pas en créer pour contoso.com lui-même.
Cette restriction pose un problème aux propriétaires d'applications qui disposent d'applications à charge équilibrée derrière [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Dans la mesure où l'utilisation d'un profil Traffic Manager nécessite la création d'un enregistrement CNAME, il est impossible de pointer vers le profil Traffic Manager depuis l'extrémité de la zone.

Ce problème peut être résolu en utilisant des enregistrements d'alias. Contrairement aux enregistrements CNAME, les enregistrements d'alias peuvent être créés à l'extrémité de la zone, et les propriétaires d'applications peuvent s'en servir pour pointer un enregistrement situé à l'extrémité de la zone vers un profil Traffic Manager doté de points de terminaison externes. Les propriétaires d’applications peuvent pointer vers le même profil Traffic Manager que celui utilisé pour tout autre domaine dans leur zone DNS.

Par exemple, contoso.com et www\.contoso.com peut pointer vers le même profil Traffic Manager. Pour en savoir plus sur l'utilisation des enregistrements d'alias avec les profils Azure Traffic Manager, consultez la section Étapes suivantes.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point d’extrémité de la zone pour les points de terminaison Azure CDN

Tout comme un profil Traffic Manager, vous pouvez également utiliser des enregistrements d’alias pour pointer votre apex de la zone DNS vers les points de terminaison Azure CDN. Cela est utile lorsque vous créez des sites Web statiques à l’aide du stockage Azure et Azure CDN. Vous pouvez ensuite accéder au site Web sans ajoutant le préfixe « www » à votre nom DNS.

Par exemple, si votre site Web statique se nomme www.contoso.com, vos utilisateurs peuvent accéder à votre site à l’aide de contoso.com sans avoir besoin d’ajouter des www au nom DNS.

Comme décrit précédemment, les enregistrements CNAME ne sont pas pris en charge à l’extrémité de la zone. Par conséquent, vous ne pouvez pas utiliser un enregistrement CNAME pour pointer contoso.com vers votre point de terminaison CDN. Au lieu de cela, vous pouvez utiliser un enregistrement d’alias pour pointer l’extrémité de la zone à un point de terminaison CDN directement.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les enregistrements d’alias, consultez les articles suivant :

- [Tutoriel : Configurer un enregistrement d'alias pour faire référence à une adresse IP publique](tutorial-alias-pip.md)
- [Tutoriel : Configurer un enregistrement d'alias pour prendre en charge des noms de domaine d'apex avec Traffic Manager](tutorial-alias-tm.md)
- [FAQ DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
