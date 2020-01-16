---
title: Protection des zones DNS et des enregistrements - Azure DNS
description: Dans ce parcours d’apprentissage, commencez à protéger des zones DNS et des jeux d’enregistrement dans Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: allensu
ms.openlocfilehash: c87f9d51c69c4f4d330862e83e5cc8e8e849a988
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969017"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Comment protéger les enregistrements et zones DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les enregistrements et zones DNS sont des ressources critiques. La suppression d’une zone DNS, voire d’un seul enregistrement DNS, peut entraîner une interruption de service totale.  Il est donc important que les zones et enregistrements DNS critiques soient protégés contre toute modification non autorisée ou accidentelle.

Cet article explique comment le DNS Azure permet de protéger vos enregistrements et zones DNS contre de telles modifications.  Nous appliquons deux puissantes fonctionnalités de sécurité d’Azure Resource Manager : le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) et les [verrous de ressources](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour les clients, groupes et ressources Azure. Le RBAC permet d’accorder précisément aux utilisateurs les droits d’accès dont ils ont besoin pour effectuer leur travail. Pour plus d’informations sur la gestion des droits d’accès avec RBAC, voir [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Rôle Contributeur de zone DNS

Le rôle Contributeur de zone DNS est un rôle intégré fourni par Azure pour gérer les ressources DNS.  L’attribution d’autorisations de Contributeur de Zone DNS à un utilisateur ou à un groupe permet à ceux-ci de gérer des ressources DNS, mais pas d’un autre type.

Par exemple, supposons que le groupe de ressources *myzones* contient cinq zones pour Contoso Corporation. L’octroi des autorisations Contributeur de zone DNS pour ce groupe de ressources à l’administrateur DNS lui donne un contrôle total sur ces zones DNS. Cela évite également d’accorder des autorisations inutiles. Par exemple, l’administrateur DNS ne peut ni créer, ni arrêter des machines virtuelles.

La façon la plus simple d’attribuer des autorisations RBAC consiste à utiliser [le portail Azure](../role-based-access-control/role-assignments-portal.md).  Ouvrez **Contrôle d’accès (IAM)** pour le groupe de ressources, sélectionnez **Ajouter**, sélectionnez le rôle **Contributeur de zone DNS**, puis sélectionnez les utilisateurs ou groupes auxquels accorder les autorisations.

![RBAC au niveau groupe de ressources via le portail Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Vous pouvez également [accorder des autorisations à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) :

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

La commande équivalente est également [disponible via l’interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md) :

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC au niveau zone

Vous pouvez appliquer les règles RBAC d’Azure à un abonnement, à un groupe de ressources ou à une ressource. Dans le cas du DNS Azure, cette ressource peut être une zone DNS ou un jeu d’enregistrements.

Par exemple, supposons que le groupe de ressources *myzones* contient la zone *contoso.com* et une sous-zone *customers.contoso.com*, dans laquelle des enregistrements CNAME sont créés pour chaque compte client.  Le compte utilisé pour gérer ces enregistrements CNAME doit avoir les autorisations nécessaires pour créer des enregistrements uniquement dans la zone *customers.contoso.com*. Il ne doit pas avoir accès aux autres zones.

Vous pouvez accorder les autorisations RBAC au niveau zone via le portail Azure.  Ouvrez **Contrôle d’accès (IAM)** pour la zone, sélectionnez **Ajouter**, sélectionnez le rôle **Contributeur de zone DNS**, puis sélectionnez les utilisateurs ou groupes auxquels accorder les autorisations.

![RBAC au niveau Zone DNS via le portail Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Vous pouvez également [accorder des autorisations à l’aide d’Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) :

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

La commande équivalente est également [disponible via l’interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md) :

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>RBAC au niveau jeu d’enregistrements

Nous pouvons aller plus loin. Imaginez l’administratrice de messagerie de la société Contoso Corporation. Elle doit avoir accès aux enregistrements MX et TXT du sommet (apex) de la zone contoso.com.  Elle n’a pas besoin d’accéder à d’autres enregistrements MX ou TXT, ou à des enregistrements d’un autre type.  Le DNS Azure vous permet de lui attribuer des autorisations au niveau jeu d’enregistrements, grâce auxquelles elle accéder précisément aux enregistrements qui lui sont nécessaires.  Elle dispose ainsi exactement du contrôle dont elle a besoin, sans pouvoir apporter d’autres modifications.

Vous pouvez configurer les autorisations RBAC au niveau du jeu d’enregistrements via le portail Azure à l’aide du bouton **Utilisateurs** dans la page du jeu d’enregistrements :

![RBAC au niveau jeu d’enregistrements via le portail Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Vous pouvez également accorder les autorisations RBAC au niveau jeu d’enregistrements en utilisant [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) :

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

La commande équivalente est également [disponible via l’interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md) :

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Rôles personnalisés

Le rôle intégré Contributeur de zone DNS donne un contrôle total sur une ressource DNS. Vous pouvez également créer vos propres rôles Azure personnalisés afin d’offrir un contrôle encore plus précis.

Reprenons l’exemple dans lequel un enregistrement CNAME dans la zone *customers.contoso.com* est créé pour chaque compte client de Contoso Corporation.  Le compte utilisé pour gérer ces enregistrements CNAME doit être autorisé à gérer uniquement les enregistrements CNAME.  Il est alors dans l’impossibilité de modifier des enregistrements d’autres types (par exemple, des enregistrements MX) ou d’effectuer des opérations au niveau de zone telles que supprimer une zone.

L’exemple suivant montre une définition de rôle personnalisé pour la gestion des enregistrements CNAME uniquement :

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

La propriété Actions définit les autorisations spécifiques de DNS suivantes :

* `Microsoft.Network/dnsZones/CNAME/*` accorde un contrôle total sur les enregistrements CNAME.
* `Microsoft.Network/dnsZones/read` accorde l’autorisation de lire les zones DNS, mais ne pas de les modifier, ce qui permet de voir la zone dans laquelle l’enregistrement CNAME est créé.

Les Actions restantes sont copiées à partir du [rôle intégré Contributeur de Zone DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> L’utilisation d’un rôle RBAC personnalisé pour empêcher la suppression de jeux d’enregistrements tout en autorisant leur mise à jour ne constitue pas un contrôle efficace. Cela empêche la suppression de jeux d’enregistrements, mais pas leur modification.  Les modifications autorisées incluent l’ajout et la suppression d’enregistrements du jeu d’enregistrements, y compris la suppression de tous les enregistrements pour ne laisser qu’un jeu d’enregistrements vide. Sur le plan de la résolution DNS, cela produit le même effet que la suppression du jeu d’enregistrements.

Il n’est actuellement pas possible de définir des rôles personnalisés via le portail Azure. Vous pouvez créer un rôle personnalisé basé sur cette définition de rôle en utilisant Azure PowerShell :

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Vous pouvez également le faire via l’interface de ligne de commande Azure :

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Ensuite, vous pouvez attribuer le rôle de la même façon qu’un rôle intégré, en procédant de la manière décrite précédemment dans cet article.

Pour plus d’informations sur la façon de créer, gérer et attribuer des rôles personnalisés, consultez [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Verrous de ressources

En plus de RBAC, Azure Resource Manager prend en charge un autre type de contrôle de sécurité permettant de verrouiller des ressources. Si les règles RBAC vous permettent de contrôler les actions d’utilisateurs ou de groupes spécifiques, les verrous appliqués à des ressources valent pour l’ensemble des utilisateurs et des rôles. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Il existe deux types de verrou de ressource : **CanNotDelete** et **ReadOnly**. Vous pouvez les appliquer à une zone DNS ou à un jeu d’enregistrements.  Les sections suivantes décrivent quelques scénarios courants et la manière de les prendre en charge à l’aide de verrous de ressources.

### <a name="protecting-against-all-changes"></a>Protection contre toute modification

Pour empêcher toute modification, appliquez à la zone un verrou ReadOnly.  Cela empêche toute création, modification ou suppression de jeux d’enregistrements.

Vous pouvez créer des verrous de ressources au niveau zone via le portail Azure.  Dans la page des zones DNS, sélectionnez **Verrous**, puis **+Ajouter** :

![Verrous de ressources au niveau zone via le portail Azure](./media/dns-protect-zones-recordsets/locks1.png)

Vous pouvez également créer des verrous de ressources au niveau zone via Azure PowerShell :

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

La configuration de verrous de ressources Azure n’est actuellement pas possible via l’interface de ligne de commande Azure.

### <a name="protecting-individual-records"></a>Protection d’enregistrements spécifiques

Pour empêcher la modification d’un jeu d’enregistrements DNS, appliquez-lui un verrou ReadOnly.

> [!NOTE]
> L’application d’un verrou CanNotDelete à un jeu d’enregistrements n’est pas un contrôle efficace. Cela empêche la suppression du jeu d’enregistrements, mais pas sa modification.  Les modifications autorisées incluent l’ajout et la suppression d’enregistrements du jeu d’enregistrements, y compris la suppression de tous les enregistrements pour ne laisser qu’un jeu d’enregistrements vide. Sur le plan de la résolution DNS, cela produit le même effet que la suppression du jeu d’enregistrements.

Vous pouvez actuellement configurer des verrous de ressources au niveau jeu d’enregistrements uniquement à l’aide d’Azure PowerShell.  Il n’est pas possible de le faire via le portail Azure ou l’interface de ligne de commande Azure.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Protection contre la suppression de zone

Lors de la suppression d’une zone dans le DNS Azure, tous les jeux d’enregistrements contenus dans celle-ci sont supprimés.  Cette opération ne peut pas être annulée.  La suppression accidentelle d’une zone critique peut avoir une incidence considérable.  Il est donc très important de protéger les zones contre toute suppression accidentelle.

L’application d’un verrou CanNotDelete à une zone empêche la suppression de celle-ci.  Toutefois, les ressources enfants héritant des verrous, cela empêche également la suppression de jeux d’enregistrements figurant dans la zone, ce qui peut être indésirable.  Par ailleurs, comme décrit dans la Remarque ci-dessus, ce procédé est également inefficace dans la mesure où il reste toujours possible de supprimer des enregistrements à partir des jeux d’enregistrements existants.

Une autre solution est d’appliquer un verrou CanNotDelete à un jeu d’enregistrements dans la zone, comme le jeu d’enregistrements SOA.  La zone ne pouvant pas être supprimée sans supprimer également les jeux d’enregistrements qu’elle contient, cela empêche de supprimer la zone, tout en permettant de modifier librement les jeux d’enregistrements. En cas de tentative de suppression de la zone, Azure Resource Manager détecte que cela aurait pour effet de supprimer également le jeu d’enregistrements SOA. Celui-ci étant verrouillé, l’appel est bloqué.  Aucun jeu d’enregistrements n’est supprimé.

La commande PowerShell suivante crée un verrou CanNotDelete sur l’enregistrement SOA de la zone donnée :

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Un autre moyen d’empêcher la suppression accidentelle d’une zone consiste à utiliser un rôle personnalisé afin que les comptes d’opérateur et de service utilisés pour gérer vos zones ne soient pas autorisés à supprimer des zones. Si vous avez besoin de supprimer une zone, vous pouvez appliquer une suppression en deux étapes : octroyer des autorisations de suppression de zone (dans l’étendue de la zone, afin d’éviter la suppression d’une zone incorrecte), puis supprimer la zone.

Cette seconde approche présente l’avantage qu’elle fonctionne pour toutes les zones auxquelles ces comptes accèdent, sans avoir à créer de verrous. En revanche, tous les comptes avec des autorisations de suppression de zone, tels que le propriétaire de l’abonnement, peuvent toujours supprimer accidentellement une zone critique.

Il est possible d’utiliser les deux approches (verrous de ressources et rôles personnalisés) en même temps en guise de protection en profondeur des zones DNS.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’utilisation de RBAC, voir [Prise en main de la gestion des accès dans le portail Azure](../role-based-access-control/overview.md).
* Pour plus d’informations sur l’utilisation des verrous de ressources, voir [Verrouiller des ressources avec Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
