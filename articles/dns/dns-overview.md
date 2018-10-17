---
title: Présentation d’Azure DNS
description: Vue d’ensemble des services d’hébergement DNS sur Microsoft Azure. Héberger votre domaine sur Microsoft Azure
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: f68281f71ed525798410fb80b0d0175d38050c40
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829917"
---
# <a name="what-is-azure-dns"></a>Présentation d’Azure DNS

Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.

Vous ne pouvez pas utiliser Azure DNS pour acheter un nom de domaine. Moyennant un abonnement annuel, vous pouvez acheter un nom de domaine à l’aide d’[Azure Web Apps](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) ou d’un bureau d’enregistrement de nom de domaine tiers. Vos domaines peuvent alors être hébergés dans Azure DNS pour la gestion des enregistrements. Pour plus d’informations, consultez [Déléguer un domaine à Azure DNS](dns-domain-delegation.md) .

Les fonctionnalités suivantes sont incluses dans Azure DNS :

## <a name="reliability-and-performance"></a>Fiabilité et performances

Les domaines DNS dans Azure DNS sont hébergés sur un réseau global de serveurs de noms DNS. Azure DNS utilise une mise en réseau Anycast afin que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche, pour des performances élevées et une haute disponibilité de domaine.

## <a name="security"></a>Sécurité

Le service Azure DNS est basé sur Azure Resource Manager, vous donnant l’accès à des fonctionnalités telles que :

* [Contrôle d’accès en fonction de rôles](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) : pour déterminer les utilisateurs qui sont autorisés à accéder à des actions spécifiques pour votre organisation.

* [Journaux d’activité](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs) : pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation a modifié une ressource.

* [Verrouillage de ressource](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) : pour verrouiller un abonnement, un groupe de ressources ou une ressource, afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier accidentellement des ressources cruciales.

Pour plus d’informations, consultez [Comment protéger les enregistrements et zones DNS](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Simplicité d'utilisation

Le service Azure DNS peut gérer les enregistrements DNS de vos services Azure et fournir un DNS pour vos ressources externes. Azure DNS est intégré au portail Azure et utilise les mêmes informations d’identification, de facturation et de contrat d’assistance que vos autres services Azure. 

La facturation DNS est basée sur le nombre de zones DNS hébergées dans Azure et sur le nombre de requêtes DNS reçues. Pour en savoir plus sur la tarification, consultez [Tarification d’Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Vos domaines et enregistrements peuvent être gérés via le portail Azure, des cmdlets Azure PowerShell et l’interface CLI Azure multiplateforme. Les applications nécessitant une gestion automatisée de DNS peuvent s’intégrer au service par le biais de l’API REST et des Kits de développement logiciel (SDK).

## <a name="customizable-virtual-networks-with-private-domains"></a>Réseaux virtuels personnalisables avec des domaines privés

Azure DNS prend également en charge les domaines DNS privés (actuellement en préversion publique). Cela vous permet d’utiliser vos propres noms de domaine personnalisés dans vos réseaux virtuels privés au lieu des noms fournis par Azure.

Pour plus d’informations, consultez [Utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md).

## <a name="alias-records"></a>Enregistrements d’alias

Azure DNS prend en charge les jeux d’enregistrements d’alias. Vous pouvez utiliser un jeu d’enregistrements d’alias pour faire référence à une ressource Azure, comme une adresse IP publique Azure ou un profil Traffic Manager. Si l’adresse IP de la ressource sous-jacente change, le jeu d’enregistrements d’alias se met à jour de façon fluide pendant la résolution DNS. Le jeu d’enregistrements d’alias pointe vers l’instance du service, laquelle est associée à une adresse IP. 

Par ailleurs, vous pouvez maintenant pointer votre apex ou votre domaine nu (par exemple, contoso.com) vers un profil Traffic Manager avec un enregistrement d’alias.

Pour plus d’informations, consultez [Vue d’ensemble des enregistrements d’alias Azure DNS](dns-alias.md).


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les enregistrements et zones DNS, consultez [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

* Pour en savoir plus sur la création d’une zone dans Azure DNS, consultez [Créer une zone DNS](./dns-getstarted-create-dnszone-portal.md).

* Pour les questions fréquemment posées sur Azure DNS, consultez le [FAQ sur Azure DNS](dns-faq.md).

