---
title: Utiliser Azure Private Link pour connecter des réseaux à Azure Monitor
description: Configurez une étendue de liaison privée Azure Monitor pour connecter en toute sécurité des réseaux aux Azure Monitor.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: bdd47962b56324f9832070b13644b5489ee38989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562276"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Utiliser Azure Private Link pour connecter des réseaux à Azure Monitor

Avec [Azure Private Link](../../private-link/private-link-overview.md), vous pouvez lier de manière sécurisée les services de la plateforme Azure en tant que service (PAA) à votre réseau virtuel en utilisant des points d’extrémité privés. Pour de nombreux services, il vous suffit de configurer un point de terminaison pour chaque ressource. Toutefois, Azure Monitor est une constellation de différents services interconnectés qui fonctionnent ensemble pour surveiller vos charges de travail. 

## <a name="advantages"></a>Avantages

Grâce à Azure Private Link, vous pouvez :

- Vous connecter en privé à Azure Monitor sans ouvrir d’accès au réseau public
- Vérifier que vos données de surveillance sont accessibles uniquement par le biais de réseaux privés autorisés
- Empêcher l’exfiltration de données à partir de vos réseaux privés en définissant les ressources Azure Monitor spécifiques qui se connectent via votre point de terminaison privé
- Connecter en toute sécurité votre réseau local privé à Azure Monitor à l’aide d’ExpressRoute et d’Azure Private Link
- Conserver tout le trafic au sein du réseau principal Microsoft Azure

Pour plus d’informations, consultez [Principaux avantages d’Azure Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Fonctionnement

Azure Monitor Private Link Scope (AMPLS) connecte des points de terminaison privés (et les réseaux virtuels dans lesquels ils se trouvent) à une ou plusieurs ressources Azure Monitor : des espaces de travail Log Analytics et des composants Application Insights.

![Schéma de la topologie de base des ressources](./media/private-link-security/private-link-basic-topology.png)

* Le point de terminaison privé de votre réseau virtuel lui permet d’atteindre des points de terminaison Azure Monitor par le biais d’adresses IP privées à partir du pool de votre réseau, au lieu d’utiliser les adresses IP publiques de ces points de terminaison. Cela vous permet de continuer à utiliser vos ressources Azure Monitor sans ouvrir votre réseau virtuel à un trafic sortant non requis. 
* Le trafic du point de terminaison privé vers vos ressources Azure Monitor passe par le réseau principal Microsoft Azure et n’est pas acheminé vers des réseaux publics. 
* Vous pouvez configurer chacun de vos espaces de travail ou composants afin d’autoriser ou de refuser l’ingestion et les requêtes provenant de réseaux publics. Vous bénéficiez ainsi d’une protection au niveau des ressources, ce qui vous permet de contrôler le trafic vers des ressources spécifiques.

> [!NOTE]
> Une seule ressource Azure Monitor peut appartenir à plusieurs AMPLS, mais vous ne pouvez pas connecter un réseau virtuel unique à plusieurs AMPLS. 

### <a name="azure-monitor-private-links-and-your-dns-its-all-or-nothing"></a>Liaisons privées Azure Monitor et votre DNS : tout ou rien
Certains services Azure Monitor utilisent des points de terminaison globaux, ce qui signifie qu’ils traitent les demandes ciblant n’importe quel espace de travail/composant. Quand vous configurez une connexion de liaison privée, votre DNS est mis à jour pour mapper les points de terminaison Azure Monitor à des adresses IP privées, afin d’envoyer le trafic via la liaison privée. En ce qui concerne les points de terminaison globaux, la configuration d’une liaison privée (même pour une seule ressource) affecte le trafic vers toutes les ressources. En d’autres termes, il est impossible de créer une connexion de liaison privée uniquement pour un composant ou un espace de travail spécifique.

#### <a name="global-endpoints"></a>Points de terminaison globaux
Plus important encore, le trafic vers les points de terminaison globaux ci-dessous est envoyé via la liaison privée :
* Tous les points de terminaison Application Insights (les points de terminaison gèrent l’ingestion, les métriques en temps réel, le profileur, le débogueur, etc.) vers des points de terminaison Application Insights sont globaux.
* Point de terminaison de requête : le point de terminaison qui gère les requêtes pour Application Insights et Log Analytics est global.

Cela signifie que tout le trafic Application Insights est envoyé à la liaison privée, et que toutes les requêtes vers les ressources Application Insights et Log Analytics sont envoyées à la liaison privée.

Le trafic vers une ressource Application Insights non ajoutée à votre AMPLS ne réussira pas la validation de liaison privée et échouera.

![Diagramme d’un comportement de type tout ou rien](./media/private-link-security/all-or-nothing.png)

#### <a name="resource-specific-endpoints"></a>Points de terminaison propres aux ressources
Tous les points de terminaison Log Analytics, à l’exception du point de terminaison de requête, sont spécifiques à l’espace de travail. Par conséquent, la création d’une liaison privée vers un espace de travail Log Analytics spécifique n’affectera pas le trafic d’ingestion (ou autre) vers d’autres espaces de travail, qui continueront à utiliser les points de terminaison publics de Log Analytics. Toutes les requêtes, toutefois, sont envoyées via la liaison privée.

### <a name="azure-monitor-private-link-applies-to-all-networks-that-share-the-same-dns"></a>La liaison privée Azure Monitor s’applique à tous les réseaux qui partagent le même DNS
Certains réseaux sont composés de plusieurs réseaux virtuels ou d’autres réseaux connectés. Si ces réseaux partagent le même DNS, la configuration d’une liaison privée sur l’un d’eux met à jour le DNS et affecte le trafic sur tous les réseaux. Cela est particulièrement important à noter en raison du comportement « tout ou rien » décrit ci-dessus.

![Schéma des remplacements DNS dans plusieurs réseaux virtuels](./media/private-link-security/dns-overrides-multiple-vnets.png)

Dans le diagramme ci-dessus, VNet 10.0.1.x se connecte d’abord à AMPLS1 puis mappe les points de terminaison globaux Azure Monitor aux adresses IP à partir de sa plage. Par la suite, VNet 10.0.2.x se connecte à AMPLS2 et remplace le mappage DNS des *mêmes points de terminaison globaux* par les adresses IP de sa plage. Comme ces réseaux virtuels ne sont pas appairés, le premier réseau virtuel ne parvient pas à atteindre ces points de terminaison.


## <a name="next-steps"></a>Étapes suivantes
- [Conception de votre configuration Private Link](private-link-design.md)
- Apprenez à [Configurer votre liaison privée](private-link-configure.md)

<h3><a id="connect-to-a-private-endpoint"></a></h3>
