---
title: Héberger des zones de recherche DNS inversées dans Azure DNS
description: Découvrez comment utiliser Azure DNS pour héberger les zones de recherche inversées DNS pour vos plages d’adresses IP
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2021
ms.author: rohink
ms.openlocfilehash: 6db63f564d481dd57d1bdbf090678616989f83e1
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108317006"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Héberger des zones de recherche DNS inversées dans Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cet article explique comment héberger des zones de recherche DNS inversée pour les plages d’adresses IP attribuées avec Azure DNS. En règle générale, votre fournisseur de services Internet attribue à votre organisation les plages d’adresses IP représentées par les zones de recherche inversée.

Pour configurer le DNS inversé pour une adresse IP Azure attribuée à votre service Azure, consultez [Configuration du DNS inversé pour les services hébergés dans Azure](dns-reverse-dns-for-azure-services.md).

Avant de lire cet article, familiarisez-vous avec la [vue d’ensemble du DNS inversé](dns-reverse-dns-overview.md). Il est pris en charge dans Azure.

Dans cet article, vous allez découvrir comment créer votre première zone DNS de recherche inversée et votre premier enregistrement avec le Portail Azure, Azure PowerShell, l’interface CLI Azure Classic et Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Créer une zone de recherche inversée DNS

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** et recherchez **Zone DNS**. Sélectionnez ensuite **Créer**.

      :::image type="content" source="./media/dns-operations-dnszones-portal/search-dns-zone.png" alt-text="Capture d’écran de la création d’une recherche de ressource pour une zone DNS inversée.":::

1. Sur la page **Créer une zone DNS**, sélectionnez ou entrez les paramètres suivants :

    | Paramètre | Détails |
    | --- | --- |
    | **Abonnement** | Sélectionnez un abonnement pour y créer la zone DNS.|
    | **Groupe de ressources** | Sélectionnez ou créez un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
    | **Nom** | Donnez un nom à la zone DNS. Le nom de la zone a été conçu différemment pour les préfixes IPv4 et IPv6. Utilisez les instructions relatives à [IPv4](#ipv4) ou à [IPv6](#ipv6) pour nommer votre zone.  |
    | **Lieu** | Sélectionnez l’emplacement du groupe de ressources. L’emplacement est déjà sélectionné si vous utilisez un groupe de ressources déjà créé. |

1. Sélectionnez **Vérifier + créer**, puis **Créer** une fois la validation réussie.

### <a name="ipv4"></a>IPv4

Le nom d’une zone de recherche inversée IPv4 est basé sur la plage d’adresses IP qu’il représente. Il doit se présenter sous la forme suivante : `<IPv4 network prefix in reverse order>.in-addr.arpa`. Pour voir des exemples, consultez [Vue d’ensemble du DNS inversé](dns-reverse-dns-overview.md#ipv4) pour IPv4.

> [!NOTE]
> Lorsque vous créez des zones de recherche DNS inversée sans classe dans Azure DNS, vous devez utiliser un trait d’union (`-`) plutôt qu’une barre oblique (`/`) dans le nom de la zone.
>
> Par exemple, pour la plage d’adresses IP 192.0.2.128/26, vous devez utiliser `128-26.2.0.192.in-addr.arpa` comme nom de zone à la place de `128/26.2.0.192.in-addr.arpa`.
>
> Bien que les normes DNS prennent en charge les deux méthodes, Azure DNS ne gère pas les noms de zone DNS qui contiennent une barre oblique (`/`).

L’exemple suivant montre comment créer une zone DNS inversée de « Classe C » nommée `2.0.192.in-addr.arpa` dans Azure DNS via le portail Azure :

:::image type="content" source="./media/dns-reverse-dns-hosting/ipv4-arpa-zone.png" alt-text="Capture d’écran de la création d’une zone DNS IPv4 arpa.":::

Les exemples suivants montrent comment effectuer cette tâche avec Azure PowerShell et Azure CLI.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName mydnsresourcegroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns zone create mydnsresourcegroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network dns zone create -g mydnsresourcegroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Le nom d’une zone de recherche inversée IPv6 doit se présenter sous la forme suivante : `<IPv6 network prefix in reverse order>.ip6.arpa`.  Pour voir des exemples, consultez [Vue d’ensemble du DNS inversé](dns-reverse-dns-overview.md#ipv6) pour IPv6.


L’exemple suivant montre comment créer une zone de recherche inversée DNS IPv6 nommée `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` dans Azure DNS via le portail Azure :

:::image type="content" source="./media/dns-reverse-dns-hosting/ipv6-arpa-zone.png" alt-text="Capture d’écran de la création d’une zone DNS IPv6 arpa.":::

Les exemples suivants montrent comment effectuer cette tâche avec Azure PowerShell et Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName mydnsresourcegroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns zone create mydnsresourcegroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g mydnsresourcegroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Déléguer une zone de recherche inversée DNS

Une fois la zone de recherche DNS inversée créée, vous devez veiller à ce qu’elle soit déléguée à partir de la zone parente. La délégation DNS permet au processus de résolution de nom DNS de trouver les serveurs de noms hébergeant votre zone de recherche inversée DNS. Ainsi les serveurs de noms peuvent répondre aux requêtes DNS inverses pour les adresses IP de votre plage d’adresses.

Pour les zones de recherche directe, le processus de délégation de zone DNS est décrit dans [Déléguer votre domaine à Azure DNS](dns-delegate-domain-azure-dns.md). La délégation pour les zones de recherche inversée fonctionne de façon similaire. La seule différence est que vous devez configurer les serveurs de noms avec le fournisseur de services Internet. C’est ce dernier qui gère votre plage d’adresses IP. C’est pourquoi il doit mettre à jour les serveurs de noms à la place du bureau d’enregistrement de noms de domaine.

## <a name="create-a-dns-ptr-record"></a>Créer un enregistrement PTR DNS

### <a name="ipv4"></a>IPv4

L’exemple suivant explique le processus de création d’un enregistrement PTR pour une zone DNS inversée dans Azure DNS. Pour plus d’informations sur les types d’enregistrements et pour savoir comment modifier des enregistrements existants, consultez [Gestion des enregistrements DNS et des jeux d’enregistrements](dns-operations-recordsets-portal.md).

1. En haut de la page de présentation *Zone DNS*, sélectionnez **+ Jeu d’enregistrements** pour ouvrir le volet *Ajouter un jeu d’enregistrements*.

    :::image type="content" source="./media/dns-reverse-dns-hosting/create-record-set-ipv4.png" alt-text="Capture d’écran de la création d’un jeu d’enregistrements de pointeur IPv4.":::

1. Le nom du jeu d’enregistrements d’un enregistrement PTR correspond à la fin de l’adresse IPv4 dans l’ordre inverse. 

    Dans cet exemple, les trois premiers octets figurent déjà dans le nom de la zone : `.2.0.192`. C’est pourquoi seul le dernier octet est nécessaire dans la zone **Nom**. Vous pouvez par exemple nommer votre jeu d’enregistrements **15** pour une ressource dont l’adresse IP est `192.0.2.15`.  

    :::image type="content" source="./media/dns-reverse-dns-hosting/create-ipv4-ptr.png" alt-text="Capture d’écran de la création d’un enregistrement de pointeur IPv4.":::

1. Pour *Type*, sélectionnez **PTR**.

1. Dans le champ *NOM DE DOMAINE*, entrez le nom de domaine complet (FQDN) de la ressource utilisant l’adresse IP.

1. Sélectionnez **OK** pour créer l’enregistrement DNS.

Les exemples suivants montrent comment effectuer cette tâche à l’aide d’Azure PowerShell et Azure CLI.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName mydnsresourcegroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set add-record mydnsresourcegroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network dns record-set ptr add-record -g mydnsresourcegroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

L’exemple suivant explique le processus de création d’un enregistrement PTR pour IPv6. Pour plus d’informations sur les types d’enregistrements et pour savoir comment modifier des enregistrements existants, consultez [Gestion des enregistrements DNS et des jeux d’enregistrements](dns-operations-recordsets-portal.md).

1. En haut du panneau *Zone DNS*, sélectionnez **+ Jeu d’enregistrements** pour ouvrir le panneau *Ajouter un jeu d’enregistrements*.

   :::image type="content" source="./media/dns-reverse-dns-hosting/create-record-set-ipv6.png" alt-text="Capture d’écran de la création d’un jeu d’enregistrements de pointeur IPv6.":::

1. Le nom du jeu d’enregistrements d’un enregistrement PTR correspond à la fin de l’adresse IPv6 dans l’ordre inverse. Il ne doit inclure aucune compression des zéros. 

    Dans cet exemple, les 64 premiers bits de l’adresse IPv6 figurent déjà dans le nom de la zone (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). C’est pourquoi seuls les 64 derniers bits sont nécessaires dans la zone **Nom**. Ils sont entrés dans l’ordre inverse, et un point est utilisé comme séparateur entre les nombres hexadécimaux. Vous pouvez nommer votre jeu d’enregistrements **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** pour une ressource dont l’adresse IP est 2001:0db8:abdc:0000:f524:10bc:1af9:405e.

    :::image type="content" source="./media/dns-reverse-dns-hosting/create-ipv6-ptr.png" alt-text="Capture d’écran de la création d’un enregistrement de pointeur IPv6.":::

1. Pour *Type*, sélectionnez **PTR**.  

1. Dans le champ *NOM DE DOMAINE*, entrez le nom de domaine complet (FQDN) de la ressource utilisant l’adresse IP.

1. Sélectionnez **OK** pour créer l’enregistrement DNS.

Les exemples suivants montrent comment effectuer cette tâche à l’aide de PowerShell et Azure CLI.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName mydnsresourcegroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set add-record mydnsresourcegroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network dns record-set ptr add-record -g mydnsresourcegroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Affichage des enregistrements

Pour afficher les enregistrements que vous avez créés, accédez à votre zone DNS dans le portail Azure. Vous pouvez afficher les enregistrements de la zone DNS dans la partie inférieure du panneau **Zone DNS**. Les enregistrements NS et SOA par défaut devraient s’afficher, avec les enregistrement que vous avez créés. Les enregistrements NS et SOA sont créés dans chaque zone. 

### <a name="ipv4"></a>IPv4

La page **Zone DNS** fait apparaître l’enregistrement PTR IPv4 :

:::image type="content" source="./media/dns-reverse-dns-hosting/view-ipv4-ptr-record.png" alt-text="Capture d’écran de l’enregistrement de pointeur IPv4 sur la page de présentation." lightbox="./media/dns-reverse-dns-hosting/view-ipv4-ptr-record-expanded.png":::

Les exemples suivants montrent comment afficher les enregistrements PTR avec Azure PowerShell et Azure CLI.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName mydnsresourcegroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set list mydnsresourcegroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network dns record-set list -g mydnsresourcegroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

La page **Zone DNS** fait apparaître l’enregistrement PTR IPv6 :

:::image type="content" source="./media/dns-reverse-dns-hosting/view-ipv6-ptr-record.png" alt-text="Capture d’écran de l’enregistrement de pointeur IPv6 sur la page de présentation." lightbox="./media/dns-reverse-dns-hosting/view-ipv6-ptr-record-expanded.png":::

Les exemples suivants montrent comment afficher les enregistrements à l’aide de PowerShell ou d’Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName mydnsresourcegroup
```

#### <a name="azure-classic-cli"></a>Azure Classic CLI

```azurecli
azure network dns record-set list mydnsresourcegroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g mydnsresourcegroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Puis-je héberger des zones de recherche inversée DNS pour les blocs IP attribués par mon fournisseur de services Internet sur Azure DNS ?

Oui. L’hébergement des zones de recherche inversée pour vos propres plages d’adresses IP dans Azure DNS est entièrement pris en charge.

Créez la zone de recherche inversée dans Azure DNS suivant les instructions de cet article. Ensuite, rapprochez-vous de votre fournisseur de services Internet pour [déléguer la zone](dns-domain-delegation.md). Vous pouvez ensuite gérer les enregistrements PTR pour chaque recherche inversée de la même façon que d’autres types d’enregistrement.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Combien coûte l’hébergement de ma zone de recherche DNS inversée ?

L’hébergement de la zone de recherche inversée DNS pour le bloc IP attribué par votre fournisseur de services Internet dans Azure DNS est facturé selon les [tarifs Azure DNS standard](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puis-je héberger des zones de recherche inversée DNS pour les adresses IPv4 et IPv6 dans Azure DNS ?

Oui. Cet article explique comment créer des zones de recherche DNS IPv4 et IPv6 dans Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Puis-je importer une zone de recherche DNS inversée existante ?

Oui. Vous pouvez utiliser Azure CLI pour importer des zones DNS existantes dans Azure DNS. Cette méthode fonctionne pour les zones de recherche directe et des zones de recherche inversée.

Pour plus d’informations, consultez [Importation et exportation d’un fichier de zone DNS](dns-import-export.md) avec Azure CLI.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le DNS inversé, consultez [Recherche DNS inversée sur Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).

* Découvrez comment [gérer des enregistrements DNS inversés pour vos services Azure](dns-reverse-dns-for-azure-services.md).
