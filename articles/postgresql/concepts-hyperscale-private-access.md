---
title: Accès privé (préversion) - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Cet article décrit l’accès privé pour Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 8ddefc9ee135b897dc866826208f50f7f9ad21ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084385"
---
# <a name="private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Accès privé (préversion) dans Azure Database pour PostgreSQL - Hyperscale (Citus)

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

Cette page décrit l’option d’accès privé. Pour l’accès public, consultez [cette page](concepts-hyperscale-firewall-rules.md).

> [!NOTE]
>
> L’accès privé est disponible en préversion dans [certaines régions](concepts-hyperscale-limits.md#regions) uniquement.
>
> Si l’option d’accès privé ne peut pas être sélectionnée pour votre groupe de serveurs, même si votre groupe de serveurs se trouve dans une région autorisée, ouvrez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Azure et incluez votre ID d’abonnement Azure pour obtenir l’accès.

## <a name="definitions"></a>Définitions

**Réseau virtuel**. Un réseau virtuel Azure (VNet) est le composant principal fondamental pour les réseaux privés dans Azure. Les réseaux virtuels permettent à de nombreux types de ressources Azure, comme des serveurs de bases de données et des machines virtuelles Azure, de communiquer en toute sécurité entre elles. Les réseaux virtuels prennent en charge les connexions locales, permettent aux hôtes dans plusieurs réseaux virtuels d’interagir entre eux par le biais du peering et offrent des avantages supplémentaires en matière de scalabilité, d’options de sécurité et d’isolation. Chaque point de terminaison privé pour un groupe de serveurs Hyperscale (Citus) requiert un réseau virtuel associé.

**Sous-réseau**. Les sous-réseaux segmentent un réseau virtuel en un ou plusieurs réseaux secondaires.
Chaque réseau secondaire obtient une partie de l’espace d’adressage, ce qui améliore l’efficacité de l’allocation des adresses.  Vous pouvez sécuriser des ressources au sein de sous-réseaux à l’aide de Groupes de sécurité réseau. Pour plus d’informations, consultez Groupes de sécurité réseau.

Lorsque vous sélectionnez un sous-réseau pour un point de terminaison privé Hyperscale (Citus), assurez-vous que les adresses IP privées disponibles dans ce sous-réseau sont suffisantes pour répondre à vos besoins actuels et futurs.

**Point de terminaison privé**. Un point de terminaison privé est une interface réseau qui utilise une adresse IP privée d’un réseau virtuel. Cette interface réseau se connecte de manière privée et sécurisée à un service basé sur Azure Private Link. Les points de terminaison privés placent les services dans votre réseau virtuel.

L’activation de l’accès privé pour Hyperscale (Citus) crée un point de terminaison privé pour le nœud coordinateur du groupe de serveurs. Le point de terminaison permet aux hôtes dans le réseau virtuel sélectionné d’accéder au coordinateur. Vous pouvez également créer des points de terminaison privés pour les nœuds Worker si vous le souhaitez.

**Zone DNS privée**. Une zone DNS privée Azure résout les noms d’hôte au sein d’un réseau virtuel lié et au sein d’un réseau virtuel appairé. Les enregistrements de domaine pour les nœuds Hyperscale (Citus) sont créés dans une zone DNS privée sélectionnée pour leur groupe de serveurs.  Veillez à utiliser des noms de domaine complets (FQDN) pour les chaînes de connexion PostgreSQL des nœuds.

## <a name="private-link"></a>Liaison privée

Vous pouvez utiliser des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour vos groupes de serveurs Hyperscale (Citus) afin de permettre aux hôtes d’un réseau virtuel (VNet) d’accéder en toute sécurité aux données via une liaison [Private Link](../private-link/private-link-overview.md).

Le point de terminaison privé du groupe de serveurs utilise une adresse IP issue de l’espace d’adressage du réseau virtuel. Le trafic entre les hôtes sur le réseau virtuel et les nœuds Hyperscale (Citus) passe par une liaison privée sur le réseau principal Microsoft, ce qui élimine l’exposition à l’Internet public.

Les applications du réseau virtuel peuvent se connecter aux nœuds Hyperscale (Citus) sur le point de terminaison privé de manière fluide, à l’aide des mêmes chaînes de connexion et mécanismes d’autorisation qu’ils utilisent dans tous les cas.

Vous pouvez sélectionner l’accès privé lors de la création d’un groupe de serveurs Hyperscale (Citus), et vous pouvez passer de l’accès public à l’accès privé à tout moment.

### <a name="using-a-private-dns-zone"></a>Utilisation d’une zone DNS privée

Une nouvelle zone DNS privée est automatiquement provisionnée pour chaque point de terminaison privé, sauf si vous sélectionnez l’une des zones DNS privées créées précédemment par Hyperscale (Citus). Pour plus d’informations, consultez la [présentation des zones DNS privées](../dns/private-dns-overview.md).

Le service Hyperscale (Citus) crée des enregistrements DNS tels que `c.privatelink.mygroup01.postgres.database.azure.com` dans la zone DNS privée sélectionnée pour chaque nœud avec un point de terminaison privé. Quand vous vous connectez à un nœud Hyperscale (Citus) à partir d’une machine virtuelle Azure via un point de terminaison privé, Azure DNS résout le nom de domaine complet du nœud en une adresse IP privée.

Les paramètres de zone DNS privée et l’appairage de réseaux virtuels sont indépendants les uns des autres. Si vous voulez vous connecter à un nœud dans le groupe de serveurs à partir d’un client provisionné dans un autre réseau virtuel (dans la même région ou une autre région), vous devez lier la zone DNS privée au réseau virtuel. Pour plus d’informations, consultez [Lier le réseau virtuel](../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
>
> Le service crée également des enregistrements CNAME publics, tels que `c.mygroup01.postgres.database.azure.com` pour chaque nœud. Toutefois, les ordinateurs sélectionnés sur l’Internet public ne peuvent se connecter au nom d’hôte public que si l’administrateur de la base de données autorise un [accès public](concepts-hyperscale-firewall-rules.md) au groupe de serveurs.

Si vous utilisez un serveur DNS personnalisé, vous devez utiliser un redirecteur DNS pour résoudre le nom de domaine complet des nœuds Hyperscale (Citus). L’adresse IP du redirecteur doit être 168.63.129.16. Le serveur DNS personnalisé doit se trouver à l’intérieur du réseau virtuel ou être accessible via le paramètre de serveur DNS du réseau virtuel. Pour en savoir plus, consultez [Résolution de noms utilisant votre propre serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="recommendations"></a>Recommandations

Lorsque vous activez l’accès privé pour votre groupe de serveurs Hyperscale (Citus), tenez compte des points suivants :

* **Taille du sous-réseau** : lors de la sélection de la taille du sous-réseau pour le groupe de serveurs Hyperscale (Citus), prenez en compte les besoins actuels, comme les adresses IP pour le coordinateur ou tous les nœuds de ce groupe de serveurs, et les besoins futurs, comme la croissance de ce groupe de serveurs. Assurez-vous que vous avez suffisamment d’adresses IP privées pour les besoins actuels et futurs. N’oubliez pas, Azure réserve cinq adresses IP dans chaque sous-réseau.
  Vous trouverez plus de détails [sur cette page (FAQ)](../virtual-network/virtual-networks-faq.md#configuration).
* **Zone DNS privée** : les enregistrements DNS avec des adresses IP privées vont être gérés par le service Hyperscale (Citus). Veillez à ne pas supprimer la zone DNS privée utilisée pour les groupes de serveurs Hyperscale (Citus).

## <a name="limits-and-limitations"></a>Limites et limitations

Consultez la page sur [les limites et les limitations](concepts-hyperscale-limits.md) de Hyperscale (Citus).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [activer et gérer l’accès privé](howto-hyperscale-private-access.md) (préversion)
* Suivez un [tutoriel](tutorial-hyperscale-private-access.md) pour voir l’accès privé (préversion) en action.
* En savoir plus sur les [points de terminaison privés](../private-link/private-endpoint-overview.md)
* En savoir plus sur les [réseaux virtuels](../virtual-network/concepts-and-best-practices.md)
* En savoir plus sur les [zones DNS privées](../dns/private-dns-overview.md)