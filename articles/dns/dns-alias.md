---
title: Vue d’ensemble des enregistrements d’alias Azure DNS
description: Vue d’ensemble de la prise en charge des enregistrements d’alias dans Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092840"
---
# <a name="azure-dns-alias-records-overview"></a>Vue d’ensemble des enregistrements d’alias Azure DNS

Les enregistrements d’alias Azure DNS sont des qualifications sur un jeu d’enregistrements DNS. Ils peuvent référencer d’autres ressources Azure de votre zone DNS. Par exemple, vous pouvez créer un jeu d’enregistrements d’alias qui référence une adresse IP publique Azure, et non un enregistrement A. Votre jeu d’enregistrements d’alias pointe de façon dynamique vers une instance du service d’adresse IP publique Azure. Par conséquent, le jeu d’enregistrements d’alias se met à jour sans interruption pendant la résolution DNS.

Un jeu d’enregistrements d’alias est pris en charge pour les types d’enregistrements suivants dans une zone Azure DNS : 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Les enregistrements d’alias pour les types d’enregistrements A ou AAAA pour Azure Traffic Manager sont pris en charge uniquement pour les types de points de terminaison externes. Vous devez fournir l’adresse IPv4 ou IPv6, de manière appropriée, pour les points de terminaison externes dans Traffic Manager. Dans l’idéal, utilisez des adresses IP statiques pour l’adresse.

## <a name="capabilities"></a>Fonctionnalités

- **Pointer vers une ressource d’adresse IP publique à partir d’un jeu d’enregistrements A/AAAA DNS.** Vous pouvez créer un jeu d’enregistrements A/AAAA et en faire un jeu d’enregistrements d’alias pour pointer vers une ressource d’adresse IP publique.

- **Pointer vers un profil Traffic Manager à partir d’un jeu d’enregistrements A/AAAA/CNAME DNS.** Vous pouvez pointer vers le CNAME d’un profil Traffic Manager à partir d’un jeu d’enregistrements CNAME DNS. contoso.trafficmanager.net en est un exemple. Maintenant, vous pouvez également pointer vers un profil Traffic Manager qui a des points de terminaison externes à partir d’un jeu d’enregistrements A ou AAAA dans votre zone DNS.

   > [!NOTE]
   > Les enregistrements d’alias pour les types d’enregistrements A ou AAAA pour Traffic Manager sont pris en charge uniquement pour les types de points de terminaison externes. Vous devez fournir l’adresse IPv4 ou IPv6, de manière appropriée, pour les points de terminaison externes dans Traffic Manager. Dans l’idéal, utilisez des adresses IP statiques pour l’adresse.
   
- **Pointer vers un autre jeu d’enregistrements DNS au sein de la même zone.** Les enregistrements d’alias peuvent référencer d’autres jeux d’enregistrements du même type. Par exemple, un jeu d’enregistrements CNAME DNS peut être un alias pour un autre jeu d’enregistrements CNAME du même type. Cette disposition est utile si vous voulez que certains jeux d’enregistrements d’alias soient des alias et d’autres des non-alias.

## <a name="scenarios"></a>Scénarios
Voici quelques scénarios courants mettant en œuvre des enregistrements d’alias.

### <a name="prevent-dangling-dns-records"></a>Empêcher les enregistrements DNS non résolus
 Dans des zones Azure DNS, vous pouvez utiliser des enregistrements d’alias pour suivre étroitement le cycle de vie de ressources Azure. Ces ressources sont notamment une adresse IP publique ou un profil Traffic Manager. Les enregistrements non résolus constituent un problème courant avec les enregistrements DNS traditionnels. Ce problème se produit en particulier avec les types d’enregistrements A/AAAA ou CNAME. 

Si le CNAME ou l’adresse IP cible n’existe plus, un enregistrement de zone DNS traditionnel ne le sait pas. Par conséquent, l’enregistrement doit être mis à jour manuellement. Dans certaines organisations, cette mise à jour manuelle ne se produit pas toujours à temps. Elle peut également être problématique en raison de la séparation des rôles et des niveaux d’autorisation associés.

Par exemple, un rôle peut avoir l’autorisation de supprimer un CNAME ou une adresse IP appartenant à une application. Mais il ne dispose pas d’autorisations suffisantes pour mettre à jour l’enregistrement DNS qui pointe vers ces cibles. Il se produit un délai entre le moment où une adresse IP ou un CNAME est supprimé et celui où l’enregistrement DNS pointant vers cet élément est supprimé. Ce délai peut causer une interruption pour les utilisateurs.

Les enregistrements d’alias suppriment la complexité associée à ce scénario. Ils permettent d’éviter des références non résolues. Prenez, par exemple, un enregistrement DNS qui est qualifié en tant qu’enregistrement d’alias pour pointer vers une adresse IP publique ou un profil Traffic Manager. Si ces ressources sous-jacentes sont supprimées, l’enregistrement d’alias DNS est supprimé en même temps. Ce processus permet de garantir que les utilisateurs ne subissent aucune interruption.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Mettre automatiquement à jour les zones DNS en cas de changement des adresses IP d’application

Ce scénario est semblable au précédent. Une application est peut-être déplacée ou la machine virtuelle sous-jacente redémarrée. Un enregistrement d’alias est alors automatiquement mis à jour quand l’adresse IP change pour la ressource d’adresse IP publique sous-jacente. Pour éviter les risques relatifs à la sécurité, dirigez les utilisateurs vers une autre application qui a l’ancienne adresse IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Héberger des applications à charge équilibrée au niveau de l’apex de zone

Le protocole DNS empêche l’affectation de tout enregistrement autre qu’un enregistrement A ou AAAA au niveau de l’apex de zone. contoso.com en est un exemple. Cette restriction pose un problème pour les propriétaires d’application qui ont des applications à charge équilibrée derrière un profil Traffic Manager. Il est impossible de pointer vers le profil Traffic Manager à partir de l’enregistrement d’apex de zone. Les propriétaires d’application doivent donc trouver une solution de contournement. Une redirection au niveau de la couche Application doit rediriger vers un autre domaine à partir de l’apex de zone. Une redirection de contoso.com vers www.contoso.com en est un exemple. Cette disposition présente un point de défaillance unique de la fonction de redirection.

Les enregistrements d’alias éliminent ce problème. Les propriétaires d’applications peuvent maintenant pointer leur enregistrement d’apex de zone vers un profil Traffic Manager disposant de points de terminaison externes. Les propriétaires d’applications peuvent pointer vers le même profil Traffic Manager que celui utilisé pour tout autre domaine dans leur zone DNS. Par exemple, contoso.com et www.contoso.com peuvent pointer vers le même profil Traffic Manager. C’est le cas tant que le profil Traffic Manager ne compte que des points de terminaison externes.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les enregistrements d’alias, consultez les articles suivant :

- [Tutoriel : Configurer un enregistrement d’alias pour référencer une adresse IP publique Azure](tutorial-alias-pip.md)
- [Tutoriel : configurer un enregistrement d’alias pour prendre en charge les noms de domaine apex avec Traffic Manager](tutorial-alias-tm.md)
- [FAQ DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
