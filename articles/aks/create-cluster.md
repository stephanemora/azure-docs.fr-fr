---
title: Créer un cluster Azure Kubernetes Service (AKS)
description: Créez un cluster AKS avec l’interface CLI ou le portail Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 304f3807a70179e4aab2ede80dc08a1aa85a2e51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098904"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Créer un cluster Azure Kubernetes Service (AKS)

Un cluster Azure Kubernetes Service (AKS) peut être créé avec l’interface Azure CLI ou le portail Azure.

## <a name="azure-cli"></a>Azure CLI

Utilisez la commande [az aks create][az-aks-create] pour créer le cluster AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Les options suivantes sont disponibles avec la commande `az aks create`. Pour plus d’informations sur chacun de ces arguments, consultez la [documentation de référence sur Azure CLI][az-aks-create] pour AKS.

| Argument | Description | Obligatoire |
|---|---|:---:|
| `--name``-n` | Nom de ressource pour le cluster managé. | Oui |
| `--resource-group``-g` | Nom du groupe de ressources Azure Kubernetes Service. | Oui |
| `--admin-username``-u` | Nom d’utilisateur pour les machines virtuelles Linux.  Par défaut : azureuser. | no |
| `--aad-client-app-id` | (PRÉVERSION) ID d’une application cliente Azure Active Directory de type « Native ». | no |
| `--aad-server-app-id` | (PRÉVERSION) ID d’une application serveur Azure Active Directory de type « Application/API web ». | no |
| `--aad-server-app-secret` | (PRÉVERSION) Secret d’une application serveur Azure Active Directory. | no |
| `--aad-tenant-id` | (PRÉVERSION) ID d’un locataire Azure Active Directory. | no |
| `--admin-username``-u` | Compte d’utilisateur à créer sur les machines virtuelles de nœud pour l’accès SSH.  Par défaut : azureuser. | no |
| ` --client-secret` | Secret associé au principal du service. | no |
| `--dns-name-prefix``-p` | Préfixe DNS pour l’adresse IP publique des clusters. | no |
| `--dns-service-ip` | Adresse IP affectée au service DNS Kubernetes. | no |
| `--docker-bridge-address` | Adresse IP et masque réseau assignés au pont Docker. | no |
| `--enable-addons``-a` | Active les composants Kubernetes figurant dans une liste séparée par des virgules. | no |
| `--enable-rbac``-r` | Active le contrôle d’accès en fonction du rôle Kubernetes. | no |
| `--generate-ssh-keys` | Génère les fichiers de clés SSH publiques et privées s’ils sont manquants. | no |
| `--kubernetes-version``-k` | Version de Kubernetes à utiliser pour la création du cluster, telle que « 1.7.9 » ou « 1.9.6 ». | no |
| `--locaton``-l` | Emplacement du groupe de ressources créé automatiquement. | no |
| `--max-pods``-m` | Nombre maximal de pods pouvant être déployés sur un nœud. | no |
| `--network-plugin` | Plug-in réseau Kubernetes à utiliser. | no |
| `--no-ssh-key``-x` | Ne créez pas et n’utilisez de clé SSH locale. | no |
| `--no-wait` | Ne pas attendre la fin de l’opération de longue durée. | no |
| `--node-count``-c` | Nombre de nœuds pour les pools de nœud par défaut.  Par défaut : 3. | no |
| `--node-osdisk-size` | Taille osDisk en Go du pool de nœud Machine virtuelle. | no |
| `--node-vm-size``-s` | Taille de la machine virtuelle.  Par défaut : Standard_D1_v2. | no |
| `--pod-cidr` | Plage d’adresse IP de notation CIDR à partir de laquelle attribuer les adresses IP de pod lorsque Kubernetes est utilisé. | no |
| `--service-cidr` | Plage d’adresses IP de notation CIDR à partir de laquelle attribuer des adresses IP de cluster de service. | no |
| `--service-principal` | Principal de service utilisé pour l’authentification du cluster. | no |
| `--ssh-key-value` | Valeur de fichier de clé ou chemin de fichier de clé SSH.  Par défaut : ~/.ssh/id_rsa.pub. | no |
| `--tags` | Balises séparées par des espaces au format 'key [= value]'. Utilisez '' pour effacer des balises existantes. | no |
| `--vnet-subnet-id` | ID d’un sous-réseau d’un réseau virtuel existant dans lequel déployer le cluster. | no |
| `--workspace-resource-id` | ID de ressource d’un espace de travail Log Analytics existant à utiliser pour stocker les données de surveillance. | no |

## <a name="azure-portal"></a>Portail Azure

Pour obtenir des instructions sur le déploiement d’un cluster AKS avec le portail Azure, consultez le [guide de démarrage rapide du portail Azure][aks-portal-quickstart] pour Azure Kubernetes Service (AKS).

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
