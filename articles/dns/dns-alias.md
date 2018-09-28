---
title: Vue d’ensemble des enregistrements d’alias Azure DNS
description: Vue d’ensemble de la prise en charge des enregistrements d’alias dans Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957557"
---
# <a name="azure-dns-alias-records-overview"></a>Vue d’ensemble des enregistrements d’alias Azure DNS

Les enregistrements d’alias Azure DNS sont une qualification sur un jeu d’enregistrements DNS qui vous permet de référencer d’autres ressources Azure à partir de votre zone DNS. Par exemple, vous pouvez créer un jeu d’enregistrements d’alias qui fait référence à une adresse IP publique Azure et non à un enregistrement A. Étant donné que votre jeu d’enregistrements d’alias pointe dynamiquement vers une instance de service d’adresse IP publique Azure, le jeu d’enregistrements d’alias se met à jour en toute transparence durant la résolution DNS.

Un jeu d’enregistrements d’alias est pris en charge pour les types d’enregistrements suivants dans une zone Azure DNS : A, AAAA et CNAME. 

> [!NOTE]
> Les enregistrements d’alias pour les types d’enregistrements A et AAAA pour Traffic Manager sont uniquement pris en charge pour les types Point de terminaison externe. Vous devez fournir l’adresse IPv4 ou IPv6 (dans l’idéal des adresses IP statiques) en fonction des points de terminaison externes dans Traffic Manager.

## <a name="capabilities"></a>Fonctionnalités

- **Pointer vers une ressource d’adresse IP publique à partir d’un jeu d’enregistrements A/AAAA DNS**. Vous pouvez créer un jeu d’enregistrements A/AAAA et en faire un jeu d’enregistrements d’alias pour pointer vers une ressource d’adresse IP publique.
- **Pointer vers un profil Traffic Manager à partir d’un jeu d’enregistrements A/AAAA/CNAME DNS**. Outre le fait de pouvoir pointer vers l’enregistrement CNAME d’un profil Traffic Manager (par exemple, contoso.trafficmanager.net) à partir d’un jeu d’enregistrements CNAME DNS, vous pouvez désormais pointer vers un profil Traffic Manager disposant de points de terminaison externes à partir d’un jeu d’enregistrements A ou AAAA dans votre zone DNS.
   > [!NOTE]
   > Les enregistrements d’alias pour les types d’enregistrements A et AAAA pour Traffic Manager sont uniquement pris en charge pour les types Point de terminaison externe. Vous devez fournir l’adresse IPv4 ou IPv6 (dans l’idéal des adresses IP statiques) en fonction des points de terminaison externes dans Traffic Manager.
- **Pointer vers un autre jeu d’enregistrements DNS au sein de la même zone**. Les enregistrements d’alias peuvent faire référence à d’autres jeux d’enregistrements du même type. Par exemple, un jeu d’enregistrements CNAME DNS peut servir d’alias à un autre jeu d’enregistrements CNAME du même type. Ceci est utile si vous souhaitez que certains jeux d’enregistrements se comportent comme des alias et d’autres comme des non-alias.

## <a name="scenarios"></a>Scénarios
Voici quelques scénarios courants mettant en œuvre des enregistrements d’alias :

### <a name="prevent-dangling-dns-records"></a>Empêcher les enregistrements DNS non résolus
À partir de zones Azure DNS, vous pouvez utiliser des enregistrements d’alias pour suivre étroitement le cycle de vie des ressources Azure, par exemple une adresse IP publique ou un profil Traffic Manager. Un problème qui se produit couramment est la non-résolution des enregistrements DNS traditionnels, en particulier avec les types d’enregistrement A/AAAA ou CNAME. 

Si l’enregistrement CNAME ou l’adresse IP cible n’existe plus, un enregistrement de zone DNS traditionnel n’a aucune connaissance de ce fait et doit être mis à jour manuellement. Dans certaines organisations, cette mise à jour manuelle ne peut pas se produire à temps ou peut être problématique en raison de la séparation des rôles et des niveaux d’autorisation associés.

Par exemple, le rôle ayant l’autorisation de supprimer l’enregistrement CNAME ou l’adresse IP appartenant à une application peut ne pas disposer d’autorisations suffisantes pour mettre à jour l’enregistrement DNS qui pointe vers ces cibles. Il peut donc y avoir un délai entre le moment où l’adresse IP ou un enregistrement CNAME est supprimé et celui où l’enregistrement DNS pointant vers cet élément est supprimé, ce qui peut causer une interruption pour les utilisateurs finaux.

Les enregistrements d’alias suppriment la complexité associée à ce scénario et empêchent de telles références non résolues. Si un enregistrement DNS est qualifié en tant qu’enregistrement d’alias pour pointer vers une adresse IP publique ou un profil Traffic Manager et que ces ressources sous-jacentes sont supprimées, l’enregistrement d’alias DNS est supprimé en même temps. De cette façon, les utilisateurs finaux ne subissent aucune interruption.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Mettre automatiquement à jour les zones DNS en cas de changement des adresses IP d’application

Comme dans le scénario précédent, si une application est déplacée ou que la machine virtuelle sous-jacente est redémarrée, un enregistrement d’alias est automatiquement mis à jour quand l’adresse IP change pour la ressource d’adresse IP publique sous-jacente. Vous pouvez éviter les risques relatifs à la sécurité si les utilisateurs finaux sont dirigés vers une autre application qui a l’ancienne adresse IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Héberger des applications à charge équilibrée au niveau de l’apex de zone

Le protocole DNS empêche l’affectation de tout enregistrement autre qu’un enregistrement A ou AAAA au niveau de l’apex de zone (par exemple : contoso.com). Cela pose un problème pour les propriétaires d’application qui ont des applications à charge équilibrée derrière un profil Traffic Manager, car il leur est impossible de pointer vers le profil Traffic Manager à partir de l’enregistrement d’apex de zone. Les propriétaires d’application sont donc contraints de trouver une solution de contournement. Ils peuvent par exemple définir une redirection au niveau de la couche Application pour rediriger l’apex de zone vers un autre domaine (contoso.com vers www.contoso.com). Il en résulte un point de défaillance unique en termes de la fonctionnalité de redirection.

Les enregistrements d’alias éliminent ce problème. Les propriétaires d’applications peuvent maintenant pointer leur enregistrement d’apex de zone vers un profil Traffic Manager disposant de points de terminaison externes. Cette fonctionnalité permet aux propriétaires d’applications de pointer vers le même profil Traffic Manager que celui utilisé pour tout autre domaine dans leur zone DNS. Par exemple, contoso.com et www.contoso.com peuvent pointer tous les deux vers le même profil Traffic Manager tant que celui-ci ne compte que des points de terminaison externes.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les enregistrements d’alias, consultez les articles ci-dessous :

- [Tutoriel : configurer un enregistrement d’alias pour faire référence à une adresse IP publique](tutorial-alias-pip.md)
- [Tutoriel : configurer un enregistrement d’alias pour prendre en charge les noms de domaine apex avec Traffic Manager](tutorial-alias-tm.md)
- [FAQ DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
