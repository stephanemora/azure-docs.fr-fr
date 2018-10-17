---
title: 'Démarrage rapide : Créer un enregistrement et une zone Azure DNS à l’aide d’Azure CLI'
description: 'Démarrage rapide : découvrez comment créer une zone et un enregistrement DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS à l’aide d’Azure CLI.'
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: f80488f555cfa3b7be6f35b9f23ea0a501a27fd9
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831595"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Démarrage rapide : Créer un enregistrement et une zone Azure DNS à l’aide d’Azure CLI

Cet article vous guide tout au long de la procédure de création de votre première zone et de votre premier enregistrement DNS à l’aide de l’interface de ligne de commande Azure, qui est disponible pour Windows, Mac et Linux. Vous pouvez également effectuer ces étapes à l’aide du [portail Azure](dns-getstarted-portal.md) ou [d’Azure PowerShell](dns-getstarted-powershell.md).

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Enfin, pour publier votre zone DNS sur Internet, vous devez configurer les serveurs de noms du domaine. Chacune de ces étapes est décrite ci-dessous.

Désormais, Azure DNS prend également en charge les zones DNS privées (actuellement en préversion publique). Pour en savoir plus sur les zones DNS privées, consultez la session relative à [l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md). Vous pouvez trouver un exemple de création d’une zone DNS privée sur la page [Créer une zone privée Azure DNS avec Azure CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la zone DNS, créez un groupe de ressources pour contenir la zone DNS :

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `az network dns zone create`. Pour consulter l’aide de cette commande, tapez `az network dns zone create -h`.

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Pour créer un enregistrement DNS, utilisez la commande `az network dns record-set [record type] add-record`. Pour obtenir de l’aide sur les enregistrements A, consultez `azure network dns record-set A add-record -h`.

L’exemple suivant crée un enregistrement avec le nom relatif « www » dans la zone DNS « contoso.com », dans le groupe de ressources « MyResourceGroup ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A », avec l’adresse IP « 1.2.3.4 », et une durée de vie par défaut de 3 600 secondes (1 heure).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

## <a name="view-records"></a>Affichage des enregistrements

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Mettre à jour les serveurs de noms

Une fois que vous jugez que votre zone et vos enregistrements DNS ont été configurés correctement, vous devez configurer votre nom de domaine pour utiliser les serveurs de noms de domaine Azure DNS, ce qui permet aux autres utilisateurs sur Internet de rechercher vos enregistrements DNS.

Les serveurs de noms de votre zone sont indiqués par la commande `az network dns zone show`. Pour afficher les noms de serveurs de noms, utilisez la sortie JSON, comme indiqué dans l’exemple suivant.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Ces serveurs de noms doivent être configurés avec le bureau d’enregistrement de noms de domaine (dans lequel vous avez acheté le nom de domaine). Votre bureau d’enregistrement offre la possibilité de configurer les serveurs de noms pour le domaine. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Supprimer toutes les ressources
 
Lorsque vous n’en avez plus besoin, vous pouvez supprimer toutes les ressources créées dans ce démarrage rapide en supprimant le groupe de ressources :

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé vos premiers enregistrement et zone DNS à l’aide d’Azure CLI, vous pouvez créer des enregistrements pour une application web dans un domaine personnalisé.

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)
