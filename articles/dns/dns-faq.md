---
title: FAQ Azure DNS
description: Questions fréquentes (FAQ) sur Azure DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 4f0800dfd264059e1dc8aac32a54f216f777647f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905713"
---
# <a name="azure-dns-faq"></a>FAQ Azure DNS

## <a name="about-azure-dns"></a>À propos d’Azure DNS

### <a name="what-is-azure-dns"></a>Présentation d’Azure DNS

Le système DNS (Domain Name System) traduit, ou résout, un nom de site web ou de service en son adresse IP. Azure DNS est un service d’hébergement pour les domaines DNS. Il offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.

Les domaines DNS dans Azure DNS sont hébergés sur le réseau global Azure de serveurs de noms DNS. Ce système utilise la mise en réseau Anycast afin que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche. Azure DNS offre des performances élevées et une haute disponibilité pour votre domaine.

Azure DNS est basé sur Azure Resource Manager. Ce service tire parti de fonctionnalités de Resource Manager comme le contrôle d’accès en fonction du rôle, les journaux d’audit et le verrouillage de ressources. Vous pouvez gérer les domaines et les enregistrements par le biais du portail Azure, des applets de commande Azure PowerShell et de l’interface Azure CLI multiplateforme. Les applications qui nécessitent la gestion DNS automatique peuvent s’intégrer au service par le biais de l’API REST et des SDK.

### <a name="how-much-does-azure-dns-cost"></a>Combien coûte Azure DNS ?

Le modèle de facturation Azure DNS est basé sur le nombre de zones DNS hébergées dans Azure DNS. Il est également basé sur le nombre de requêtes DNS qu’elles reçoivent. Des remises sont proposées en fonction de l’utilisation.

Pour plus d’informations, consultez la [page de tarification d’Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Quel est le contrat de niveau de service (SLA) d’Azure DNS ?

Azure garantit qu’au moins 100 % du temps, les requêtes DNS valides reçoivent une réponse d’au moins un serveur de noms Azure DNS.

Pour plus d’informations, consultez la [page du contrat de niveau de service (SLA) d’Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Qu’est-ce qu’une zone DNS ? Est-ce la même chose qu’un domaine DNS ? 

Un domaine est un nom unique dans le système de noms de domaine. contoso.com en est un exemple.

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Par exemple, le domaine contoso.com peut contenir plusieurs enregistrements DNS. Les enregistrements peuvent inclure mail.contoso.com pour un serveur de messagerie et le www\.contoso.com pour un site Web. Ces enregistrements sont hébergés dans la zone DNS contoso.com.

Un nom de domaine est *juste un nom*. Une zone DNS est une ressource de données contenant les enregistrements DNS pour un nom de domaine. Azure DNS vous permet d’héberger une zone DNS et de gérer les enregistrements DNS pour un domaine dans Azure. Il fournit également des serveurs de noms DNS pour répondre aux requêtes DNS provenant d’Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Dois-je acheter un nom de domaine DNS pour utiliser Azure DNS ? 

Pas nécessairement.

Vous n’avez pas besoin d’acheter un domaine pour héberger une zone DNS dans Azure DNS. Vous pouvez créer une zone DNS à tout moment sans être propriétaire d’un nom de domaine. Les requêtes DNS pour cette zone ne sont résolues que si elles sont dirigées vers les serveurs de noms Azure DNS affectés à la zone.

Pour lier votre zone DNS à la hiérarchie DNS mondiale, vous devez acheter le nom de domaine. Les requêtes DNS du monde entier trouvent alors votre zone DNS et obtiennent en réponse vos enregistrements DNS.

## <a name="azure-dns-features"></a>Fonctionnalités Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>L’utilisation d’enregistrements d’alias pour un apex de nom de domaine avec Traffic Manager est-elle soumise à des restrictions ?

Oui. Vous devez utiliser des adresses IP publiques statiques avec Azure Traffic Manager. Configurez la cible du **point de terminaison externe** à l’aide d’une adresse IP statique. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS prend-il en charge le routage du trafic DNS ou le basculement du point de terminaison ?

Le routage du trafic DNS et le basculement du point de terminaison sont fournis par Traffic Manager. Ce dernier est un service Azure distinct qui peut être utilisé avec Azure DNS. Pour plus d’informations, consultez la [présentation de Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS prend uniquement en charge l’hébergement de domaines DNS statiques, dans lequel chaque requête DNS pour un enregistrement DNS donné reçoit toujours la même réponse DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS prend-il en charge l’inscription de nom de domaine ?

Non. Azure DNS ne prend actuellement pas en charge la possibilité d’acheter des noms de domaine. Pour acheter des domaines, vous devez utiliser un bureau d’enregistrement de noms de domaine tiers. Le bureau d’enregistrement facture généralement des frais annuels peu élevés. Les domaines peuvent alors être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Pour plus d’informations, voir [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

La fonctionnalité permettant d’acheter des noms de domaine est suivie dans le backlog Azure. Utilisez le site de commentaires pour [inscrire votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS prend-il en charge DNSSEC ?

Non. Azure DNS ne prend actuellement pas en charge les extensions de sécurité DNS (DNSSEC).

La fonctionnalité DNSSEC est suivie dans le backlog Azure DNS. Utilisez le site de commentaires pour [inscrire votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS prend-il en charge les transferts de zone (AXFR/IXFR) ?

Non. Azure DNS ne prend actuellement pas en charge les transferts de zone. Les zones DNS peuvent être [importées dans Azure DNS à l’aide de l’interface Azure CLI](dns-import-export.md). Les enregistrements DNS sont gérés par le biais du [portail de gestion Azure DNS](dns-operations-recordsets-portal.md), [l’API REST](https://docs.microsoft.com/powershell/module/az.dns), le [SDK](dns-sdk.md), les [applets de commande PowerShell](dns-operations-recordsets.md) ou [l’outil CLI](dns-operations-recordsets-cli.md).

La fonctionnalité de transfert de zone est suivie dans le backlog Azure DNS. Utilisez le site de commentaires pour [inscrire votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS prend-il en charge les redirections d’URL ?

Non. Les services de redirection d’URL ne sont pas un service DNS. Ils fonctionnent au niveau HTTP, et non pas au niveau DNS. Certains fournisseurs DNS incluent un service de redirection d’URL dans leur offre globale. Ce service n’est actuellement pas pris en charge par Azure DNS.

La fonctionnalité de redirection d’URL est suivie dans le backlog Azure DNS. Utilisez le site de commentaires pour [inscrire votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS prend-il en charge le jeu de codage ASCII étendu (8 bits) pour les jeux d’enregistrements TXT ?

Oui. Azure DNS prend en charge le jeu de codage ASCII étendu pour les jeux d’enregistrements TXT. Vous devez toutefois utiliser la dernière version des API REST Azure, des SDK, de PowerShell et de l’interface CLI. Les versions antérieures au 1er octobre 2017 ou au SDK 2.1 ne prennent pas en charge le jeu ASCII étendu. 

Par exemple, pour un enregistrement TXT comportant le caractère ASCII étendu \128, vous pouvez fournir une chaîne en tant que valeur. Par exemple, « abcd\128efgh ». Azure DNS utilise la valeur d’octet de ce caractère (128), dans la représentation interne. Au moment de la résolution DNS, cette valeur d’octet s’affiche dans la réponse. Notez également que « abc » et « \097\098\099 » sont interchangeables en ce qui concerne la résolution. 

Nous suivons les règles d’échappement de format maître de zone de fichiers [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt). Par exemple, `\` échappe maintenant tous les éléments conformément à la RFC. Si vous spécifiez `A\B` comme valeur d’enregistrement TXT, elle est représentée et résolue simplement comme `AB`. Si vous voulez vraiment que l’enregistrement TXT soit `A\B` à la résolution, vous devez de nouveau échapper le caractère `\`. Par exemple, spécifiez `A\\B`.

Cette prise en charge n’est actuellement pas disponible pour les enregistrements TXT créés à partir du portail Azure.

## <a name="alias-records"></a>Enregistrements d’alias

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Dans quels scénarios les enregistrements d’alias sont-ils utiles ?

Consultez la section des scénarios dans la [Vue d’ensemble des enregistrements d’alias Azure DNS](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Quels sont les types d’enregistrements pris en charge pour les jeux d’enregistrements d’alias ?

Les jeux d’enregistrements d’alias sont pris en charge pour les types d’enregistrements suivants dans une zone Azure DNS :
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Quelles sont les ressources prises en charge comme cibles pour les jeux d’enregistrements alias ?

- **Pointer vers une ressource d’adresse IP publique à partir d’un jeu d’enregistrements A/AAAA DNS.** Vous pouvez créer un jeu d’enregistrements A/AAAA et en faire un jeu d’enregistrements d’alias pour pointer vers une ressource d’adresse IP publique.
- **Pointer vers un profil Traffic Manager à partir d’un jeu d’enregistrements A/AAAA/CNAME DNS.** Vous pouvez pointer vers le CNAME d’un profil Traffic Manager à partir d’un jeu d’enregistrements CNAME DNS. contoso.trafficmanager.net en est un exemple. Maintenant, vous pouvez également pointer vers un profil Traffic Manager qui a des points de terminaison externes à partir d’un jeu d’enregistrements A ou AAAA dans votre zone DNS.
- **Pointez sur un point de terminaison Azure Content Delivery Network (CDN)**. Cela est utile lorsque vous créez des sites Web statiques à l’aide du stockage Azure et Azure CDN.
- **Pointer vers un autre jeu d’enregistrements DNS au sein de la même zone.** Les enregistrements d’alias peuvent faire référence à d’autres jeux d’enregistrements du même type. Par exemple, un jeu d’enregistrements CNAME DNS peut être un alias pour un autre jeu d’enregistrements CNAME du même type. Cette disposition est utile si vous voulez que certains jeux d’enregistrements d’alias soient des alias et d’autres des non-alias.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Puis-je créer et mettre à jour des enregistrements d’alias à partir du portail Azure ?

Oui. Vous pouvez créer ou gérer des enregistrements d’alias dans le portail Azure, ainsi qu’avec les API REST Azure, PowerShell, l’interface CLI et les SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Les enregistrements d’alias garantissent-ils la suppression de mon jeu d’enregistrements DNS quand l’adresse IP publique sous-jacente est supprimée ?

Oui. Cette fonctionnalité est l’une des principales caractéristiques des enregistrements d’alias. Elle vous permet d’éviter d’éventuelles interruptions qui impactent les utilisateurs de votre application.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Les enregistrements d’alias garantissent-ils la mise à jour de mon jeu d’enregistrements DNS avec l’adresse IP appropriée en cas de changement de l’adresse IP publique sous-jacente ?

Oui. Cette fonctionnalité est l’une des principales caractéristiques des enregistrements d’alias. Elle vous permet d’éviter d’éventuelles interruptions ou des risques de sécurité qui impactent votre application.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Y a-t-il des restrictions quant à l’utilisation de jeux d’enregistrements d’alias pour pointer vers Traffic Manager à partir d’enregistrements A ou AAAA ?

Oui. Pour pointer vers un profil Traffic Manager en tant qu’alias à partir d’un jeu d’enregistrements A ou AAAA, le profil Traffic Manager doit utiliser uniquement des points de terminaison externes. Quand vous créez les points de terminaison externes dans Traffic Manager, fournissez les adresses P réelles des points de terminaison.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>L’utilisation d’enregistrements d’alias entraîne-t-elle des frais supplémentaires ?

Les enregistrements d’alias sont une qualification sur un jeu d’enregistrements DNS valide. Ils n’occasionnent pas de frais supplémentaires.

## <a name="use-azure-dns"></a>Utiliser Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>J’ai peuvent cohabiter sur un domaine à l’aide d’Azure DNS et un autre fournisseur DNS ?

Oui. Azure DNS prend en charge le co-hébergement de domaines avec d’autres services DNS.

Pour configurer le co-hébergement, modifier les enregistrements NS pour le domaine pointer vers les serveurs de noms des deux fournisseurs. Les enregistrements de serveur de noms (NS) déterminent quels fournisseurs reçoivent des requêtes DNS pour le domaine. Vous pouvez modifier ces enregistrements NS dans Azure DNS, dans l’autre fournisseur et dans la zone parente. Cette dernière est généralement configurée par le biais du bureau d’enregistrement de noms de domaine. Pour plus d’informations sur la délégation DNS, consultez [Délégation de domaine DNS](dns-domain-delegation.md).

Vérifiez également que les enregistrements DNS du domaine sont synchronisés entre les deux fournisseurs DNS. Azure DNS ne prend actuellement pas en charge les transferts de zone DNS. Les enregistrements DNS doivent être synchronisés à l’aide du [portail de gestion Azure DNS](dns-operations-recordsets-portal.md), de [l’API REST](https://docs.microsoft.com/powershell/module/az.dns), du [SDK](dns-sdk.md), des [applets de commande PowerShell](dns-operations-recordsets.md) ou de [l’outil CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Dois-je déléguer mon domaine sur les quatre serveurs de noms Azure DNS ?

Oui. Azure DNS affecte quatre serveurs de noms à chaque zone DNS. Cette disposition a pour but l’isolation des défaillances et une meilleure résilience. Pour pouvoir obtenir le contrat de niveau de service (SLA) Azure DNS, déléguez votre domaine aux quatre serveurs de noms.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quelles sont les limites d’utilisation d’Azure DNS ?

Les limites par défaut suivantes s’appliquent quand vous utilisez Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Puis-je déplacer une zone Azure DNS entre des groupes de ressources ou entre des abonnements ?

Oui. Les zones DNS peuvent être déplacées entre des groupes de ressources ou entre des abonnements.

Le déplacement d’une zone DNS n’a aucun effet sur une requête DNS. Les serveurs de noms attribués à la zone restent les mêmes. Les requêtes DNS sont traitées, dans l’ensemble, comme d’habitude.

Pour plus d’informations et pour obtenir des instructions sur le déplacement des zones DNS, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou abonnement](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Combien de temps faut-il pour que les modifications DNS s’appliquent ?

En général, les nouvelles zones DNS et les nouveaux enregistrements DNS apparaissent rapidement dans les serveurs de noms Azure DNS. Cela demande quelques secondes.

Les changements apportés aux enregistrements DNS existants peuvent prendre un peu plus de temps. Ils apparaissent généralement dans les serveurs de noms Azure DNS sous 60 secondes. La mise en cache DNS par des clients DNS et des programmes de résolution DNS récursifs en dehors d’Azure DNS peut également avoir un impact sur ces délais. Pour contrôler cette durée de cache, utilisez la propriété de durée de vie (TTL) de chaque jeu d’enregistrements.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Comment puis-je protéger mes zones DNS contre une suppression accidentelle ?

Azure DNS est géré à l’aide d’Azure Resource Manager. Ce service tire profit des fonctionnalités de contrôle d’accès proposées par Azure Resource Manager. Le contrôle d’accès en fonction du rôle contrôle les utilisateurs qui disposent d’un accès en lecture ou en écriture aux zones et jeux d’enregistrements DNS. Les verrouillages de ressources empêchent toute modification ou suppression accidentelle de zones et jeux d’enregistrements DNS.

Pour plus d’informations, consultez [Protéger des enregistrements et zones DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Comment configurer des enregistrements SPF dans Azure DNS ?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Les serveurs de noms Azure DNS effectuent-ils des résolutions sur IPv6 ? 

Oui. Les serveurs de noms DNS Azure sont à double pile. Cela signifie qu’ils disposent d’adresses IPv4 et IPv6. Pour rechercher l’adresse IPv6 des serveurs de noms Azure DNS affectée à votre zone DNS, utilisez un outil tel que nslookup. Par exemple `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Comment configurer un IDN dans Azure DNS ?

Les noms de domaines internationaux (IDN) encodent chaque nom DNS à l’aide de [punycode](https://en.wikipedia.org/wiki/Punycode). Les requêtes DNS sont effectuées à l’aide de ces noms codés en punycode.

Pour configurer des noms de domaines internationaux (IDN) dans Azure DNS, convertissez le nom de zone ou de jeu d’enregistrements en punycode. Azure DNS ne prend actuellement pas en charge la conversion intégrée vers le format punycode ou à partir de celui-ci.

## <a name="private-dns"></a>DNS privé

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS prend-il en charge les domaines privés ?

La prise en charge de domaines privés est implémentée à l’aide de la fonctionnalité Private Zones. Cette fonctionnalité est actuellement disponible en préversion publique. Les zones privées sont gérées à l’aide des mêmes outils que les zones Azure DNS accessibles sur Internet. Elles peuvent être résolues uniquement à partir de vos réseaux virtuels spécifiés. Pour plus d’informations, consultez la [Vue d’ensemble](private-dns-overview.md).

Actuellement, les zones privées ne sont pas prises en charge sur le portail Azure.

Pour plus d’informations sur les autres options DNS internes dans Azure, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Quelle est la différence entre le réseau virtuel d’inscription et le réseau virtuel de résolution dans le contexte des zones privées ?

Vous pouvez lier des réseaux virtuels à une zone privée DNS en tant que réseau virtuel d’inscription ou en tant que réseau virtuel de résolution. Dans les deux cas, les machines virtuelles sur le réseau virtuel effectuent une résolution correcte par rapport aux enregistrements de la zone privée. Avec un réseau virtuel d’inscription, les enregistrements DNS sont automatiquement inscrits dans la zone pour les machines virtuelles du réseau virtuel. Quand une machine virtuelle sur un réseau virtuel d’inscription est supprimée, l’enregistrement DNS correspondant de la zone privée liée est automatiquement supprimé. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones fonctionne-il d’une région Azure à une autre ?

Oui. La fonctionnalité Private Zones est prise en charge pour la résolution DNS entre les réseaux virtuels de différentes régions Azure. Elle fonctionne même sans l’appairage explicite des réseaux virtuels. Tous les réseaux virtuels doivent être spécifiés en tant que réseaux virtuels de résolution pour la zone privée. Les clients peuvent avoir besoin que les réseaux virtuels soient appairés pour que le trafic TCP/HTTP circule d’une région à une autre.

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>La connectivité à Internet à partir de réseaux virtuels est-elle nécessaire pour les zones privées ?

Non. Les zones privées fonctionnent avec les réseaux virtuels. Les clients les utilisent pour gérer les domaines pour les machines virtuelles ou d’autres ressources dans et entre les réseaux virtuels. Une connectivité à Internet n’est pas nécessaire pour la résolution de noms. 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>La même zone privée peut-elle être utilisée pour plusieurs réseaux virtuels pour la résolution ?

Oui. Les clients peuvent associer jusqu’à 10 réseaux virtuels de résolution à une même zone privée.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Un réseau virtuel qui appartient à un autre abonnement peut-il être ajouté en tant que réseau virtuel de résolution à une zone privée ?

Oui. Vous devez disposer d'une autorisation d'écriture sur les réseaux virtuels ainsi que sur la zone DNS privée. L’autorisation d’accès en écriture peut être accordée à plusieurs rôles RBAC. Par exemple, le rôle RBAC Contributeur de réseaux classiques dispose d’autorisations d’écriture sur les réseaux virtuels. Pour plus d’informations sur les rôles RBAC, consultez [Contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Les enregistrements DNS de machines virtuelles inscrits automatiquement dans une zone privée sont-ils supprimés automatiquement quand les machines virtuelles sont supprimées par le client ?

Oui. Si vous supprimez une machine virtuelle sur un réseau virtuel d’inscription, les enregistrements DNS qui ont été inscrits dans la zone sont automatiquement supprimés. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Un enregistrement de machine virtuelle inscrit automatiquement dans une zone privée à partir d’un réseau virtuel d’inscription peut-il être supprimé manuellement ?

Non. Les enregistrements DNS de machine virtuelle qui sont inscrits automatiquement dans une zone privée à partir d’un réseau virtuel d’inscription ne sont pas visibles ou modifiables par les clients. Vous pouvez remplacer les enregistrements DNS inscrits automatiquement par un enregistrement DNS créé manuellement dans la zone. La question et la réponse suivantes traitent de ce sujet.

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Que se passe-t-il quand nous essayons de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle existante inscrite automatiquement sur un réseau virtuel d’inscription ?

Vous essayez de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle inscrite automatiquement existante sur un réseau virtuel d’inscription. Dans ce cas, le nouvel enregistrement DNS remplace l’enregistrement de machine virtuelle inscrit automatiquement. Si vous essayez de supprimer à nouveau cet enregistrement DNS créé manuellement de la zone, la suppression réussit. L’inscription automatique se produit à nouveau tant que la machine virtuelle existe encore et qu’une adresse IP privée lui est attachée. L’enregistrement DNS est automatiquement recréé dans la zone.

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Que se passe-t-il quand nous dissocions un réseau virtuel d’inscription d’une zone privée ? Les enregistrements de machines virtuelles du réseau virtuel inscrits automatiquement sont-ils également supprimés de la zone ?

Oui. Pour dissocier un réseau virtuel d’inscription d’une zone privée, vous mettez à jour la zone DNS pour supprimer le réseau virtuel d’inscription associé. Dans ce processus, les enregistrements de machines virtuelles qui ont été inscrits automatiquement sont supprimés de la zone. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Que se passe-t-il quand nous supprimons un réseau virtuel d’inscription ou de résolution qui est lié à une zone privée ? Devons-nous mettre manuellement à jour la zone privée afin de dissocier le réseau virtuel en tant que réseau virtuel d’inscription ou de résolution de la zone ?

Oui. Quand vous supprimez un réseau virtuel d’inscription ou de résolution sans le dissocier au préalable d’une zone privée, votre opération de suppression réussit. Toutefois, le réseau virtuel n’est pas automatiquement dissocié de votre zone privée, le cas échéant. Vous devez dissocier manuellement le réseau virtuel de la zone privée. C’est pourquoi vous devez dissocier votre réseau virtuel de votre zone privée avant de le supprimer.

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Résolution DNS en utilisant le nom de domaine complet (internal.cloudapp.net) par défaut continue de fonctionner même si une zone privée (par exemple, private.contoso.com) est liée à un réseau virtuel ?

Oui. La fonctionnalité Private Zones ne remplace pas les résolutions DNS par défaut à l’aide de la zone internal.cloudapp.net fournie par Azure. Elle est proposée en tant qu’amélioration ou fonctionnalité supplémentaire. Que vous vous appuyiez sur la zone internal.cloudapp.net fournie par Azure ou sur votre propre zone privée, utilisez le nom de domaine complet de la zone par rapport à laquelle vous souhaitez effectuer la résolution. 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Le suffixe DNS sur les machines virtuelles au sein d’un réseau virtuel lié est-il remplacé par celui de la zone privée ?

Non. Le suffixe DNS sur les machines virtuelles de votre réseau virtuel lié reste le suffixe par défaut fourni par Azure (« *.internal.cloudapp.net »). Vous pouvez changer manuellement ce suffixe DNS sur vos machines virtuelles en le remplaçant par celui de la zone privée. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Existe-t-il des limitations pour les zones privées pendant cette préversion ?

Oui. La préversion publique a les limitations suivantes.
* Un réseau virtuel d’inscription par zone privée.
* Jusqu’à 10 réseaux virtuels de résolution par zone privée.
* Un réseau virtuel donné n’est lié qu’à une seule zone privée en tant que réseau virtuel d’inscription.
* Un réseau virtuel donné est lié jusqu’à 10 zones privées en tant que réseau virtuel de résolution.
* Si un réseau virtuel d’inscription est spécifié, les enregistrements DNS pour les machines virtuelles de ce réseau virtuel qui sont inscrits dans la zone privée ne sont pas visibles ou récupérables à partir de PowerShell, de l’interface CLI ou des API. Les enregistrements de machine virtuelle sont enregistrés et résolus avec succès.
* Le DNS inversé fonctionne uniquement pour l’espace IP privé sur le réseau virtuel d’inscription.
* Le DNS inversé pour une adresse IP privée qui n’est pas inscrite dans la zone privée retourne « internal.cloudapp.net » comme suffixe DNS. Ce suffixe ne peut pas être résolu. Il peut s’agir, par exemple, d’une adresse IP privée pour une machine virtuelle dans un réseau virtuel qui est lié en tant qu’un réseau virtuel de résolution à une zone privée.
* Un réseau virtuel doit être vide lorsqu'il est lié pour la première fois à une zone privée en tant que réseau virtuel d'inscription ou de résolution. Le réseau virtuel peut ensuite être non vide pour les liaisons ultérieures à d’autres zones privées en tant que réseau virtuel d’inscription ou de résolution.
* Le transfert conditionnel n’est pas pris en charge, par exemple pour activer la résolution entre les réseaux Azure et les réseaux locaux. Découvrez comment les clients peuvent implémenter ce scénario par le biais d’autres mécanismes. Consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Existe-t-il des quotas ou des limites applicables aux zones ou aux enregistrements pour les zones privées ?

Il n’existe aucune limite quant au nombre de zones autorisées par abonnement pour les zones privées. Il n’existe aucune limite quant au nombre de jeux d’enregistrements par zone pour les zones privées. Les zones publiques et privées sont prises en compte pour les limites DNS globales. Pour plus d’informations, consultez les [Limites de service et d’abonnement Azure](../azure-subscription-service-limits.md#azure-dns-limits).

### <a name="is-there-portal-support-for-private-zones"></a>Existe-t-il une prise en charge du portail pour les zones privées ?

Les zones privées qui sont déjà créées par le biais des API, de PowerShell, de l’interface CLI et des SDK sont visibles sur le portail Azure. Mais les clients ne peuvent pas créer des zones privées ou gérer des associations avec des réseaux virtuels. Pour les réseaux virtuels associés en tant que réseaux virtuels d’inscription, les enregistrements de machines virtuelles inscrits automatiquement ne sont pas visibles à partir du portail. 

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur Azure DNS](dns-overview.md).

- [En savoir plus sur la façon d’utiliser Azure DNS pour les domaines privés](private-dns-overview.md).

- [En savoir plus sur les zones et les enregistrements DNS](dns-zones-records.md).

- [Bien démarrer avec Azure DNS](dns-getstarted-portal.md).
