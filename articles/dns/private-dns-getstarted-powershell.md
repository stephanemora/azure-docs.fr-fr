---
title: 'Didacticiel : Créez une zone privée Azure DNS à l’aide d’Azure Powershell'
description: Dans ce tutoriel, vous pourrez créer et tester une zone DNS privée et faire un enregistrement dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone privée DNS et de votre premier enregistrement à l’aide d’Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b4d75c7a6db89b19d88cddcc564fd4e6a9ad0f49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "65916874"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Didacticiel : Créer une zone privée Azure DNS à l’aide d’Azure PowerShell

Ce didacticiel vous indique la procédure à suivre pour créer votre première zone privée DNS et pour enregistrer à l’aide d’Azure Powershell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Pour publier une zone DNS privée sur votre réseau virtuel, vous spécifiez la liste des réseaux virtuels qui sont autorisés à résoudre les enregistrements dans la zone.  On les appelle *résolution de réseaux virtuels*. Vous pouvez également spécifier un réseau virtuel pour lequel Azure DNS enregistre le nom d’hôte à chaque fois qu’une machine virtuelle est créée, change de protocole internet, ou est supprimée.  Nous appelons cela un *réseau virtuel d’enregistrement*.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une zone DN privée DNS
> * Créer des machines virtuelles de test
> * Créer un enregistrement DNS supplémentaire
> * Tester la zone privée

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous préférez, vous pouvez suivre ce tutoriel en utilisant [Azure CLI](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Tout d’abord, créez un groupe de ressources pour contenir la zone DNS : 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Créer une zone DN privée DNS

Une zone DNS est créée à l’aide de `New-AzDnsZone`l’applet de commande avec une valeur de *Private* pour le paramètre **ZoneType**. L’exemple suivant crée une zone DNS appelée **private.contoso.com** dans le groupe de ressources appelé **MyAzureResourceGroup** et s’assure que la zone DNS soit disponible pour le réseau virtuel appelé **MyAzureVnet**.

Si le paramètre **ZoneType** est oublié, la zone sera créée comme une zone publique, c’est pour cela qu’il faut créer une zone privée. 

```azurepowershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Si vous souhaitez créer une zone seulement pour la résolution de nom (pas de création automatique de nom d’hôte), vous pouvez utiliser le paramètre de *ResolutionVirtualNetworkId* au lieu du paramètre *RegistrationVirtualNetworkId*.

> [!NOTE]
> Vous ne pourrez pas voir les enregistrements de nom d’hôte créés automatiquement. Mais plus tard, vous allez les tester pour vous assurer qu’ils existent.

### <a name="list-dns-private-zones"></a>Répertorier les zones privées DNS

En omettant le nom de la zone dans `Get-AzDnsZone`, vous pouvez énumérer toutes les zones d’un groupe de ressources. Cette opération renvoie un tableau d’objets de la zone.

```azurepowershell
Get-AzDnsZone -ResourceGroupName MyAzureResourceGroup
```

En omettant le nom de zone et le nom du groupe de ressources de `Get-AzDnsZone`, vous pouvez énumérer toutes les zones dans l’abonnement Azure.

```azurepowershell
Get-AzDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Créer les machines virtuelles de test

Maintenant, créez deux machines virtuelles afin de pouvoir tester votre zone DNS privée :

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

L’exécution de cette opération nécessite quelques minutes.

## <a name="create-an-additional-dns-record"></a>Créer un enregistrement DNS supplémentaire

Vous pouvez utiliser l’applet de commande `New-AzDnsRecordSet` pour créer des jeux d’enregistrements. L’exemple suivant crée un enregistrement avec le nom relatif **db** dans la zone DNS **private.contoso.com**, dans le groupe de ressources **MyAzureResourceGroup**. Le nom complet du jeu d’enregistrements est **db.private.contoso.com**. Le type d’enregistrement est « A », avec l’adresse IP « 10.2.0.4 », et la durée de vie est de 3600secondes.

```azurepowershell
New-AzDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Afficher les enregistrements DNS

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```azurepowershell
Get-AzDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```
N’oubliez pas, vous ne verrez pas les enregistrements A créés automatiquement pour vos deux machines virtuelles de test.

## <a name="test-the-private-zone"></a>Tester la zone privée

Maintenant vous pouvez désormais tester la résolution de noms pour votre zone privée **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurer des machines virtuelles pour autoriser l’ICMP entrant

Vous pouvez utiliser la commande test ping pour tester la résolution de nom. Par conséquent, configurez le pare-feu sur les deux machines virtuelles pour autoriser les paquets ICMP entrants.

1. Connectez-vous à myVM01 et ouvrez une fenêtre Windows PowerShell avec les privilèges d’administrateur.
2. Exécutez la commande suivante :

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Répétez l’opération pour myVM02.

### <a name="ping-the-vms-by-name"></a>Faites un test ping sur les machines virtuelles par leur nom

1. À partir de l’invite de commandes Windows PowerShell myVM02, faites un test ping de myVM01 en utilisant le nom d’hôte enregistré automatiquement :
   ```
   ping myVM01.private.contoso.com
   ```
   La sortie doit ressembler à cela :
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Maintenant faites un test ping sur le nom **db** que vous avez créé précédemment :
   ```
   ping db.private.contoso.com
   ```
   La sortie doit ressembler à cela :
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources **MyAzureResourceGroup** pour supprimer les ressources créées dans ce didacticiel.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous déployez une zone DNS privée, créez un enregistrement DNS et testez la zone.
Ensuite, vous pouvez en apprendre davantage sur les zones DNS privées.

> [!div class="nextstepaction"]
> [Utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md)




