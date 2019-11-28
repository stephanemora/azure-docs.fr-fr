---
title: Créer une zone et un enregistrement DNS pour un nom de domaine – Azure CLI – Azure DNS
description: Cet exemple de script Azure CLI montre comment créer une zone et un enregistrement DNS pour un nom de domaine.
services: dns
author: asudbring
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: 8f5dde0a35f31d2c33ab2857659efc88339c2ead
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210394"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Exemple de script Azure CLI : Créer une zone et un enregistrement DNS

Cet exemple de script Azure CLI permet de créer une zone et un enregistrement DNS pour un nom de domaine. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la zone DNS et toutes les ressources associées.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Crée une zone Azure DNS. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Ajoute un enregistrement *A* à une zone DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Répertorie tous les jeux d’enregistrements *A* dans une zone DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

