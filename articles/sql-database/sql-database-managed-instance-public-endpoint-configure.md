---
title: Configurer le point de terminaison public - Azure SQL Database managed instance | Microsoft Docs
description: Découvrez comment configurer un point de terminaison public pour l’instance managée
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465159"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurer le point de terminaison public dans l’instance managée de base de données SQL Azure

Point de terminaison public pour un [instance managée](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) permet l’accès à votre instance gérée à partir en dehors de la [réseau virtuel](../virtual-network/virtual-networks-overview.md). Vous êtes en mesure d’accéder à votre instance gérée à partir de l’architecture mutualisées des services Azure tels que Power BI, Azure App Service ou un réseau local. En utilisant le point de terminaison public sur une instance gérée, il est inutile d’utiliser un VPN, ce qui peut aider à éviter les problèmes de débit VPN.

Dans cet article, vous allez découvrir comment :

> [!div class="checklist"]
> - Activer le point de terminaison public pour votre instance gérée dans le portail Azure
> - Activer le point de terminaison public pour votre instance gérée à l’aide de PowerShell
> - Configurer votre instance gérée network security group pour autoriser le trafic vers le point de terminaison public instance managée
> - Obtenir la chaîne de connexion de point de terminaison public instance managée

## <a name="permissions"></a>Autorisations

En raison de la sensibilité des données qui se trouve dans une instance gérée, la configuration pour activer le point de terminaison public instance managée nécessite un processus en deux étapes. Cette mesure de sécurité est conforme à la séparation des responsabilités :

- L’activation de point de terminaison public sur une instance managée doit être effectuée par l’administrateur d’instance gérée. Vous trouverez l’administrateur de l’instance gérée sur **vue d’ensemble** page de votre SQL gérés des ressources de l’instance.
- Autoriser le trafic à l’aide d’un groupe de sécurité réseau qui doit être effectuée par un administrateur de réseau. Pour plus d’informations, consultez [les autorisations de groupe de sécurité réseau](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>L’activation de point de terminaison public pour une instance gérée dans le portail Azure

1. Lancez le portail Azure sur <https://portal.azure.com/.>
1. Ouvrez le groupe de ressources avec l’instance gérée, puis sélectionnez le **SQL Database managed instance** que vous souhaitez configurer le point de terminaison public sur.
1. Sur le **sécurité** paramètres, sélectionnez le **réseau virtuel** onglet.
1. Dans la page de configuration de réseau virtuel, sélectionnez **activer** , puis le **enregistrer** icône pour mettre à jour la configuration.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>L’activation de point de terminaison public pour une instance gérée à l’aide de PowerShell

### <a name="enable-public-endpoint"></a>Activer le point de terminaison public

Exécutez les commandes PowerShell suivantes. Remplacez **id d’abonnement** avec votre ID d’abonnement. Remplacez également **rg-name** avec le groupe de ressources pour votre instance gérée, puis remplacez **mi-name** avec le nom de votre instance gérée.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Désactiver le point de terminaison public

Pour désactiver le point de terminaison public à l’aide de PowerShell, que vous serez d’exécuter la commande suivante (et n’oubliez pas également de fermer le groupe de sécurité réseau pour le port entrant 3342 si vous avez configuré) :

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Autoriser le trafic de point de terminaison public sur le groupe de sécurité réseau

1. Si vous disposez de la page de configuration de l’instance gérée toujours ouvert, accédez à la **vue d’ensemble** onglet. Dans le cas contraire, revenez à votre **SQL Database managed instance** ressource. Sélectionnez le **réseau virtuel/sous-réseau** lien, ce qui vous permettra d’accéder à la page de configuration de réseau virtuel.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Sélectionnez le **sous-réseaux** onglet dans le volet de gauche de configuration de votre réseau virtuel et prenez note de la **groupe de sécurité** pour votre instance gérée.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Revenez à votre groupe de ressources qui contient votre instance gérée. Vous devez voir le **groupe de sécurité réseau** nom indiqué ci-dessus. Sélectionnez le nom dans la page de configuration du groupe de sécurité réseau.

1. Sélectionnez le **règles de sécurité entrantes** onglet, et **ajouter** une règle qui a une priorité plus élevée que le **deny_all_inbound** règle avec les paramètres suivants : </br> </br>

    |Paramètre  |Valeur suggérée  |Description   |
    |---------|---------|---------|
    |**Source**     |Toute adresse IP ou une balise de Service         |<ul><li>Pour obtenir des services Azure tels que Power BI, sélectionnez la balise de Service Cloud Azure</li> <li>Pour votre ordinateur ou de la machine virtuelle Azure, utilisez l’adresse IP NAT</li></ul> |
    |**Plages de ports sources**     |*         |Cette option pour laisser * (tout) comme source ports sont généralement alloué dynamiquement et en tant que tel, imprévisible |
    |**Destination**     |Quelconque         |En laissant de destination en tant que pour autoriser le trafic sur le sous-réseau de l’instance managée |
    |**Plages de ports de destination**     |3342         |Port de destination étendue à 3342, qui est le point de terminaison instance managée publique TDS |
    |**Protocole**     |TCP         |Managed instance utilise protocole TCP pour TDS |
    |**Action**     |AUTORISER         |Autoriser le trafic entrant vers une instance gérée par le biais du point de terminaison public |
    |**Priorité**     |1300         |Assurez-vous que cette règle est la priorité plus élevée que le **deny_all_inbound** règle |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342 est utilisé pour le point de terminaison public connexions à managed instance et ne peut pas être modifiées à ce stade.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Obtention de la chaîne de connexion de point de terminaison public instance managée

1. Accédez à la page de configuration d’instance gérée SQL qui a été activée pour le point de terminaison public. Sélectionnez le **chaînes de connexion** onglet sous le **paramètres** configuration.
1. Notez que le nom d’hôte de point de terminaison public est fourni dans le format < mi_name >. **public**. < dns_zone >. database.windows.net et que le port utilisé pour la connexion est 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [à l’aide d’Azure SQL Database managed instance en toute sécurité avec le point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md).