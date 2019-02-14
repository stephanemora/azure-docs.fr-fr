---
title: Créer une zone privée Azure DNS avec Azure CLI
description: Dans ce tutoriel, vous pourrez créer et tester une zone DNS privée et faire un enregistrement dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone privée DNS et de votre premier enregistrement à l’aide d’Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/25/2018
ms.author: victorh
ms.openlocfilehash: 5559e2fc9b9cce95bd7d5d02a64d134e5eaa03be
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100616"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Créer une zone privée Azure DNS avec Azure CLI

Ce tutoriel vous indique la procédure à suivre pour créer votre première zone et votre premier enregistrement DNS privé avec Azure CLI.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Pour publier une zone DNS privée sur votre réseau virtuel, vous spécifiez la liste des réseaux virtuels qui sont autorisés à résoudre les enregistrements dans la zone.  On les appelle *résolution de réseaux virtuels*. Vous pouvez également spécifier un réseau virtuel pour lequel Azure DNS enregistre le nom d’hôte à chaque fois qu’une machine virtuelle est créée, change de protocole internet, ou est supprimée.  Nous appelons cela un *réseau virtuel d’enregistrement*.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une zone DN privée DNS
> * Créer des machines virtuelles de test
> * Créer un enregistrement DNS supplémentaire
> * Tester la zone privée

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous préférez, vous pouvez effectuer ce didacticiel en utilisant [Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Tout d’abord, créez un groupe de ressources pour contenir la zone DNS : 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Créer une zone DN privée DNS

Une zone DNS est créée à l’aide d’une `az network dns zone create`commande avec une valeur de *Private* pour le paramètre **ZoneType**. L’exemple suivant crée une zone DNS appelée **contoso.local** dans le groupe de ressources appelé **MyAzureResourceGroup** et s’assure que la zone DNS soit disponible pour le réseau virtuel appelé **MyAzureVnet**.

Si le paramètre **ZoneType** est oublié, la zone sera créée comme une zone publique, c’est pour cela qu’il faut créer une zone privée.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n contoso.local \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Si vous souhaitez créer une zone uniquement pour la résolution de nom (pas de création automatique de nom d’hôte), vous pouvez utiliser le paramètre de *résolution de réseaux virtuels* au lieu du paramètre *d’enregistrement de réseaux virtuels*.

> [!NOTE]
> Vous ne pourrez pas voir les enregistrements de nom d’hôte créés automatiquement. Mais plus tard, vous allez les tester pour vous assurer qu’ils existent.

### <a name="list-dns-private-zones"></a>Répertorier les zones privées DNS

Pour énumérer des zones DNS, utilisez `az network dns zone list`. Pour obtenir de l’aide, consultez l’article `az network dns zone list --help`.

Spécifier le groupe de ressources permet de ne lister que les zones du groupe de ressources :

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Omettre le groupe de ressources permet de lister toutes les zones de l’abonnement :

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Créer les machines virtuelles de test

Maintenant, créez deux machines virtuelles afin de pouvoir tester votre zone DNS privée :

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

L’exécution de cette opération nécessite quelques minutes.

## <a name="create-an-additional-dns-record"></a>Créer un enregistrement DNS supplémentaire

Pour créer un enregistrement DNS, utilisez la commande `az network dns record-set [record type] add-record`. Pour obtenir de l’aide, concernant l’ajout d’enregistrements A par exemple, consultez `azure network dns record-set A add-record --help`.

 L’exemple suivant crée un enregistrement avec le nom relatif **db** dans la zone DNS **contoso.local**, dans le groupe de ressources **MyAzureResourceGroup**. Le nom complet du jeu d’enregistrements est **db.contoso.local**. Le type d’enregistrement est « A », avec l’adresse IP « 10.2.0.4 ».

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z contoso.local \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Afficher les enregistrements DNS

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z contoso.local
```
N’oubliez pas, vous ne verrez pas les enregistrements A créés automatiquement pour vos deux machines virtuelles de test.

## <a name="test-the-private-zone"></a>Tester la zone privée

Maintenant vous pouvez désormais tester la résolution de noms pour votre zone privée **contoso.local**.

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
   ping myVM01.contoso.local
   ```
   La sortie doit ressembler à cela :
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
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
   ping db.contoso.local
   ```
   La sortie doit ressembler à cela :
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
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

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous déployez une zone DNS privée, créez un enregistrement DNS et testez la zone.
Ensuite, vous pouvez en apprendre davantage sur les zones DNS privées.

> [!div class="nextstepaction"]
> [Utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md)
