---
title: Meilleures pratiques en matière de sécurité et de conformité par lots
description: Découvrez les bonnes pratiques et des conseils utiles pour l’amélioration de la sécurité avec vos solutions Azure Batch.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98723810"
---
# <a name="batch-security-and-compliance-best-practices"></a>Meilleures pratiques en matière de sécurité et de conformité par lots

Cet article fournit des conseils et des meilleures pratiques pour améliorer la sécurité lors de l’utilisation de Azure Batch.

Par défaut, les comptes Azure Batch possèdent un point de terminaison public et sont accessibles publiquement. Lorsqu’un pool Azure Batch est créé, le pool est configuré dans un sous-réseau spécifié d’un réseau virtuel Azure. Les machines virtuelles du pool Batch sont accessibles par le biais d’adresses IP publiques créées par Batch. Les nœuds de calcul d’un pool peuvent communiquer entre eux si nécessaire, par exemple pour exécuter des tâches multi-instances, mais les nœuds d’un pool ne peuvent pas communiquer avec les ordinateurs virtuels en dehors du pool.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagramme montrant un environnement de traitement par lots classique.":::

De nombreuses fonctionnalités sont disponibles pour vous aider à créer un déploiement de Azure Batch plus sécurisé. Pour restreindre l’accès à des nœuds et réduire la détectabilité des nœuds à partir d’Internet, en [configurant le pool sans adresse IP publiques](batch-pool-no-public-ip-address.md). Les nœuds de calcul peuvent communiquer en toute sécurité avec d’autres machines virtuelles ou avec un réseau local en [configurant le pool dans un sous-réseau d’un réseau virtuel Azure](batch-virtual-network.md). Vous pouvez également activer [l’accès privé à partir de réseaux virtuels](private-connectivity.md) à partir d’un service alimenté par Azure Private Link.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagramme montrant un environnement de traitement par lots plus sécurisé.":::

## <a name="general-security-related-best-practices"></a>Meilleures pratiques relatives à la sécurité générale

### <a name="pool-configuration"></a>Configuration du pool

De nombreuses fonctionnalités de sécurité sont uniquement disponibles pour les pools configurés à l’aide de [Configuration de machines virtuelles](nodes-and-pools.md#configurations) et non pour les pools avec la configuration des services cloud. Nous vous recommandons d’utiliser des pools de configuration de machines virtuelles qui utilisent des [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/overview.md), dans la mesure du possible.

### <a name="batch-account-authentication"></a>Authentification de compte Batch

L’accès au compte Batch prend en charge deux méthodes d’authentification : Clé partagée et [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Nous vous recommandons vivement d’utiliser Azure AD pour l’authentification par compte Batch. Certaines fonctionnalités Batch nécessitent cette méthode d’authentification, y compris un grand nombre de fonctionnalités liées à la sécurité présentées ici.

### <a name="batch-account-pool-allocation-mode"></a>Mode d’allocation de pool de compte Batch

Lorsque vous créez un compte Batch, vous pouvez choisir entre deux [modes de répartition de pool](accounts.md#batch-accounts) :

- **Service Batch** : L’option par défaut, dans laquelle les ressources du service cloud sous-jacent ou du groupe de machines virtuelles identiques utilisées pour allouer et gérer des nœuds de pool sont créées dans des abonnements internes et ne sont pas directement visibles dans le Portail Azure. Seuls les pools et les nœuds batch sont visibles. 
- **Abonnement utilisateur** : Les ressources du service cloud sous-jacent ou du groupe de machines virtuelles identiques sont créées dans le même abonnement que le compte Batch. Ces ressources sont donc visibles dans l’abonnement, en plus des ressources Batch correspondantes.

Avec le mode d’abonnement utilisateur, des machines virtuelles Batch et autres ressources sont créées directement dans l’abonnement lors de la création d’un pool. Le mode abonnement utilisateur est requis si vous souhaitez créer des pools Batch à l’aide de Azure Reserved VM Instances, utiliser Azure Policy sur des ressources de groupe de machines virtuelles identiques et/ou gérer le quota principal de l’abonnement (partagé par tous les comptes Batch de l’abonnement). Pour créer un compte Batch dans le mode abonnement utilisateur, vous devez également inscrire votre abonnement auprès d’Azure Batch et associer le compte avec une Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Restreindre l’accès au point de terminaison public

### <a name="batch-network-endpoints"></a>Points de terminaison de réseau Batch

N’oubliez pas que, par défaut, les points de terminaison avec des adresses IP publiques sont utilisés pour communiquer avec les comptes Batch, les pools Batch et les nœuds de pool.

### <a name="batch-account-api"></a>API du compte Batch

 Lorsqu’un compte Batch est créé, un point de terminaison public est créé et utilisé pour appeler la plupart des opérations du compte à l’aide d’une [API REST](/rest/api/batchservice/). Le point de terminaison du compte a une URL de base à l’aide du format `https://{account-name}.{region-id}.batch.azure.com`. L’accès au compte Batch est sécurisé, avec la communication vers le point de terminaison de compte chiffré à l’aide du protocole HTTPS et chaque requête authentifiée à l’aide de l’authentification par clé partagée ou Azure Active Directory (Azure AD).

### <a name="azure-resource-manager"></a>Azure Resource Manager

En plus des opérations spécifiques à un compte Batch, les [opérations de gestion](/rest/api/batchmanagement/) s’appliquent à des comptes Batch uniques et multiples. Ces opérations de gestion sont accessibles via Azure Resource Manager.

Les opérations de gestion par lots via Azure Resource Manager sont chiffrées à l’aide du protocole HTTPS et chaque requête est authentifiée à l’aide de l’authentification Azure AD.

### <a name="batch-pool-nodes"></a>Nœuds de pool Batch

Le service Batch communique avec un agent de nœud Batch qui s’exécute sur chaque nœud du pool. Par exemple, le service demande à l’agent de nœud d’exécuter une tâche, d’arrêter une tâche ou d’obtenir les fichiers d’une tâche. La communication vers l’agent de nœud est activée par un ou plusieurs équilibreurs de charge, dont le nombre dépend du nombre de nœuds dans un pool. L’équilibreur de charge transmet la communication vers le nœud souhaité, chaque nœud étant adressé par un numéro de port unique. Par défaut, les équilibreurs de charge ont des adresses IP publiques qui leur sont associées. Vous pouvez également accéder à distance aux nœuds de pool via RDP ou SSH (cet accès est activé par défaut, avec communication via les équilibreurs de charge).

### <a name="restricting-access-to-batch-endpoints"></a>Restriction de l’accès aux points de terminaison Batch 

Plusieurs fonctionnalités sont disponibles pour limiter l’accès aux différents points de terminaison Batch, en particulier lorsque la solution utilise un réseau virtuel. 

#### <a name="use-private-endpoints"></a>Utiliser des points de terminaison privés

[Azure Private Link](../private-link/private-link-overview.md) permet d’accéder aux services Azure PaaS ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé dans votre réseau virtuel. Vous pouvez utiliser Private Link pour limiter l’accès des utilisateurs à un compte Batch à partir du réseau virtuel ou de tout réseau virtuel appairé. Les ressources mappées à Private Link sont également accessibles localement via un Peering privé par le biais d’un VPN ou d’Azure ExpressRoute.

Pour utiliser des points de terminaison privés, un compte Batch doit être configuré de manière appropriée lors de sa création ; la configuration de l’accès au réseau public doit être désactivée. Une fois créés, les points de terminaison privés peuvent être créés et associés au compte Batch. Pour plus d’informations, consultez [Utiliser des points de terminaison privés avec des comptes Azure Batch](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Créer des pools dans des réseaux virtuels

Les nœuds de calcul d’un pool Batch peuvent communiquer entre eux, par exemple pour exécuter des tâches multi-instances, sans nécessiter de réseau virtuel (VNET). Toutefois, par défaut, les nœuds d’un pool ne peuvent pas communiquer avec les machines virtuelles qui se trouvent en dehors du pool sur un réseau virtuel et ont des adresses IP privées, telles que les serveurs de licences ou les serveurs de fichiers.

Pour autoriser les nœuds de calcul à communiquer de façon sécurisée avec d’autres machines virtuelles ou avec un réseau local, vous pouvez configurer un pool en tant que sous-réseau d’un réseau virtuel Azure.

Lorsque les pools ont des points de terminaison d’adresses IP publiques, le sous-réseau doit autoriser des communications entrantes issues du service Batch pour pouvoir planifier des tâches et exécuter d’autres opérations sur les nœuds de calcul et également des communications sortantes pour communiquer avec le stockage Azure ou d’autres ressources en fonction des besoins de votre charge de travail. Pour les pools dans la configuration de machines virtuelles, Batch ajoute des groupes de sécurité réseau (NSG) au niveau de l’interface réseau attaché aux nœuds de calcul. Ces groupes de sécurité réseau ont des règles pour activer :

- le trafic TCP entrant à partir des adresses IP du service Batch
- le trafic TCP entrant pour l’accès à distance
- le trafic sortant sur n’importe quel port vers le réseau virtuel (peut être modifié par les règles NSG au niveau du sous-réseau)
- le trafic sortant sur n’importe quel port vers l’Internet (peut être modifié par les règles NSG au niveau du sous-réseau)

Vous n’avez pas besoin de spécifier des groupes de sécurité réseau au niveau du sous-réseau du réseau virtuel, car Batch configure ses propres groupes de sécurité réseau. Si vous avez un groupe de sécurité réseau associé au sous-réseau dans lequel les nœuds de calcul Batch sont déployés ou si vous souhaitez appliquer des règles de groupe de sécurité réseau personnalisées pour remplacer celles appliquées par défaut, vous devez configurer ce groupe de sécurité réseau avec au moins les règles de sécurité entrantes et sortantes afin d’autoriser la communication vers le service Batch aux nœuds de pool et la communication vers le nœud de pool au Stockage Azure.

Pour plus d’informations, consultez [Créer un pool Azure Batch dans un réseau virtuel](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Créer des pools avec une adresse IP publique statique

Par défaut, les adresses IP publiques associées aux pools sont dynamiques. Elles sont créées lors de la création d’un pool et des adresses IP peuvent être ajoutées ou supprimées lorsqu’un pool est redimensionné. Lorsque les applications de tâche s’exécutant sur des nœuds de pool doivent accéder à des services externes, l’accès à ces services peut nécessiter une restriction à des adresses IP spécifiques.  Dans ce cas, les adresses IP dynamiques ne seront pas gérées.

Vous pouvez créer des ressources d’adresses IP publiques statiques dans le même abonnement que le compte Batch avant la création du pool. Vous pouvez ensuite spécifier ces adresses lors de la création de votre pool.

Pour plus d’informations, consultez [Créer un pool Azure Batch avec des adresses IP publiques spécifiées](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Créer des pools sans adresse IP publique

Par défaut, une ou plusieurs adresses IP publiques sont attribuées à tous les nœuds de calcul dans un pool de configuration de machines virtuelles Azure Batch. Ces points de terminaison sont utilisés par le service Batch pour planifier des tâches et pour communiquer avec les nœuds de calcul, notamment dans le cadre de l’accès sortant à Internet.

Pour restreindre l’accès à ces nœuds et réduire la détectabilité de ces nœuds à partir d’Internet, vous pouvez provisionner le pool sans adresses IP publiques.

Pour plus d’informations, consultez [Créer un pool sans adresse IP publique](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Limiter l’accès à distance aux nœuds de pool

Par défaut, Batch autorise un utilisateur du nœud disposant d’une connectivité réseau à se connecter en externe à un nœud de calcul dans un pool Batch en utilisant RDP ou SSH.

Pour limiter l’accès à distance aux nœuds, utilisez l’une des méthodes suivantes :

- Configurez le [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) pour refuser l’accès. Le groupe de sécurité réseau approprié (NSG) approprié sera associé au pool.
- Créez votre pool [sans adresse IP publique](batch-pool-no-public-ip-address.md). Par défaut, ces pools ne sont pas accessibles en dehors du réseau virtuel.
- Associez un groupe de sécurité réseau au réseau virtuel pour refuser l’accès aux ports RDP ou SSH.
- Ne créez aucun utilisateur sur le nœud. Sans aucun utilisateur de nœud, l’accès à distance n’est pas possible.

## <a name="encrypt-data"></a>Chiffrer les données

### <a name="encrypt-data-in-transit"></a>Chiffrer les données en transit

Toutes les communications vers le point de terminaison du compte Batch (ou via Azure Resource Manager) doivent utiliser le protocole HTTPS. Vous devez utiliser `https://` dans les URL de compte Batch spécifiées dans les API lors de la connexion au service Batch.

Les clients qui communiquent avec le service Batch doivent être configurés pour utiliser le protocole TLS (Transport Layer Security) 1.2.

### <a name="encrypt-batch-data-at-rest"></a>Chiffrer Batch des données au repos

Certaines des informations spécifiées dans les API Batch, telles que les certificats de compte, les métadonnées de tâche et de travail et les lignes de commande de tâche, sont automatiquement chiffrées lorsqu’elles sont stockées par le service Batch. Par défaut, ces données sont chiffrées à l’aide des clés gérées par la plateforme Azure Batch propres à chaque compte Batch.

Vous pouvez également chiffrer ces données avec des [clés gérées par le client](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) est utilisé pour générer et stocker la clé, avec l’identificateur de clé inscrit avec votre compte Batch.

### <a name="encrypt-compute-node-disks"></a>Chiffrer des disques de nœud de calcul

Les nœuds de calcul Batch ont deux disques par défaut : un disque de système d’exploitation et le disque SSD temporaire local. [Les fichiers et répertoires](files-and-directories.md) gérés par Batch se trouvent sur le disque SSD temporaire, qui est l’emplacement par défaut des fichiers tels que les fichiers de sortie des tâches. Les applications de tâche Batch peuvent utiliser l’emplacement par défaut sur le disque SSD ou le disque du système d’exploitation.

Pour une sécurité accrue, chiffrez ces disques à l’aide de l’une de ces fonctionnalités de chiffrement de disque Azure :

- [Chiffrement de disque managé au repos avec des clés gérées par la plateforme](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Chiffrement sur l’hôte à l’aide d’une clé gérée par la plateforme](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Services d’accès sécurisé à partir de nœuds de calcul

Les nœuds Batch peuvent [accéder en toute sécurité aux informations d’identification et aux secrets](credential-access-key-vault.md) stockés dans [Azure Key Vault](../key-vault/general/overview.md), qui peuvent être utilisés par les applications de tâche pour accéder à d’autres services. Un certificat est utilisé pour accorder l’accès aux nœuds du pool à Key Vault.

## <a name="governance-and-compliance"></a>Gouvernance et conformité

### <a name="compliance"></a>Compatibilité

Pour aider les clients à répondre à leurs propres obligations de conformité dans les secteurs et marchés réglementés dans le monde entier, Azure gère un [large éventail d’offres de conformité](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Ces offres sont basées sur divers types de garanties, notamment des certifications, attestations, validations, autorisations et évaluations officielles créées par des sociétés d’audit tierces indépendantes, ainsi que des modifications contractuelles, des auto-évaluations et des documents d’aide pour les clients créés par Microsoft. Consultez la [vue d’ensemble complète des offres de conformité](https://aka.ms/AzureCompliance) pour déterminer celles qui peuvent être pertinentes pour vos solutions Batch.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) aide à appliquer les normes organisationnelles et à évaluer la conformité à grande échelle. Les cas d’usage courants pour Azure Policy incluent la mise en œuvre de la gouvernance pour la cohérence des ressources, la conformité réglementaire, la sécurité, le coût et la gestion.

Selon le mode d’allocation de pools et les ressources auxquelles une stratégie doit s’appliquer, utilisez Azure Policy avec Batch de l’une des manières suivantes :

- Directement, à l’aide de la ressource Microsoft.Batch/batchAccounts. Vous pouvez utiliser un sous-ensemble des propriétés d’un compte Batch. Par exemple, votre stratégie peut inclure des régions de compte Batch valides, le mode d’allocation de pools autorisé et si un réseau public est activé pour les comptes.
- Indirectement, à l’aide de la ressource Microsoft.Compute/virtualMachineScaleSets. Les comptes Batch avec le mode d’allocation de pools d’abonnements utilisateur peuvent avoir une stratégie définie selon les ressources du groupe de machines virtuelles identiques, créées dans l’abonnement au compte Batch. Par exemple, les tailles de machine virtuelle autorisées et la garantie que certaines extensions sont exécutées sur chaque nœud de pool.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [ligne de base de sécurité Azure pour Batch](security-baseline.md).
- En savoir plus sur les [meilleures pratiques pour Azure Batch](best-practices.md).