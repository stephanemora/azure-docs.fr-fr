---
title: Recommandations réseau pour Azure Security Center
description: Cet article liste les recommandations de sécurité d’Azure Security Center qui vous permettent de protéger vos ressources réseau.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781841"
---
# <a name="network-recommendations---reference-guide"></a>Recommandations réseau - Guide de référence

Cet article fournit la liste complète des recommandations que vous pouvez voir dans Azure Security Center concernant la topologie de votre réseau et vos points de terminaison accessible sur Internet.

Vous trouverez une explication de la façon de les trouver et de les résoudre [ici](security-center-network-recommendations.md).

## <a name="network-recommendations"></a>Recommandations pour le réseau

|Nom de la recommandation|Description|severity|Degré de sécurisation|Type de ressource|
|----|----|----|----|----|----|
|Les groupes de sécurité réseau situés au niveau du sous-réseau doivent être activés|Activez des groupes de sécurité réseau pour contrôler l'accès réseau des ressources déployées sur vos sous-réseaux.|Élevé / Moyen|30|Subnet|
|Les machines virtuelles doivent être associées à un groupe de sécurité réseau|Activez des groupes de sécurité réseau pour contrôler l’accès réseau de vos machines virtuelles.|Élevé / Moyen|30|Machine virtuelle|
|L'accès doit être limité pour les groupes de sécurité réseau permissifs avec machines virtuelles connectées à Internet|Renforcez les groupes de sécurité réseau de vos machines virtuelles accessibles sur Internet, en limitant l'accès des règles d'autorisation existantes.|Élevé|20|Machine virtuelle|
|Les règles relatives aux applications web doivent être renforcées sur les groupes de sécurité réseau IaaS|Renforcez le groupe de sécurité réseau (NSG) des machines virtuelles qui exécutent des applications web lorsque les règles NSG sont trop permissives en ce qui concerne les ports des applications web.|Élevé|20|Machine virtuelle|
|L'accès à App Services doit être limité|Limitez l'accès à App Services en modifiant la configuration du réseau pour empêcher le trafic entrant provenant de plages trop larges.|Élevé|10|App Service|
|Les ports de gestion doivent être fermés sur vos machines virtuelles|Renforcez le groupe de sécurité réseau de vos machines virtuelles de manière à limiter l'accès aux ports de gestion.|Élevé|10|Machine virtuelle|
DDoS Protection Standard doit être activé|Protégez les réseaux virtuels contenant des applications avec adresses IP publiques en activant la norme de service de protection DDoS. La protection DDoS permet de limiter les risques d'attaques volumétriques et les attaques par protocole réseau.|Élevé|10|Réseau virtuel|
|Le transfert IP doit être désactivé sur votre machine virtuelle|Désactivez le transfert IP. Lorsque le transfert IP est activé sur la carte réseau d’une machine virtuelle, cette dernière peut recevoir du trafic adressé à d’autres destinations. Le transfert IP n’est que rarement nécessaire (par exemple, lors de l’utilisation de la machine virtuelle en tant qu’appliance virtuelle réseau). Par conséquent, un examen par l’équipe de sécurité réseau est requis.|Moyenne|10|Machine virtuelle|
|L'application web ne doit pas être accessible via HTTPS|Activez l'accès « HTTPS uniquement » pour les applications web. L'utilisation de HTTPS garantit l'authentification du serveur/service, et protège les données en transit contre les attaques par écoute clandestine de la couche réseau.|Moyenne|20|Application web|
|Le contrôle d'accès réseau juste-à-temps doit être appliqué sur les machines virtuelles|Appliquez un contrôle d'accès de machine virtuelle Juste-à-temps (JAT) pour bloquer définitivement l'accès aux ports sélectionnés, et permettez aux utilisateurs autorisés de les ouvrir, via JAT, pour une durée limitée.|Élevé|20|Machine virtuelle|
|Les applications Function App ne doivent être accessibles que via HTTPS|Activez l'accès « HTTPS uniquement » pour les applications Function App. L'utilisation de HTTPS garantit l'authentification du serveur/service, et protège les données en transit contre les attaques par écoute clandestine de la couche réseau.|Moyenne|20|Conteneur de fonctions|
|La sécurisation du transfert vers des comptes de stockage doit être activée|Activez le transfert sécurisé vers des comptes de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L'utilisation de HTTPS garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|Élevé|20|Compte de stockage|


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Superviser l’identité et l’accès](security-center-identity-access.md)
* [Protection de vos machines et applications](security-center-virtual-machine-protection.md)
* [Protection de votre service SQL Azure](security-center-sql-service-recommendations.md)

