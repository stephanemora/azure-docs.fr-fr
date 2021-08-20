---
title: Configurer les pare-feux et réseaux virtuels d’Azure Key Vault – Azure Key Vault
description: Découvrez les paramètres réseau Key Vault.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3e9332706c3bcc9d4f4cabd20d10fa099271d69
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285323"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurer les pare-feux et réseaux virtuels d’Azure Key Vault

Ce document aborde en détail les différentes configurations du pare-feu Key Vault. Pour suivre les instructions pas à pas de configuration de ces paramètres, référez-vous à [ce guide](how-to-azure-key-vault-network-security.md). 

Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel pour Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Paramètres du pare-feu

Cette section présente les différentes configurations possibles du pare-feu Azure Key Vault.

### <a name="key-vault-firewall-disabled-default"></a>Pare-feu Key Vault désactivé (configuration par défaut)

Par défaut, quand vous créez un coffre de clés, le pare-feu Azure Key Vault est désactivé. Toutes les applications et tous les services Azure peuvent accéder au coffre de clés et lui envoyer des requêtes. Notez que cette configuration n’implique pas que tous les utilisateurs pourront effectuer des opérations sur le coffre de clés. Le coffre de clés restreint toujours l’accès aux secrets, aux clés et aux certificats qu’il stocke en exigeant des autorisations de stratégie d’accès et une authentification Azure Active Directory. Pour mieux comprendre l’authentification du coffre de clés, consultez [ici](./authentication.md) l’article sur les concepts de base de l’authentification du coffre de clés. Pour plus d’informations, consultez [Accès à Azure Key Vault derrière un pare-feu](./access-behind-firewall.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Pare-feu Key Vault activé (services approuvés uniquement)

Quand vous activez le pare-feu Key Vault, vous pouvez choisir d’autoriser les services Microsoft approuvés à contourner ce pare-feu. La liste des services approuvés n’inclut pas tous les services Azure. Par exemple, Azure DevOps n’y figure pas. **Cela ne signifie pas que les services qui ne sont pas dans la liste des services approuvés ne sont pas approuvés ou sécurisés.** La liste des services approuvés englobe les services dans lesquels tout le code exécuté est contrôlé par Microsoft. Du fait que les utilisateurs peuvent écrire du code personnalisé dans des services Azure comme Azure DevOps, Microsoft n’offre pas l’option de créer une approbation permanente pour le service. En outre, la présence d’un service dans la liste des services approuvés ne signifie pas pour autant que le service est autorisé dans tous les scénarios. 

Pour déterminer si un service que vous voulez utiliser fait partie de la liste des services approuvés, consultez [ce document](./overview-vnet-service-endpoints.md#trusted-services).
Vous pouvez suivre les instructions du guide pratique concernant le [Portail, Azure CLI et PowerShell](how-to-azure-key-vault-network-security.md).

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Pare-feu Key Vault activé (adresses et plages IPv4 - adresses IP statiques)

Si vous souhaitez autoriser un service particulier à accéder au coffre de clés par le biais du pare-feu Key Vault, vous pouvez ajouter son adresse IP à la liste autorisée du pare-feu Key Vault. Cette configuration est idéale pour les services qui utilisent des adresses IP statiques ou des plages connues. Il existe une limite de 1000 de plages CIDR pour ce cas.

Pour autoriser une adresse ou plage IP d’une ressource Azure telle qu’une application web ou une application logique, effectuez les étapes suivantes.

1. Se connecter au portail Azure.
1. Sélectionnez la ressource (instance spécifique du service).
1. Cliquez sur le panneau « Propriétés » sous « Paramètres ».
1. Recherchez le champ « Adresse IP ».
1. Copiez cette valeur ou plage et entrez-la dans la liste d’autorisation du pare-feu Key Vault.

Pour autoriser un service Azure entier, par le biais du pare-feu Key Vault, utilisez la liste des adresses IP du centre de données documentées publiquement pour Azure, accessible [ici](https://www.microsoft.com/download/details.aspx?id=41653). Recherchez les adresses IP associées au service dont vous avez besoin dans la région de votre choix et ajoutez ces adresses IP au pare-feu Key Vault à l’aide des étapes ci-dessus.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Pare-feu Key Vault activé (réseaux virtuels - adresses IP dynamiques)

Si vous essayez d’autoriser une ressource Azure telle qu’une machine virtuelle par le biais du coffre de clés, il est possible que vous ne puissiez pas utiliser des adresses IP statiques et que vous ne souhaitiez pas autoriser toutes les adresses IP de machines virtuelles Azure à accéder à votre coffre de clés.

Dans ce cas, vous devez créer la ressource dans un réseau virtuel, puis autoriser le trafic provenant du réseau virtuel et du sous-réseau spécifiques à accéder à votre coffre de clés. Pour ce faire, procédez comme suit.

1. Se connecter au portail Azure.
1. Sélectionnez le coffre de clés à configurer.
1. Sélectionnez le panneau « Réseau ».
1. Sélectionnez « + Ajouter un réseau virtuel existant ».
1. Sélectionnez le réseau virtuel et le sous-réseau que vous souhaitez autoriser via le pare-feu Key Vault.

### <a name="key-vault-firewall-enabled-private-link"></a>Pare-feu Key Vault activé (liaison privée)

Pour savoir comment configurer une connexion de liaison privée sur votre coffre de clés, consultez [ce document](./private-link-service.md).

> [!IMPORTANT]
> Une fois que les règles de pare-feu sont effectives, les utilisateurs peuvent effectuer des opérations du [plan de données](security-features.md#privileged-access) de Key Vault seulement quand leurs demandes proviennent de réseaux virtuels ou de plages d’adresses IPv4 autorisés. Ceci s’applique également à l’accès au coffre de clés à partir du portail Azure. Si des utilisateurs peuvent accéder à un coffre de clés à partir du portail Azure, il ne peuvent pas lister les clés/secrets/certificats si leur ordinateur client ne figure pas dans la liste autorisée. Ceci affecte également l’accès au sélecteur de compte Key Vault par d’autres services Azure. Des utilisateurs qui peuvent voir la liste des coffres de clés ne peuvent pas lister les clés si des règles de pare-feu bloquent leur ordinateur client.

> [!NOTE]
> Notez les limitations de configuration suivantes :
> * Un maximum de 200 règles de réseau virtuel et de 1 000 règles IPv4 sont autorisées. 
> * Les règles de réseau IP sont autorisées uniquement pour les adresses IP publiques. Les plages d’adresses IP réservées aux réseaux privés (comme défini dans RFC 1918) ne sont pas autorisées dans les règles IP. Les réseaux privés incluent des adresses qui commencent par **10.** , **172.16-31.** et **192.168.** . 
> * Seules les adresses IPv4 sont prises en charge pour le moment.

## <a name="references"></a>References
* Informations de référence sur les modèles ARM : [Informations de référence sur les modèles ARM pour Azure Key Vault](/azure/templates/Microsoft.KeyVault/vaults)
* Commandes Azure CLI : [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Applets de commande Azure PowerShell : [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Étapes suivantes

* [Points de terminaison de service de réseau virtuel pour Azure Key Vault](overview-vnet-service-endpoints.md)
* [Présentation de la sécurité Azure Key Vault](security-features.md)