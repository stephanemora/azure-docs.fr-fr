---
title: Configurer des pare-feux et des réseaux virtuels dans Stockage Azure | Microsoft Docs
description: Configurez la sécurité réseau en couche pour votre compte de stockage à l’aide des pare-feu d’Azure Storage et d’Azure Virtual Network.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3ec4e6ee49052657210fffa8976b661c1a9e5948
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827458"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurer des pare-feux et des réseaux virtuels dans Stockage Azure

Stockage Azure fournit un modèle de sécurité en couche. Ce modèle vous permet de sécuriser et de contrôler le niveau d’accès à vos comptes de stockage selon les exigences de vos applications et environnements d’entreprise, en fonction du type et du sous-ensemble de réseaux utilisés. Quand des règles de réseau sont configurées, seules les applications demandant des données sur l’ensemble de réseaux spécifié peuvent accéder à un compte de stockage. Vous pouvez limiter l’accès à votre compte de stockage aux demandes provenant d’adresses IP ou de plages d’adresses IP spécifiées, ou d’une liste de sous-réseaux dans un réseau virtuel Azure (VNet).

Les comptes de stockage ont un point de terminaison public accessible via Internet. Vous pouvez également créer des [points de terminaison privés pour votre compte de stockage](storage-private-endpoints.md), afin d’attribuer une adresse IP privée de votre réseau virtuel au compte de stockage, et de sécuriser tout le trafic entre votre réseau virtuel et le compte de stockage via un lien privé. Le pare-feu de stockage Azure fournit un contrôle d’accès pour le point de terminaison public de votre compte de stockage. Vous pouvez également utiliser ce pare-feu pour bloquer tout accès via le point de terminaison public quand des points de terminaison privés sont utilisés. La configuration de votre pare-feu de stockage permet aussi de sélectionner des services approuvés de la plateforme Azure pour accéder au compte de stockage de manière sécurisée.

Une application qui accède à un compte de stockage alors que des règles de réseau sont toujours activées requiert une autorisation appropriée pour la demande. L’autorisation est prise en charge avec les informations d’identification Azure Active Directory (Azure AD) pour les objets blob et les files d’attente, avec une clé d’accès de compte valide ou un jeton SAS.

> [!IMPORTANT]
> L’activation des règles de pare-feu pour votre compte de stockage bloque les demandes entrantes pour les données par défaut, sauf si les demandes proviennent d’un service qui fonctionne au sein d’un réseau virtuel (VNet) Azure ou à partir d’adresses IP publiques autorisées. Les demandes qui sont bloquées comprennent les demandes émanant d’autres services Azure, du portail Azure, des services de journalisation et de métriques, etc.
>
> Vous pouvez accorder l’accès aux services Azure qui fonctionnent à partir d’un réseau virtuel en autorisant le trafic en provenance du sous-réseau hébergeant l’instance de service. Vous pouvez également activer un nombre limité de scénarios via le mécanisme [Exceptions](#exceptions) décrit ci-dessous. Pour accéder aux données du compte de stockage via le portail Azure, vous devez utiliser un ordinateur qui se trouve dans la limite de confiance (IP ou réseau virtuel) que vous avez définie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénarios

Pour sécuriser votre compte de stockage, vous devez commencer par configurer une règle pour refuser l’accès au trafic de tous les réseaux (y compris le trafic Internet) sur le point de terminaison public, par défaut. Ensuite, vous devez configurer des règles qui autorisent l’accès au trafic en provenance de réseaux virtuels spécifiques. Vous pouvez également configurer des règles pour accorder l’accès au trafic en provenance de plages d’adresses IP Internet publiques, en autorisant des connexions à partir de clients Internet ou locaux spécifiques. Cette configuration vous permet de créer une limite de réseau sécurisée pour vos applications.

Vous pouvez combiner des règles de pare-feu qui autorisent l’accès à partir de réseaux virtuels spécifiques et de plages d’adresses IP publiques sur le même compte de stockage. Les règles de pare-feu de stockage peuvent être appliquées aux comptes de stockage existants ou à la création de comptes de stockage.

Les règles de pare-feu de stockage s’appliquent au point de terminaison public d’un compte de stockage. Vous n’avez pas besoin de règles d’accès de pare-feu pour autoriser le trafic via les points de terminaison privés d’un compte de stockage. Le processus d’approbation de la création d’un point de terminaison privé accorde un accès implicite au trafic à partir du sous-réseau qui héberge le point de terminaison privé.

Les règles de réseau sont appliquées sur tous les protocoles réseau vers le stockage Azure, notamment REST et SMB. Pour accéder aux données avec des outils tels que le portail Azure, l’Explorateur de stockage et AZCopy, vous devez configurer des règles de réseau explicites.

Une fois appliquées, les règles de réseau concernent toutes les demandes. Les jetons SAS qui accordent l’accès à une adresse IP spécifique servent à limiter l’accès du détenteur du jeton, mais n’accordent pas d’accès au-delà des règles de réseau configurées.

Le trafic des disques de machine virtuelle (notamment les opérations de montage et démontage et les E/S de disque) n’est pas affecté par les règles de réseau. L’accès REST aux objets blob de pages est protégé par les règles de réseau.

Les comptes de stockage Classic ne prennent pas en charge les pare-feux et les réseaux virtuels.

Vous pouvez utiliser des disques non managés dans les comptes de stockage avec des règles de réseau appliquées à la sauvegarde et la restauration de machines virtuelles en créant une exception. Ce processus est décrit dans la section [Exceptions](#exceptions) de cet article. Les exceptions de pare-feu ne sont pas applicables avec disques managés dans la mesure où ils sont déjà managés par Azure.

## <a name="change-the-default-network-access-rule"></a>Changer la règle d’accès réseau par défaut

Par défaut, les comptes de stockage acceptent les connexions des clients sur n’importe quel réseau. Pour limiter l’accès aux réseaux sélectionnés, vous devez d’abord changer l’action par défaut.

> [!WARNING]
> Le changement des règles de réseau peut impacter la capacité de vos applications à se connecter au stockage Azure. La définition de la règle de réseau par défaut sur **Refuser** bloque tout accès aux données, sauf si des règles de réseau spécifiques **accordant** l’accès sont également appliquées. Accordez l’accès uniquement aux réseaux autorisés à l’aide des règles de réseau avant de changer la règle par défaut pour refuser l’accès.

### <a name="managing-default-network-access-rules"></a>Gestion des règles d’accès réseau par défaut

Vous pouvez gérer les règles d’accès réseau par défaut pour les comptes de stockage via le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Pour refuser l’accès par défaut, choisissez d’autoriser l’accès à partir de **Réseaux sélectionnés**. Pour autoriser le trafic de tous les réseaux, choisissez d’autoriser l’accès à partir de **Tous les réseaux**.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Affichez l’état de la règle par défaut pour le compte de stockage.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Définissez la règle par défaut pour refuser l’accès réseau par défaut.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Définissez la règle par défaut pour autoriser l’accès réseau par défaut.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Affichez l’état de la règle par défaut pour le compte de stockage.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Définissez la règle par défaut pour refuser l’accès réseau par défaut.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Définissez la règle par défaut pour autoriser l’accès réseau par défaut.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Accorder l’accès à partir d’un réseau virtuel

Vous pouvez configurer des comptes de stockage pour autoriser l’accès uniquement à partir de sous-réseaux spécifiques. Les sous-réseaux autorisés peuvent appartenir à un réseau virtuel dans le même abonnement ou dans un autre abonnement, y compris dans un abonnement appartenant à un autre locataire Azure Active Directory.

Activez un [point de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview) pour le stockage Azure dans le réseau virtuel. Le point de terminaison de service achemine le trafic à partir du réseau virtuel via un chemin d’accès optimal vers le service Stockage Azure. Les identités du sous-réseau et du réseau virtuel sont également transmises avec chaque demande. Les administrateurs peuvent ensuite configurer des règles de réseau pour le compte de stockage qui autorisent la réception des demandes à partir de sous-réseaux spécifiques d’un réseau virtuel. Les clients qui obtiennent un accès par le biais de ces règles de réseau doivent continuer à respecter les exigences d’autorisation du compte de stockage pour accéder aux données.

Chaque compte de stockage prend en charge jusqu’à 200 règles de réseau virtuel qui peuvent être combinées avec des [règles de réseau IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Régions de réseau virtuel disponibles

En général, les points de terminaison de service fonctionnent entre les réseaux virtuels et les instances de service d’une même région Azure. Quand les points de terminaison de service sont utilisés avec Stockage Azure, cette étendue inclut aussi la [région jumelée](/azure/best-practices-availability-paired-regions). Les points de terminaison de service permettent une continuité des activités pendant un basculement régional ainsi qu’un accès sans interruption aux instances de stockage géoredondantes en lecture seule (RA-GRS). Les règles de réseau qui autorisent l’accès à un compte de stockage à partir d’un réseau virtuel accordent également l’accès à toutes les instances RA-GRS.

Quand vous planifiez une récupération d’urgence en cas de panne régionale, vous devez créer les réseaux virtuels à l’avance dans la région jumelée. Activez les points de terminaison de service pour le Stockage Azure, avec des règles de réseau accordant l’accès à partir de ces réseaux virtuels alternatifs. Appliquez ensuite ces règles à vos comptes de stockage géoredondants.

> [!NOTE]
> Les points de terminaison de service ne s’appliquent pas au trafic extérieur à la région du réseau virtuel et à la région jumelée désignée. Vous pouvez appliquer les règles de réseau accordant l’accès à partir de réseaux virtuels aux comptes de stockage dans la région principale d’un compte de stockage ou dans la région jumelée désignée.

### <a name="required-permissions"></a>Autorisations requises

Pour appliquer une règle de réseau virtuel à un compte de stockage, l’utilisateur doit disposer des autorisations appropriées pour les sous-réseaux à ajouter. L’autorisation nécessaire est *joindre le service à un sous-réseau* et est incluse dans le rôle intégré *Contributeur de compte de stockage*. Elle peut également être ajoutée aux définitions de rôles personnalisés.

Le compte de stockage et les réseaux virtuels auxquels l’accès est accordé peuvent se trouver dans des abonnements différents, y compris des abonnements appartenant à un autre locataire Azure AD.

> [!NOTE]
> La configuration de règles qui accordent l’accès à des sous-réseaux de réseaux virtuels qui font partie d’un autre locataire Azure Active Directory n’est actuellement possible que via PowerShell, l’interface de ligne de commande et des API REST. S’il est possible de consulter ces règles sur le portail Azure, il est impossible de les y configurer.

### <a name="managing-virtual-network-rules"></a>Gestion des règles de réseau virtuel

Vous pouvez gérer les règles de réseau virtuel pour les comptes de stockage via le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Vérifiez que vous avez choisi d’autoriser l’accès à partir des **Réseaux sélectionnés**.

1. Pour accorder l’accès à un réseau virtuel avec une nouvelle règle de réseau, sous **Réseaux virtuels**, cliquez sur **Ajouter un réseau virtuel existant**, sélectionnez les options **Réseaux virtuels** et **Sous-réseaux**, puis cliquez sur **Ajouter**. Pour créer un réseau virtuel et lui accorder l’accès, cliquez sur **Ajouter un nouveau réseau virtuel**. Fournissez les informations nécessaires pour créer le nouveau réseau virtuel, puis cliquez sur **Créer**.

    > [!NOTE]
    > Si un point de terminaison de service pour le stockage Azure n’a pas déjà été configuré pour le réseau virtuel et les sous-réseaux sélectionnés, vous pouvez le configurer dans le cadre de cette opération.
    >
    > Actuellement, seuls des réseaux virtuels appartenant à un même locataire Azure Active Directory s’affichent pour sélection lors de la création d’une règle. Pour accorder l’accès à un sous-réseau d’un réseau virtuel appartenant à un autre locataire, utilisez PowerShell, l’interface de ligne de commande ou des API REST.

1. Pour supprimer une règle de réseau ou sous-réseau virtuel, cliquez sur **...** pour ouvrir le menu contextuel du réseau ou sous-réseau virtuel, puis cliquez sur **Supprimer**.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Listez les règles de réseau virtuel.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Activez le point de terminaison de service pour Stockage Azure sur un réseau virtuel et un sous-réseau existants.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Pour ajouter une règle de réseau pour un sous-réseau d’un réseau virtuel appartenant à un autre locataire Azure AD, utilisez un paramètre **VirtualNetworkResourceId** complet sous la forme « /subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name ».

1. Supprimez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Listez les règles de réseau virtuel.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Activez le point de terminaison de service pour Stockage Azure sur un réseau virtuel et un sous-réseau existants.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Ajoutez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Pour ajouter une règle pour un sous-réseau d’un réseau virtuel appartenant à un autre locataire Azure AD, utilisez un ID de sous-réseau complet sous la forme « /subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\> ».
    >
    > Vous pouvez utiliser le paramètre **subscription** pour récupérer l’ID de sous-réseau d’un réseau virtuel appartenant à un autre locataire Azure AD.

1. Supprimez une règle de réseau pour un réseau virtuel et un sous-réseau.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

## <a name="grant-access-from-an-internet-ip-range"></a>Accorder l’accès à partir d’une plage d’adresses IP Internet

Vous pouvez configurer les comptes de stockage pour autoriser l’accès à partir de plages d’adresses IP Internet publiques spécifiques. Cette configuration donne l’accès à des services Internet et des réseaux locaux spécifiques et bloque le trafic Internet général.

Fournissez les plages d’adresses Internet autorisées à l’aide de la [notation CIDR](https://tools.ietf.org/html/rfc4632) sous la forme *16.17.18.0/24* ou sous la forme d’adresses IP individuelles de type *16.17.18.19*.

   > [!NOTE]
   > Les petites plages d’adresses qui utilisent les tailles de préfixe « /31 » ou « /32 » ne sont pas prises en charge. Ces plages doivent être configurées à l’aide des règles d’adresses IP individuelles.

Les règles de réseau IP sont autorisées uniquement pour les adresses IP **Internet publiques**. Les plages d’adresses IP réservées aux réseaux privés (comme défini dans [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) ne sont pas autorisées dans les règles IP. Les réseaux privés incluent des adresses qui commencent par _10.*_ , _172.16.*_  - _172.31.*_ et _192.168.*_ .

   > [!NOTE]
   > Les règles de réseau IP n’ont aucun effet sur les requêtes provenant de la même région Azure que le compte de stockage. Utilisez des [règles de réseau virtuel](#grant-access-from-a-virtual-network) pour autoriser les requêtes de même région.

  > [!NOTE]
  > Les services déployés dans la même région que le compte de stockage utilisent des adresses IP Azure privées pour la communication. Vous ne pouvez donc pas restreindre l’accès à des services Azure spécifiques en fonction de leur plage d’adresses IP sortantes publiques.

Seules les adresses IPV4 sont prises en charge dans la configuration des règles de pare-feu de stockage.

Chaque compte de stockage peut prendre en charge jusqu’à 100 règles de réseau IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configuration de l’accès à partir de réseaux locaux

Pour accorder l’accès à votre compte de stockage à partir de réseaux locaux avec une règle de réseau IP, vous devez identifier les adresses IP Internet utilisées par votre réseau. Contactez votre administrateur réseau pour obtenir de l’aide.

Si vous utilisez [ExpressRoute](/azure/expressroute/expressroute-introduction) localement, pour le Peering public ou Microsoft, vous devez identifier les adresses IP NAT (traduction d’adresses réseau) utilisées. Pour le peering public, chaque circuit ExpressRoute utilise par défaut deux adresses IP NAT qui sont appliquées au trafic de service Azure lorsque le trafic entre dans le réseau principal de Microsoft Azure. Pour le peering Microsoft, les adresses IP NAT utilisées sont fournies par le client ou par le fournisseur du service. Pour autoriser l’accès à vos ressources de votre service, vous devez autoriser ces adresses IP publiques dans le paramètre de pare-feu IP de ressource. Pour trouver les adresses IP de votre circuit ExpressRoute de peering public, [ouvrez un ticket de support avec ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via le portail Azure. Découvrez d’autres informations sur le [peering public et Microsoft NAT pour ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestion des règles de réseau IP

Vous pouvez gérer les règles de réseau IP pour les comptes de stockage via le portail Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Vérifiez que vous avez choisi d’autoriser l’accès à partir des **Réseaux sélectionnés**.

1. Pour accorder l’accès à une plage d’adresses IP Internet, entrez l’adresse IP ou la plage d’adresses IP (au format CIDR) sous **Pare-feu** > **Plages d’adresses**.

1. Pour supprimer une règle de réseau IP, cliquez sur l’icône Corbeille à côté de la plage d’adresses.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Listez les règles de réseau IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Ajoutez une règle de réseau pour une adresse IP individuelle.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ajoutez une règle de réseau pour une plage d’adresses IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Supprimez une règle de réseau pour une adresse IP individuelle.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Supprimez une règle de réseau pour une plage d’adresses IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Listez les règles de réseau IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Ajoutez une règle de réseau pour une adresse IP individuelle.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Ajoutez une règle de réseau pour une plage d’adresses IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Supprimez une règle de réseau pour une adresse IP individuelle.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Supprimez une règle de réseau pour une plage d’adresses IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon les règles de réseau n’ont aucun effet.

## <a name="exceptions"></a>Exceptions

Les règles de réseau vous aident à créer un environnement sécurisé pour les connexions entre vos applications et vos données dans la plupart des scénarios. Toutefois, certaines applications font appel à des services Azure qui ne peuvent pas être isolés individuellement par le biais de règles de réseau virtuel ou d’adresses IP. Pour autant, ces services doivent avoir accès au stockage afin d’activer toutes les fonctionnalités des applications. Dans ces cas de figure, vous pouvez utiliser le paramètre ***Autoriser les services Microsoft approuvés...*** pour activer de tels services afin d’accéder à vos données, journaux ou analyses.

### <a name="trusted-microsoft-services"></a>Services Microsoft approuvés

Certains services Microsoft fonctionnent à partir de réseaux qui ne peuvent pas être inclus dans vos règles de réseau. Vous pouvez accorder à une partie de ces services Microsoft approuvés l’accès au compte de stockage, mais conserver des règles de réseau pour d’autres applications. Ces services approuvés se connectent ensuite à votre compte de stockage de manière sécurisée à l’aide de l’authentification renforcée. Nous autorisons deux modes d’accès approuvés pour les services Microsoft.

- Les ressources de certains services, **quand ils sont inscrits dans votre abonnement**, peuvent accéder à votre compte de stockage **dans le même abonnement** pour des opérations spécifiques, comme la journalisation ou la sauvegarde.
- Vous pouvez accorder aux ressources de certains services un accès explicite à votre compte de stockage en **attribuant un rôle Azure** à son identité managée attribuée par le système.


Quand vous activez le paramètre **Autoriser les services Microsoft approuvés...** , les ressources des services suivants qui sont inscrites dans le même abonnement que votre compte de stockage bénéficient d’un accès pour un ensemble limité d’opérations, comme décrit ci-dessous :

| Service                  | Nom du fournisseur de ressources     | Opérations autorisées                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Sauvegarde Azure             | Microsoft.RecoveryServices | Effectuez des sauvegardes et des restaurations de disques non managés dans des machines virtuelles IAAS. (non requis pour les disques managés). [Plus d’informations](/azure/backup/backup-introduction-to-azure-backup) |
| Azure Data Box           | Microsoft.DataBox          | Permet l’importation des données vers Azure à l’aide de Microsoft Azure Data Box. [Plus d’informations](/azure/databox/data-box-overview) |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Création d’une image personnalisée et installation de l’artefact. [Plus d’informations](../../devtest-labs/devtest-lab-overview.md) |
| Azure Event Grid         | Microsoft.EventGrid        | Permettez la publication d’événements Stockage Blob et autorisez Event Grid à effectuer des publications dans les files d’attente de stockage. En savoir plus sur les [événements Stockage Blob](/azure/event-grid/event-sources) et la [publication dans les files d’attente](/azure/event-grid/event-handlers). |
| Hubs d'événements Azure         | Microsoft.EventHub         | Archivage des données avec Event Hubs Capture. [En savoir plus](/azure/event-hubs/event-hubs-capture-overview) |
| Azure File Sync          | Microsoft.StorageSync      | Vous permet de transformer votre serveur de fichiers local en cache pour les partages de fichiers Azure. Autoriser la synchronisation sur plusieurs sites, une récupération d’urgence rapide et une sauvegarde sur le cloud. [En savoir plus](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Approvisionnez le contenu initial du système de fichiers par défaut pour un nouveau cluster HDInsight. [Plus d’informations](/azure/hdinsight/hdinsight-hadoop-use-blob-storage) |
| Azure Import/Export      | Microsoft.ImportExport     | Permet l’importation de données dans Azure et l’exportation de données à partir d’Azure avec le service Import/Export. [Plus d’informations](/azure/storage/common/storage-import-export-service)  |
| Azure Monitor            | Microsoft.Insights         | Autorise l’écriture de données de supervision dans un compte de stockage sécurisé, à savoir les journaux de ressources, les journaux de connexion et d’audit Azure Active Directory et les journaux Microsoft Intune. [Plus d’informations](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) |
| Mise en réseau Azure         | Microsoft.Network          | Stockez et analysez les journaux du trafic réseau, notamment celui qui transite par les services Network Watcher et Traffic Analytics. [Plus d’informations](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Activez la réplication pour la reprise d’activité des machines virtuelles Azure IaaS lors de l’utilisation de comptes de stockage de cache avec pare-feu activé, de stockage source ou de stockage cible.  [Plus d’informations](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) |

Le paramètre **Autoriser les services Microsoft approuvés...** permet à une instance particulière des services ci-dessous d’accéder au compte de stockage, si vous [attribuez explicitement un rôle Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) à l’[identité managée attribuée par le système](../../active-directory/managed-identities-azure-resources/overview.md) pour cette instance de ressource. Dans ce cas, l’étendue de l’accès pour l’instance correspond au rôle Azure affecté à l’identité managée.

| Service                        | Nom du fournisseur de ressources                 | Objectif            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Gestion des API Azure           | Microsoft.ApiManagement/service        | Active l’accès au service Gestion des API pour les comptes de stockage derrière un pare-feu à l’aide de stratégies. [Plus d’informations](/azure/api-management/api-management-authentication-policies#use-managed-identity-in-send-request-policy) |
| Recherche cognitive Azure         | Microsoft.Search/searchServices        | Permet aux services Recherche cognitive d’accéder aux comptes de stockage pour l’indexation, le traitement et l’interrogation. |
| Tâches Azure Container Registry | Microsoft.ContainerRegistry/registries | ACR Tasks peut accéder aux comptes de stockage lors de la génération d’images conteneur. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Autorise l’accès aux comptes de stockage par le biais du Runtime ADF. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Autorise l’accès aux comptes de stockage par le biais de Data Share. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Autorise l’écriture des données d’un IoT Hub dans le stockage d’objets blob. [En savoir plus](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Permet aux applications logiques d’accéder aux comptes de stockage. [Plus d’informations](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity) |
| Service Azure Machine Learning | Microsoft.MachineLearningServices      | Les espaces de travail Azure Machine Learning autorisés écrivent des sorties, des modèles et des journaux expérimentaux dans le stockage d’objets blob et lisent les données. [Plus d’informations](/azure/machine-learning/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace) | 
| Azure Synapse Analytics (anciennement SQL Data Warehouse)       | Microsoft.Sql                          | Permet l'importation et l'exportation de données à partir de bases de données SQL spécifiques à l'aide de l'instruction COPY ou de PolyBase. [Plus d’informations](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) |
| Azure SQL Database       | Microsoft.Sql                          | Permet d'[importer](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage) des données à partir de comptes de stockage et d'[écrire](https://docs.microsoft.com/azure/azure-sql/database/audit-write-storage-account-behind-vnet-firewall) des données d'audit sur des comptes de stockage situés derrière un pare-feu. |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Autorise l’écriture des données d’une tâche de streaming dans le stockage d’objets blob. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. [Plus d’informations](/azure/stream-analytics/blob-output-managed-identity) |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces          | Permet l’accès aux données dans Stockage Azure à partir de Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Accès aux données Storage Analytics

Dans certains cas, un accès en lecture aux journaux et aux métriques de ressources est nécessaire en dehors de la limite du réseau. Quand vous configurez l’accès aux services approuvés pour le compte de stockage, vous pouvez autoriser l’accès en lecture aux fichiers journaux ou aux tables de métriques, ou aux deux. [Découvrez plus d’informations sur l’utilisation de Storage Analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestion des exceptions

Vous pouvez gérer les exceptions de règle de réseau dans le portail Azure, PowerShell ou Azure CLI v2.

#### <a name="azure-portal"></a>Portail Azure

1. Accédez au compte de stockage que vous voulez sécuriser.

1. Cliquez sur le menu de paramètres appelé **Pare-feu et réseaux virtuels**.

1. Vérifiez que vous avez choisi d’autoriser l’accès à partir des **Réseaux sélectionnés**.

1. Sous **Exceptions**, sélectionnez les exceptions que vous voulez accorder.

1. Cliquez sur **Enregistrer** pour enregistrer les changements.

#### <a name="powershell"></a>PowerShell

1. Installez [Azure PowerShell](/powershell/azure/install-Az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps).

1. Affichez les exceptions pour les règles de réseau du compte de stockage.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configurez les exceptions aux règles de réseau du compte de stockage.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Supprimez les exceptions aux règles de réseau du compte de stockage.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon la suppression des exceptions n’a aucun effet.

#### <a name="cliv2"></a>CLIv2

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli).

1. Affichez les exceptions pour les règles de réseau du compte de stockage.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configurez les exceptions aux règles de réseau du compte de stockage.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Supprimez les exceptions aux règles de réseau du compte de stockage.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> [Définissez la règle par défaut](#change-the-default-network-access-rule) sur **Refuser**, sinon la suppression des exceptions n’a aucun effet.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur les points de terminaison de service du réseau Azure dans [Points de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview).

Explorez en détail la sécurité de Stockage Azure dans [Guide de sécurité de Stockage Azure](../blobs/security-recommendations.md).
