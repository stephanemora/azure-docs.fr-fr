---
title: Types de point de terminaison Traffic Manager | Microsoft Docs
description: Cet article explique les différents types de points de terminaison pouvant être utilisés avec Azure Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051225"
---
# <a name="traffic-manager-endpoints"></a>Points de terminaison Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic réseau sur vos déploiements d’applications exécutés dans différents centres de données. Vous pouvez configurer chaque déploiement d’application en tant que « point de terminaison » dans Traffic Manager. Lorsque Traffic Manager reçoit une demande DNS, il choisit un point de terminaison disponible à renvoyer dans la réponse DNS. Traffic Manager base son choix sur l’état du point de terminaison actuel et la méthode de routage du trafic. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](traffic-manager-how-it-works.md).

Traffic Manager prend en charge trois types de points de terminaison :

* **points de terminaison Azure** sont utilisés pour les services hébergés dans Azure.
* Les **points de terminaison externes** sont utilisés pour les adresses IPv4/IPv6, les noms de domaine complets ou pour les services hébergés en dehors d’Azure. Ces services peuvent être locaux ou hébergés par un autre fournisseur d’hébergement.
* Les **points de terminaison imbriqués** servent à combiner différents profils Traffic Manager pour créer des schémas de routage du trafic plus souples afin de répondre aux besoins des déploiements plus vastes et plus complexes.

Différents types de points de terminaison peuvent être combinés dans un même profil Traffic Manager sans la moindre restriction. Chaque profil peut contenir n’importe quelle combinaison de types de points de terminaison.

Les sections suivantes décrivent de manière plus approfondie chaque type de point de terminaison.

## <a name="azure-endpoints"></a>points de terminaison Azure

Les points de terminaison Azure sont utilisés pour les services Azure dans Traffic Manager. Les types de ressource Azure suivants sont pris en charge :

* Services cloud PaaS.
* Web Apps
* Emplacements d’application web
* Ressources PublicIPAddress (pouvant être connectées aux machines virtuelles directement ou par le biais de l’Azure Load Balancer). publicIpAddress doit se voir affecter un nom DNS pour être utilisé dans un profil Traffic Manager.

Les ressources PublicIPAddress sont des ressources Azure Resource Manager. Ils n’existent pas dans le modèle de déploiement classique et sont uniquement pris en charge dans les expériences Azure Resource Manager de Traffic Manager. Les autres types de points de terminaison sont pris en charge via les modèles de déploiement Resource Manager et Classic.

Quand des points de terminaison Azure sont utilisés, Traffic Manager détecte quand une application web est arrêtée ou démarrée. Cet état est reflété dans l’état du point de terminaison. Pour plus d’informations, consultez [Surveillance des points de terminaison Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status). Quand le service sous-jacent est arrêté, Traffic Manager n’effectue ni les vérifications d’intégrité des points de terminaison, ni la direction du trafic vers le point de terminaison. Aucun événement de facturation Traffic Manager ne se produit pour l’instance arrêtée. Lorsque le service est redémarré, la facturation reprend et le point de terminaison est éligible pour recevoir le trafic. Cette détection ne s’applique pas aux points de terminaison PublicIpAddress.

## <a name="external-endpoints"></a>points de terminaison externes

Des points de terminaison externes sont utilisés pour les adresses IPv4/IPv6, pour les noms de domaine complets ou pour des services hébergés en dehors d’Azure. L’usage de points de terminaison d’adresse IPv4/IPv6 autorise Traffic Manager à vérifier l’intégrité de ceux-ci sans exiger de nom DNS. Par conséquent, Traffic Manager peut répondre à des requêtes avec des enregistrements A/AAAA lors du retour de ce point de terminaison dans une réponse. Des services extérieurs à Azure peuvent inclure un service hébergé en local ou avec un autre fournisseur. Les points de terminaison externes peuvent être utilisés individuellement ou être combinés avec des points de terminaison Azure dans le même profil Traffic Manager. L’exception concerne les points de terminaison spécifiés en tant qu’adresses IPv4 ou IPv6, qui peuvent uniquement être des points de terminaison externes. La combinaison de points de terminaison Azure avec des points de terminaison externes permet la prise en charge de divers scénarios :

* Renforcer la redondance d’une application locale existante dans un modèle de basculement actif-actif ou actif-passif en utilisant Azure. 
* Acheminer le trafic vers des points de terminaison qui n’ont pas de nom DNS associé. Cela réduit en outre la latence de recherche DNS globale en éliminant la nécessité d’exécuter une deuxième requête DNS pour obtenir une adresse IP d’un nom DNS retourné.
* Réduire la latence des applications pour les utilisateurs dans le monde entier, étendre une application locale vers d’autres emplacements géographiques dans Azure. Pour plus d’informations, consultez [Routage du trafic Traffic Manager basé sur les performances](traffic-manager-routing-methods.md#performance).
* Augmentez la capacité pour une application locale existante, soit en continu, soit en mode « rafale dans le cloud » pour gérer un pic de demande à l’aide d’Azure.

Dans certains cas, il est utile d’utiliser des points de terminaison externes pour référencer des services Azure. Consultez le [Forum aux questions](traffic-manager-faqs.md#traffic-manager-endpoints) pour obtenir des exemples. La facturation des contrôles d’intégrité dépend de la vitesse des points de terminaison Azure et non de la vitesse des points de terminaison externes. Contrairement aux points de terminaison Azure, si vous arrêtez ou supprimez le service sous-jacent, la facturation du contrôle d’intégrité continue. La facturation s’arrête une fois que vous avez désactivé ou supprimé le point de terminaison dans Traffic Manager.

## <a name="nested-endpoints"></a>points de terminaison imbriqués

Les points de terminaison imbriqués combinent plusieurs profils Traffic Manager pour créer des schémas de routage du trafic souples afin de répondre aux besoins des déploiements plus vastes et plus complexes. Dans le cas des points de terminaison imbriqués, un profil « enfant » est ajouté comme point de terminaison à un profil « parent ». Les profils enfant et parent peuvent contenir d’autres points de terminaison de tout type, y compris d’autres profils imbriqués. 

Pour plus d’informations, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Utilisation d’applications web en tant que points de terminaison

D’autres aspects doivent être pris en compte lors de la configuration d’applications web en tant que points de terminaison dans Traffic Manager :

1. Seules les applications web associées à une référence (SKU) « Standard » ou supérieures peuvent être utilisées avec Traffic Manager. Les tentatives d’ajout d’une application web d’une référence SKU inférieure échouent. Si vous utilisez une application web associée à une référence SKU inférieure, Traffic Manager n’envoie plus de trafic vers cette application web. Pour plus d'informations sur les plans pris en charge, consultez les [Plans App Service](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Lorsqu’un point de terminaison reçoit une requête HTTP, il utilise l’en-tête « host » de la requête pour identifier l’application web qui doit traiter la requête. L’en-tête de l'hôte contient le nom DNS utilisé pour lancer la requête, par exemple « contosoapp.azurewebsites.net ». Pour utiliser un autre nom DNS avec votre application web, le nom DNS doit être enregistré en tant que nom de domaine personnalisé pour l’application. Lorsque vous ajoutez un point de terminaison d’application web en tant que point de terminaison Azure, le nom DNS du profil Traffic Manager est automatiquement enregistré pour l’application. Cet enregistrement est supprimé automatiquement lors de la suppression du point de terminaison.
3. Chaque profil Traffic Manager peut contenir au maximum un point de terminaison d’application web provenant de chaque région Azure. Pour éviter cette contrainte, vous pouvez configurer une application web en tant que point de terminaison externe. Pour plus d’informations, visitez le [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Activation et désactivation des points de terminaison

La désactivation d’un point de terminaison dans Traffic Manager peut être particulièrement utile pour supprimer temporairement le trafic d’un point de terminaison en mode de maintenance ou en cours de redéploiement. Une fois le point de terminaison à nouveau fonctionnel, il peut être réactivé.

Vous pouvez activer ou désactiver des points de terminaison via le portail Traffic Manager, PowerShell, Azure CLI ou une API REST.

> [!NOTE]
> La désactivation d’un point de terminaison Azure n’a aucun lien avec son état de déploiement dans Azure. Un service Azure (tel qu’une machine virtuelle ou une application web) reste en fonction et peut recevoir du trafic même lorsqu’il est désactivé dans Traffic Manager. Le trafic peut être adressé directement sur l’instance de service plutôt que via le nom DNS du profil Traffic Manager. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](traffic-manager-how-it-works.md).

L’éligibilité actuelle de chaque point de terminaison pour recevoir le trafic dépend des facteurs suivants :

* L’état du profil (activé/désactivé)
* L’état du point de terminaison (activé/désactivé)
* Les résultats du contrôle d’intégrité pour ce point de terminaison

Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Comme Traffic Manager fonctionne au niveau du DNS, il est impossible de modifier les connexions existantes à un point de terminaison. Lorsqu’un point de terminaison n’est pas disponible, Traffic Manager dirige les nouvelles connexions vers un autre point de terminaison disponible. Toutefois, l’hôte situé derrière le point de terminaison désactivé ou non sain peut continuer à recevoir le trafic via les connexions existantes, jusqu’à ce que ces sessions s’arrêtent. Les applications doivent limiter la durée de session pour autoriser le trafic à transiter à partir des connexions existantes.

Si tous les points de terminaison d’un profil sont désactivés, ou si le profil lui-même est désactivé, Traffic Manager envoie une réponse « NXDOMAIN » à une nouvelle requête DNS.

## <a name="faqs"></a>FAQ

* [Puis-je utiliser Traffic Manager avec des points de terminaison de plusieurs abonnements ?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Puis-je utiliser Traffic Manager avec les emplacements intermédiaires de services cloud ?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Traffic Manager prend-il en charge les points de terminaison IPv6 ?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Puis-je utiliser Traffic Manager avec plusieurs applications web dans la même région ?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Comment déplacer les points de terminaison Azure de mon profil Traffic Manager vers un autre groupe de ressources ?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-it-works.md).
* En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md)de Traffic Manager.
* En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md)avec Traffic Manager.