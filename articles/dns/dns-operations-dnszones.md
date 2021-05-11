---
title: Gérer des zones DNS dans Azure DNS - PowerShell | Microsoft Docs
description: Vous pouvez gérer des zones DNS à l’aide d’Azure PowerShell. Cet article décrit comment mettre à jour, supprimer et créer des zones DNS sur Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02c6518a1ccfaa678c42369ae22f67670aaf0566
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203256"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Gestion des zones DNS à l'aide de PowerShell

> [!div class="op_single_selector"]
> * [Portail](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure Classic CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Cet article vous montre comment gérer vos zones DNS avec Azure PowerShell. Vous pouvez également gérer vos zones DNS à l’aide de [l’interface de ligne de commande Azure](dns-operations-dnszones-cli.md) multiplateforme ou du portail Azure.

Ce guide traite spécifiquement des zones DNS publiques. Pour plus d’informations sur l’utilisation d’Azure PowerShell pour gérer les Zones privées dans Azure DNS, consultez [Prise en main d’Azure DNS Private Zones à l’aide d’Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzDnsZone` .

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyDNSResourceGroup* :

```azurepowershell-interactive
New-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
```

L’exemple suivant montre comment créer une zone DNS avec deux [balises Azure Resource Manager](dns-zones-records.md#tags), *projet = demo* et *env = test* :

```azurepowershell-interactive
New-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup -Tag @{ project="demo"; env="test" }
```

## <a name="get-a-dns-zone"></a>Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez l’applet de commande `Get-AzDnsZone` : Cette opération retourne un objet de zone DNS correspondant à une zone existante dans Azure DNS. Cet objet contient des données sur la zone (par exemple le nombre de jeux d’enregistrements), mais ne contient pas les jeux d’enregistrements eux-mêmes (voir `Get-AzDnsRecordSet`).

```azurepowershell-interactive
Get-AzDnsZone -Name contoso.com –ResourceGroupName MyDNSResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Création de la liste des zones DNS

En omettant le nom de la zone dans `Get-AzDnsZone`, vous pouvez énumérer toutes les zones d’un groupe de ressources. Cette opération renvoie un tableau d’objets de la zone.

```azurepowershell-interactive
$zoneList = Get-AzDnsZone -ResourceGroupName MyDNSResourceGroup
$zoneList
```

En omettant le nom de zone et le nom du groupe de ressources de `Get-AzDnsZone`, vous pouvez énumérer toutes les zones dans l’abonnement Azure.

```azurepowershell-interactive
$zoneList = Get-AzDnsZone
$zoneList
```

## <a name="update-a-dns-zone"></a>Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `Set-AzDnsZone`. Cette applet de commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Guide pratique pour gérer les enregistrements DNS](dns-operations-recordsets.md)). Elle est utilisée seulement pour mettre à jour les propriétés de la ressource de zone elle-même. Les propriétés de zone accessibles en écriture sont actuellement limitées aux [« balises » Azure Resource Manager de la ressource de zone](dns-zones-records.md#tags).

Utilisez l’une des options suivantes pour mettre à jour la zone DNS :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Spécifier la zone en utilisant le nom de zone et le groupe de ressources

Cette approche remplace les balises de zone existantes par les valeurs spécifiées.

```azurepowershell-interactive
Set-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone en utilisant un objet $zone

Cette approche récupère l’objet existant de la zone, modifie les balises, puis valide les modifications. De cette façon, les balises existantes peuvent être conservées.

```azurepowershell-interactive
# Get the zone object
$zone = Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzDnsZone -Zone $zone
```

Lors de l’utilisation de `Set-AzDnsZone` avec un objet $zone, les [vérifications d’ETag](dns-zones-records.md#etags) sont utilisées pour éviter que les modifications simultanées soient remplacées. Le commutateur facultatif `-Overwrite` permet de supprimer ces vérifications.

## <a name="delete-a-dns-zone"></a>Suppression d’une zone DNS

Les zones DNS peuvent être supprimées à l’aide de l’applet de commande `Remove-AzDnsZone`.

> [!NOTE]
> Supprimer une zone DNS supprime également tous les enregistrements DNS de la zone. Cette opération ne peut pas être annulée. Si la zone DNS est en cours d’utilisation, les services utilisant la zone échouent lors de la suppression de la zone.
>
>Pour vous protéger contre la suppression accidentelle de zones, consultez la page [Comment protéger des zones et enregistrements DNS](dns-protect-zones-recordsets.md).


Utilisez l’une des options suivantes pour supprimer une zone DNS :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Spécifier la zone en utilisant le nom de zone et le nom de groupe de ressources

```azurepowershell-interactive
Remove-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone en utilisant un objet $zone

Vous pouvez spécifier la zone à supprimer à l’aide d’un objet `$zone` retourné par `Get-AzDnsZone`.

```azurepowershell-interactive
$zone = Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
Remove-AzDnsZone -Zone $zone
```

L’objet de zone peut également être redirigé au lieu d’être transmis en tant que paramètre :

```azurepowershell-interactive
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup | Remove-AzDnsZone

```

Comme avec `Set-AzDnsZone`, la spécification de la zone avec un objet `$zone` active les vérifications d’ETag et permet de s’assurer que les modifications simultanées ne sont pas supprimées. Utilisez le commutateur `-Overwrite` pour supprimer ces vérifications.

## <a name="confirmation-prompts"></a>Invites de confirmation

Les applets de commande `New-AzDnsZone`, `Set-AzDnsZone` et `Remove-AzDnsZone` prennent en charge les invites de confirmation.

Les invites `New-AzDnsZone` et `Set-AzDnsZone` demandent une confirmation si la variable de préférence PowerShell `$ConfirmPreference` a la valeur `Medium` ou une valeur inférieure. Étant donné que la suppression d’une zone DNS peut entraîner des conditions indésirables, l’applet de commande `Remove-AzDnsZone` vous demande de confirmer si la variable PowerShell `$ConfirmPreference` a une autre valeur que `None`.

Étant donné que la valeur par défaut `$ConfirmPreference` est `High`, seule la commande `Remove-AzDnsZone` demande une confirmation par défaut.

Vous pouvez remplacer le paramétrage actuel de `$ConfirmPreference` par le paramètre `-Confirm`. Si vous spécifiez les paramètres `-Confirm` ou `-Confirm:$True`, les applets de commande vous invitent à confirmer l’exécution. Si vous spécifiez `-Confirm:$False`, l’applet de commande ne demande pas de confirmation.

Pour plus d’informations sur les paramètres `-Confirm` et `$ConfirmPreference`, voir [À propos des variables de préférence](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [gérer des jeux d’enregistrements et des enregistrements](dns-operations-recordsets.md) dans votre zone DNS.
<br>
Découvrez comment [déléguer votre domaine à Azure DNS](dns-domain-delegation.md).
<br>
Examinez la [documentation de référence d’Azure DNS PowerShell](/powershell/module/Az.dns).