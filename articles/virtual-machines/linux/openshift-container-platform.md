---
title: Déployer OpenShift Container Platform dans Azure | Microsoft Docs
description: Déployez OpenShift Container Platform dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127002"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Déployer OpenShift Container Platform dans Azure

Vous pouvez utiliser plusieurs méthodes pour déployer OpenShift Container Platform dans Azure :

- Vous pouvez déployer manuellement les composants d’infrastructure Azure nécessaires, puis suivre la [documentation OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Vous pouvez également utiliser un [modèle Resource Manager](https://github.com/Microsoft/openshift-container-platform/) existant qui simplifie le déploiement du cluster OpenShift Container Platform.
- Une autre option consiste à utiliser l’[offre de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pour toutes les options, vous devez avoir un abonnement Red Hat. Pendant le déploiement, l’instance Red Hat Enterprise Linux est inscrite dans l’abonnement Red Hat et associée à l’ID du pool contenant les droits pour OpenShift Container Platform.
Vérifiez que vous avez un nom d’utilisateur, un mot de passe et un ID de pool RHSM (Red Hat Subscription Manager) valides. Vous pouvez utiliser une clé d’activation, un ID d’organisation et un ID de pool. Vous pouvez vérifier ces informations en vous connectant au site https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Déployer à l’aide du modèle Resource Manager d’OpenShift Container Platform

### <a name="private-clusters"></a>Clusters privés

Déploiement de clusters de OpenShift privés nécessite plus de simplement ne pas avoir une adresse IP publique, associée à l’équilibreur de charge master (console web) ou à l’infrastructure équilibreur (routeur) de charge.  En règle générale, un cluster privé utilise un serveur DNS personnalisé (et pas de la valeur par défaut Azure DNS), un nom de domaine personnalisé (par exemple, contoso.com) et prédéfinis ou des réseaux virtuels.  Pour les clusters privés, vous devez configurer votre réseau virtuel avec tous les sous-réseaux appropriés et les paramètres du serveur DNS à l’avance.  Utilisez ensuite **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, et  **existingNodeSubnetReference** pour spécifier le sous-réseau existant pour une utilisation par le cluster.

Si master privé est sélectionné (**masterClusterType**= privé), une adresse IP privée statique doit être spécifié pour **masterPrivateClusterIp**.  Cette adresse IP sera attribuée au composant frontal de l’équilibreur de charge principal.  L’adresse IP doit être dans la CIDR pour le sous-réseau principal et pas en cours d’utilisation.  **masterClusterDnsType** doit être définie sur « custom » et le maître de nom DNS doit être fourni pour **masterClusterDns**.  Le nom DNS doit correspondre à l’IP privée statique et servira à accéder à la console sur les nœuds principaux.

Si le routeur privé est sélectionné (**routerClusterType**= privé), une adresse IP privée statique doit être spécifié pour **routerPrivateClusterIp**.  Cette adresse IP est affectée à la partie frontale de l’équilibreur de charge infrarouge.  L’adresse IP doit se trouver dans la CIDR pour l’infrastructure sous-réseau et non utilisé.  **routingSubDomainType** doit avoir la valeur « custom » et le nom DNS générique de routage doit être assurée par le **routingSubDomain**.  

Si maîtres privés et routeur privé sont sélectionnés, le nom de domaine personnalisé doit également être entré pour **domainName**

Après un déploiement réussi, le nœud Bastion est le seul nœud avec une adresse IP publique que vous pouvez ssh dans.  Même si les nœuds principaux sont configurés pour l’accès public, ils ne sont pas exposés pour ssh accès.

Pour effectuer le déploiement à l’aide du modèle Resource Manager, vous utilisez un fichier de paramètres qui fournit les paramètres d’entrée. Pour personnaliser davantage le déploiement, dupliquez (fork) le dépôt GitHub et changez les éléments appropriés.

Voici une liste non exhaustive d’options de personnalisation courantes :

- Taille de machine virtuelle bastion (variable dans azuredeploy.json)
- Conventions de nommage (variables dans azuredeploy.json)
- Caractéristiques de cluster OpenShift, modifiées dans le fichier hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurer le fichier de paramètres

Le [modèle OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) a plusieurs branches disponibles pour différentes versions d’OpenShift Container Platform.  Selon vos besoins, vous pouvez effectuer le déploiement directement à partir du dépôt, ou dupliquer (fork) le dépôt et personnaliser les modèles ou les scripts avant le déploiement.

Utilisez la valeur de `appId` du principal de service créé précédemment pour le paramètre `aadClientId`.

L’exemple suivant montre un fichier de paramètres nommé azuredeploy.parameters.json avec toutes les entrées obligatoires.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Remplacez les paramètres par vos informations spécifiques.

Les paramètres peuvent différer selon les versions. Vérifiez quels sont les paramètres nécessaires pour la branche que vous utilisez.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Expliqué du fichier Parameters.JSON

| Propriété | Description | Options valides | Valeur par défaut |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL pour les artefacts (json, scripts, etc.). |  |  https :\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Région Azure pour déployer des ressources pour |  |  |
| `masterVmSize` | Taille de la machine virtuelle principale. Sélectionnez une des tailles de machine virtuelle autorisées répertoriés dans le fichier azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Taille de l’infrastructure machine virtuelle. Sélectionnez une des tailles de machine virtuelle autorisées répertoriés dans le fichier azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Taille de machine virtuelle du nœud d’application. Sélectionnez une des tailles de machine virtuelle autorisées répertoriés dans le fichier azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Taille de la machine virtuelle du nœud de stockage natif (CNS) de conteneur. Sélectionnez une des tailles de machine virtuelle autorisées répertoriés dans le fichier azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | L’image RHEL à utiliser. defaultgallery : À la demande ; place de marché : image tiers | defaultgallery <br> place de marché | defaultgallery |
| `marketplaceOsImage` | Si `osImageType` est la place de marché, puis entrez les valeurs appropriées pour les « publisher » « offre », « sku », « version » de l’offre de la place de marché. Ce paramètre est un type d’objet |  |  |
| `storageKind` | Le type de stockage à utiliser  | géré<br> non managé | géré |
| `openshiftClusterPrefix` | Préfixe utilisé pour configurer les noms d’hôtes pour tous les nœuds du cluster.  Entre 1 et 20 caractères |  | MyCluster |
| `minoVersion` | La version mineure de OpenShift Container Platform 3.11 à déployer |  | 69 |
| `masterInstanceCount` | Nombre de nœuds maîtres à déployer | 1, 3, 5 | 3 |
| `infraInstanceCount` | Nombre d’infrastructure nœuds à déployer | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Nombre de nœuds à déployer | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Nombre de nœuds de noms communs à déployer | 3, 4 | 3 |
| `osDiskSize` | Taille du disque du système d’exploitation pour la machine virtuelle (en Go) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Taille du disque de données à attacher aux nœuds de volume Docker (en Go) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Taille du disque de données à attacher à des nœuds de noms communs pour une utilisation en glusterfs (en Go | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nom d’utilisateur administrateur pour la connexion de système d’exploitation (VM) et OpenShift initial d’utilisateurs |  | ocpadmin |
| `enableMetrics` | Activer les métriques. Mesures nécessitent que davantage de ressources sélectionnez donc une taille appropriée pour l’infrastructure de machine virtuelle | true <br> false | false |
| `enableLogging` | Activer la journalisation. elasticsearch pod nécessite de 8 Go de RAM sélectionnez donc une taille appropriée pour l’infrastructure de machine virtuelle | true <br> false | false |
| `enableCNS` | Activer le stockage natif de conteneur | true <br> false | false |
| `rhsmUsernameOrOrgId` | ID de nom d’utilisateur du Gestionnaire d’abonnements Hat ou organisation rouge |  |  |
| `rhsmPoolId` | L’ID du Pool Red Hat abonnement Manager qui contient vos droits OpenShift pour les nœuds de calcul |  |  |
| `rhsmBrokerPoolId` | Le Red Hat abonnement Manager ID du Pool qui contient des nœuds de vos droits OpenShift maîtres et infrastructure. Si vous n’avez pas autre pool d’ID, entrez le même ID du pool en tant que « rhsmPoolId » |  |
| `sshPublicKey` | Copiez votre clé publique SSH ici |  |  |
| `keyVaultSubscriptionId` | L’ID d’abonnement de l’abonnement qui contient le coffre de clés |  |  |
| `keyVaultResourceGroup` | Le nom du groupe de ressources qui contient le coffre de clés |  |  |
| `keyVaultName` | Le nom du coffre de clés que vous avez créé |  |  |
| `enableAzure` | Activer le fournisseur de Cloud Azure | true <br> false | true |
| `aadClientId` | ID Azure Active Directory Client également connu en tant qu’ID d’Application pour le Principal de Service |  |  |
| `domainName` | Nom du nom de domaine personnalisé à utiliser (le cas échéant). La valeur « none » si n’est pas le déploiement de cluster entièrement privé |  | Aucun |
| `masterClusterDnsType` | Type de domaine pour la console web de OpenShift. 'default' utilisera étiquette DNS du maître d’infrastructure IP publique. « personnalisé » vous permet de définir votre propre nom | default <br> personnalisé | default |
| `masterClusterDns` | Le nom DNS personnalisé à utiliser pour accéder à la console web de OpenShift si vous avez sélectionné 'personnalisé' pour `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Si la valeur est « nipio », `routingSubDomain` utilisera nip.io.  Utilisez 'custom' Si vous avez votre propre domaine que vous souhaitez utiliser pour le routage | nipio <br> personnalisé | nipio |
| `routingSubDomain` | Le nom DNS générique à utiliser pour le routage si vous avez sélectionné 'personnalisé' pour `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Indiquez si vous souhaitez utiliser un réseau virtuel existant ou créez un réseau virtuel | Existant <br> new | new |
| `virtualNetworkResourceGroupName` | Nom du groupe de ressources pour le nouveau réseau virtuel si vous avez sélectionné 'new' pour `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | Le nom du nouveau réseau virtuel à créer si vous avez sélectionné 'new' pour `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Préfixe d’adresse du nouveau réseau virtuel |  | 10.0.0.0/14 |
| `masterSubnetName` | Le nom du sous-réseau principal |  | mastersubnet |
| `masterSubnetPrefix` | CIDR utilisé pour le sous-réseau principal - doit être un sous-ensemble du préfixe d’adresse |  | 10.1.0.0/16 |
| `infraSubnetName` | Le nom de l’infrastructure sous-réseau |  | infrasubnet |
| `infraSubnetPrefix` | CIDR utilisé pour l’infrastructure sous-réseau - doit être un sous-ensemble du préfixe d’adresse |  | 10.2.0.0/16 |
| `nodeSubnetName` | Le nom du sous-réseau de nœud |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR utilisé pour le sous-réseau de nœud - doit être un sous-ensemble du préfixe d’adresse |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Référence complète à un sous-réseau existant pour les nœuds principaux. Ne pas nécessaire si la création du nouveau réseau virtuel / sous-réseau |  |  |
| `existingInfraSubnetReference` | Référence complète à un sous-réseau existant pour infra nœuds. Ne pas nécessaire si la création du nouveau réseau virtuel / sous-réseau |  |  |
| `existingCnsSubnetReference` | Référence complète à un sous-réseau existant pour les nœuds de noms communs. Ne pas nécessaire si la création du nouveau réseau virtuel / sous-réseau |  |  |
| `existingNodeSubnetReference` | Référence complète à un sous-réseau existant pour les nœuds de calcul. Ne pas nécessaire si la création du nouveau réseau virtuel / sous-réseau |  |  |
| `masterClusterType` | Spécifiez si le cluster utilise des nœuds principaux privés ou publics. Si vous choisissez privé, les nœuds principaux ne soient pas exposés à Internet via une adresse IP publique. Au lieu de cela, il utilisera l’adresse IP privée, spécifié dans le `masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | Si les nœuds principaux privés sont sélectionnées, une adresse IP privée doit être spécifiée pour une utilisation par l’équilibreur de charge interne pour les nœuds principaux. Cette adresse IP statique doit être dans le bloc CIDR pour le sous-réseau principal et n’est déjà en cours d’utilisation. Si les nœuds master publics sont sélectionnés, cette valeur ne sera pas utilisée mais doit toujours être spécifiée |  | 10.1.0.200 |
| `routerClusterType` | Spécifiez si le cluster utilise public ou privé infra nœuds. Si vous choisissez privé, l’infrastructure nœuds ne soient pas exposés à Internet via une adresse IP publique. Au lieu de cela, il utilisera l’adresse IP privée, spécifié dans le `routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | S’ils sont privés infra nœuds sont sélectionnés, puis une adresse IP privée est obligatoire pour utiliser par l’équilibreur de charge interne pour infra nœuds. Cette adresse IP statique doit être dans le bloc CIDR pour le sous-réseau principal et n’est déjà en cours d’utilisation. S’ils sont publics infra nœuds sont sélectionnés, cette valeur ne sera pas utilisée mais doit toujours être spécifiée |  | 10.2.0.200 |
| `routingCertType` | Utiliser un certificat personnalisé pour le domaine de routage ou le certificat auto-signé par défaut : suivez les instructions dans **certificats personnalisés** section | selfsigned <br> personnalisé | selfsigned |
| `masterCertType` | Utiliser un certificat personnalisé pour le domaine principal ou le certificat auto-signé par défaut : suivez les instructions dans **certificats personnalisés** section | selfsigned <br> personnalisé | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Déployer à l’aide d’Azure CLI

> [!NOTE] 
> La commande suivante requiert Azure CLI 2.0.8 ou version ultérieure. Pour vérifier la version d’Azure CLI, exécutez la commande `az --version`. Pour mettre à jour la version de l’interface CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

L’exemple suivant déploie le cluster OpenShift et toutes les ressources associées dans un groupe de ressources nommé openshiftrg, avec le nom de déploiement myOpenShiftCluster. Le modèle est référencé directement à partir du dépôt GitHub, et un fichier de paramètres locaux nommé azuredeploy.parameters.json est utilisé.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Le déploiement prend au moins 60 minutes, selon le nombre total de nœuds déployés et les options configurées. Le nom de domaine complet du DNS Bastion et l’URL de la console OpenShift s’affichent dans le terminal à la fin du déploiement.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Si vous ne souhaitez pas lier la ligne de commande d’attente de la fin du déploiement, ajoutez `--no-wait` aux options pour le déploiement du groupe. La sortie du déploiement peut être récupérée à partir du portail Azure, dans la section du déploiement du groupe de ressources.

## <a name="connect-to-the-openshift-cluster"></a>Se connecter au cluster OpenShift

Une fois le déploiement terminé, récupérez la connexion à partir de la section de sortie du déploiement. Se connecter à la console OpenShift dans votre navigateur à l’aide de la **URL de la Console OpenShift**. Vous pouvez également exécuter SSH à l’hôte Bastion. Dans l’exemple suivant, le nom d’utilisateur administrateur est clusteradmin et le nom de domaine complet du DNS d’adresses IP publiques Bastion est bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com :

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées quand vous n’en avez plus besoin.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Étapes suivantes

- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes de déploiement d’OpenShift dans Azure](./openshift-troubleshooting.md)
- [Prise en main d’OpenShift Container Platform](https://docs.openshift.com/container-platform)
