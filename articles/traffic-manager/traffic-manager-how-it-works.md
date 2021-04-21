---
title: Fonctionnement d’Azure Traffic Manager | Microsoft Docs
description: Cet article vous aide à comprendre la manière dont Traffic Manager achemine le trafic afin d’optimiser les performances et la disponibilité de vos applications web.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: 376aa04228113c56f0f797f737833802c9eca021
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029486"
---
# <a name="how-traffic-manager-works"></a>Fonctionnement de Traffic Manager

Azure Traffic Manager vous permet de contrôler la répartition du trafic entre les points de terminaison de votre application. Un point de terminaison est tout service côté Internet hébergé à l’intérieur ou à l’extérieur d’Azure.

Traffic Manager offre deux principaux avantages :

- Distribution du trafic selon l’une des [méthodes de routage du trafic](traffic-manager-routing-methods.md)
- [Analyse continue de l’intégrité des points de terminaison](traffic-manager-monitoring.md) et basculement automatique en cas d’échec des points de terminaison

Quand un client tente de se connecter à un service, il doit d’abord résoudre le nom DNS du service à une adresse IP. Le client se connecte ensuite à l’adresse IP pour accéder au service.

**L’essentiel ici est de comprendre que Traffic Manager fonctionne au niveau DNS, c’est-à-dire au niveau de la couche Application (couche 7).**  Traffic Manager utilise DNS pour diriger les clients vers des points de terminaison de service spécifiques en fonction des règles de la méthode de routage du trafic. Les clients se connectent **directement** au point de terminaison sélectionné. Traffic Manager n’est pas un proxy ou une passerelle. Traffic Manager ne voit pas le trafic entre le client et le service.

## <a name="traffic-manager-example"></a>Exemple Traffic Manager

Contoso Corp a développé un nouveau portail pour ses partenaires. L’URL de ce portail est `https://partners.contoso.com/login.aspx`. L’application est hébergée dans trois régions Azure. Pour améliorer la disponibilité et optimiser la performance globale, les clients utilisent Traffic Manager pour router le trafic client vers le point de terminaison disponible le plus proche.

Pour obtenir cette configuration, ils procèdent comme suit :

1. Ils déploient trois instances de leur service. Les noms DNS de ces déploiements sont « contoso-us.cloudapp.net », « contoso-eu.cloudapp.net » et « contoso-asia.cloudapp.net ».
1. Ils créent un profil Traffic Manager nommé « contoso.trafficmanager.net » et le configurent pour utiliser la méthode de routage du trafic « Performance » sur les trois points de terminaison.
1. Ils configurent leur nom de domaine personnel, « partners.contoso.com » pour pointer vers « contoso.trafficmanager.net », en utilisant un enregistrement CNAME DNS.

![Configuration DNS de Traffic Manager][1]

> [!NOTE]
> Lorsque vous utilisez un domaine personnel avec Azure Traffic Manager, vous devez utiliser un enregistrement CNAME pour pointer votre nom de domaine personnel vers votre nom de domaine Traffic Manager. Les normes DNS ne vous permettent pas de créer un enregistrement CNAME au « sommet » (ou à la racine) d’un domaine. Par conséquent, vous ne pouvez pas créer d’enregistrement CNAME pour « contoso.com » (parfois appelé un domaine « nu »). Vous pouvez uniquement créer un enregistrement CNAME pour un domaine sous « contoso.com », tel que « www.contoso.com ». Pour contourner cette limitation, nous vous recommandons d'héberger votre domaine DNS sur [Azure DNS](../dns/dns-overview.md) et d'utiliser des [enregistrements d'alias](../dns/tutorial-alias-tm.md) pour pointer vers le profil de votre gestionnaire de trafic. Vous pouvez également utiliser une simple redirection HTTP pour diriger les requêtes « contoso.com » vers un autre nom, par exemple « www.contoso.com ».

### <a name="how-clients-connect-using-traffic-manager"></a>Connexion des clients à l’aide de Traffic Manager

Dans la continuation de l’exemple précédent, quand un client demande la page `https://partners.contoso.com/login.aspx`, il exécute la procédure ci-après pour résoudre le nom DNS et établir une connexion :

![Établissement de la connexion à l’aide de Traffic Manager][2]

1. Le client envoie une requête DNS à son service DNS récursif configuré pour résoudre le nom « partners.contoso.com ». Un service DNS récursif, parfois appelé service « DNS local », n’héberge pas directement de domaines DNS. Au lieu de cela, le client déleste le travail de contacter les divers services DNS faisant autorité sur Internet, nécessaire pour résoudre un nom DNS.
2. Pour résoudre le nom DNS, le service DNS récursif recherche les serveurs de noms pour le domaine « contoso.com ». Il contacte ensuite ces serveurs de noms pour demander l’enregistrement DNS « partners.contoso.com ». Les serveurs DNS contoso.com retournent l’enregistrement CNAME qui pointe vers contoso.trafficmanager.net.
3. Ensuite, le service DNS récursif recherche les serveurs de noms pour le domaine « trafficmanager.net », qui sont fournis par le service Azure Traffic Manager. Il envoie alors une demande pour l’enregistrement DNS « contoso.trafficmanager.net » à ces serveurs DNS.
4. Les serveurs de noms Traffic Manager reçoivent la demande. Ils choisissent un point de terminaison en fonction des critères suivants :

    - l’état configuré de chaque point de terminaison (les points de terminaison désactivés ne sont pas retournés) ;
    - l’état actuel de chaque point de terminaison, tel qu’il ressort des contrôles d’intégrité de Traffic Manager (pour plus d’informations, voir la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md)) ;
    - la méthode de routage du trafic choisie (pour plus d’informations, voir [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)).

5. Le point de terminaison choisi est retourné en tant qu’autre enregistrement CNAME DNS. Dans ce cas, supposons que contoso-eu.cloudapp.net est retourné.
6. Ensuite, le service DNS récursif recherche les serveurs de noms pour le domaine « cloudapp.net ». Il contacte ces serveurs de noms pour demander l’enregistrement DNS « contoso-eu.cloudapp.net ». Un enregistrement DNS « A » contenant l’adresse IP du point de terminaison de service basé en Europe est retourné.
7. Le service DNS récursif consolide les résultats et renvoie une seule réponse DNS au client.
8. Le client reçoit les résultats DNS et se connecte à l’adresse IP donnée. Notez qu’il se connecte directement au point de terminaison du service d’application, et non via Traffic Manager. Puisqu’il s’agit d’un point de terminaison HTTPS, le client exécute la négociation SSL/TLS nécessaire, puis soumet une demande HTTP GET pour la page « /login.aspx ».

Le service DNS récursif met en cache les réponses DNS qu’il reçoit. Le programme de résolution DNS sur l’appareil client met également en cache le résultat. La mise en cache permet que les requêtes DNS suivantes reçoivent une réponse plus rapidement, en utilisant les données du cache au lieu d’interroger d’autres serveurs de noms. La durée du cache est déterminée par la propriété « time-to-live » (TTL) de chaque enregistrement DNS. Des valeurs plus courtes entraînent une expiration plus rapide du cache, et donc davantage d’allers-retours avec les serveurs de noms Traffic Manager. Des valeurs plus longues signifient que davantage de temps peut être nécessaire pour diriger le trafic à l’écart d’un point de terminaison défaillant. Traffic Manager vous permet de configurer la durée de vie utilisée dans les réponses DNS de Traffic Manager, sur une valeur comprise entre 0 seconde et 2 147 483 647 secondes (la plage maximale, conformément à la norme [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)). Cela vous permet de choisir la valeur qui répond au mieux aux besoins de votre application.

## <a name="faqs"></a>FAQ

* [Quelle est l’adresse IP utilisée par Traffic Manager ?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Quels types de trafic peuvent être routés à l’aide de Traffic Manager ?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Traffic Manager prend-il en charge les sessions « persistantes » ?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Pourquoi une erreur HTTP s’affiche-t-elle quand j’utilise Traffic Manager ?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Quel est l’impact de Traffic Manager sur les performances ?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Quels protocoles d’application puis-je utiliser avec Traffic Manager ?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [Puis-je utiliser Traffic Manager avec un nom de domaine « nu » ?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Traffic Manager considère-t-il l’adresse de sous-réseau client lors du traitement des requêtes DNS ?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Qu’est-ce que le TTL du DNS et comment affecte-t-il mes utilisateurs ?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Quelles sont les limites minimales et maximales de la durée de vie des réponses de Traffic Manager ?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Comment comprendre le volume de requêtes entrantes sur mon profil ?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md)de Traffic Manager.

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md)de Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png
