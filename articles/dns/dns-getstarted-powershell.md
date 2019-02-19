---
title: 'Démarrage rapide : Créer un enregistrement et une zone Azure DNS à l’aide d’Azure PowerShell'
description: Découvrez comment créer une zone et un enregistrement DNS dans Azure DNS. Il s’agit d’un démarrage rapide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS à l’aide d’Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 839c97ccccbc1ce2cf646afcd27894a190eda1b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000885"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Démarrage rapide : Créer un enregistrement et une zone Azure DNS à l’aide d’Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dans ce démarrage rapide, vous allez créer vos premiers enregistrement et zone DNS à l’aide d’Azure PowerShell. Vous pouvez également effectuer ces étapes à l’aide du [portail Azure](dns-getstarted-portal.md) ou [d’Azure CLI](dns-getstarted-cli.md). 

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Enfin, pour publier votre zone DNS sur Internet, vous devez configurer les serveurs de noms du domaine. Chacune de ces étapes est décrite ci-dessous.

Azure DNS prend également en charge la création de domaines privés. Pour obtenir des instructions détaillées sur la création de votre première zone DNS privée et enregistrement, consultez [Bien démarrer avec les zones privées Azure DNS à l’aide de PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la zone DNS, créez un groupe de ressources pour contenir la zone DNS :

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzDnsZone` . L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```powershell
New-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Vous pouvez utiliser l’applet de commande `New-AzDnsRecordSet` pour créer des jeux d’enregistrements. L’exemple suivant crée un enregistrement avec le nom relatif « www » dans la zone DNS « contoso.com », dans le groupe de ressources « MyResourceGroup ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A », avec l’adresse IP « 1.2.3.4 », et la durée de vie est de 3 600 secondes.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>Affichage des enregistrements

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```powershell
Get-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>Mettre à jour les serveurs de noms

Une fois que vous jugez que votre zone et vos enregistrements DNS ont été configurés correctement, vous devez configurer votre nom de domaine pour utiliser les serveurs de noms Azure DNS. Cela permet à d’autres utilisateurs sur Internet de rechercher vos enregistrements DNS.

Les serveurs de noms de votre zone sont indiqués par la cmdlet `Get-AzDnsZone` :

```powershell
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Ces serveurs de noms doivent être configurés avec le bureau d’enregistrement de noms de domaine (dans lequel vous avez acheté le nom de domaine). Votre bureau d’enregistrement offre la possibilité de configurer les serveurs de noms pour le domaine. Pour plus d’informations, consultez [Tutoriel : Héberger votre domaine dans Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer toutes les ressources créées dans ce démarrage rapide en supprimant le groupe de ressources :

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé vos premiers enregistrement et zone DNS à l’aide d’Azure PowerShell, vous pouvez créer des enregistrements pour une application web dans un domaine personnalisé.

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)

