---
title: Utiliser un serveur DNS personnalisé
titleSuffix: Azure Machine Learning
description: Comment configurer un serveur DNS personnalisé pour qu’il fonctionne avec un espace de travail Azure Machine Learning et un point de terminaison privé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 04/01/2021
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.openlocfilehash: 2a55fedd0fe059e7bff8203924389956dce36f3b
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889805"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Utilisation de votre espace de travail avec un serveur DNS personnalisé

Lorsque vous utilisez un espace de travail Azure Machine Learning avec un point de terminaison privé, il existe [plusieurs manières de gérer la résolution de noms DNS](../private-link/private-endpoint-dns.md). Par défaut, Azure gère automatiquement la résolution de noms pour votre espace de travail et votre point de terminaison privé. Si vous __utilisez votre propre serveur DNS personnalisé__, vous devez créer manuellement des entrées DNS ou utiliser des redirecteurs conditionnels pour l’espace de travail.

> [!IMPORTANT]
> Cet article ne traite que de la recherche du nom de domaine complet (FQDN) et des adresses IP pour ces entrées. il ne fournit pas d’informations sur la configuration des enregistrements DNS pour ces éléments. Pour savoir comment ajouter des enregistrements, consultez la documentation de votre logiciel DNS.

## <a name="prerequisites"></a>Prérequis

- Réseau virtuel Azure qui utilise [votre propre serveur DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Un espace de travail Azure Machine Learning avec un point de terminaison privé. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

- Connaissances sur l’utilisation de l’[isolement réseau pendant la formation et l’inférence](./how-to-network-security-overview.md).

- Connaissances sur la [configuration de la zone DNS d’un point de terminaison privé Azure](../private-link/private-endpoint-dns.md)

- Éventuellement, [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="public-regions"></a>Régions publiques

La liste suivante contient les noms de domaine complets (FQDN) utilisés par votre espace de travail s’il se trouve dans une région publique :

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > Le nom de l’espace de travail pour ce nom de domaine complet peut être tronqué. La troncature est faite pour maintenir `ml-<workspace-name, truncated>-<region>-<workspace-guid>` sous 63 caractères.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * Les instances de calcul sont accessibles uniquement à partir du réseau virtuel.
    > * L’adresse IP de ce nom de domaine complet n’est **pas** l’adresse IP de l’instance de calcul. Utilisez plutôt l’adresse IP privée du point de terminaison privé de l’espace de travail (l’adresse IP des entrées `*.api.azureml.ms`).

## <a name="azure-china-21vianet-regions"></a>Régions cloud Azure China 21Vianet

Les noms de domaine complets suivants sont destinés aux régions Azure China 21Vianet :

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > Le nom de l’espace de travail pour ce nom de domaine complet peut être tronqué. La troncature est faite pour maintenir `ml-<workspace-name, truncated>-<region>-<workspace-guid>` sous 63 caractères.
* `<instance-name>.<region>.instances.ml.azure.cn`
## <a name="find-the-ip-addresses"></a>Rechercher les adresses IP

Pour rechercher les adresses IP internes des noms de domaine complets dans le réseau virtuel, utilisez l’une des méthodes suivantes :

> [!NOTE]
> Les noms de domaine complets et les adresses IP seront différents en fonction de votre configuration. Par exemple, la valeur GUID dans le nom de domaine sera spécifique à votre espace de travail.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre __espace de travail__ Azure Machine Learning.
1. Dans la section __Paramètres__, sélectionnez __Connexions des points de terminaison privés__.
1. Sélectionnez le lien dans la colonne __Point de terminaison privé__ qui s’affiche.
1. La liste des noms de domaine complets (FQDN) et les adresses IP du point de terminaison privé de l’espace de travail se trouvent au bas de la page.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Liste des noms de domaine complets dans le portail":::

---

Les informations retournées par toutes les méthodes sont les mêmes : une liste du nom de domaine complet et de l’adresse IP privée pour les ressources. L’exemple suivant provient d’une région Azure globale :

| FQDN | Adresse IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Certains noms de domaine complets ne sont pas affichés dans la liste par le point de terminaison privé, mais ils sont requis par l’espace de travail dans les régions eastus, southcentralus et westus2. Ces noms de domaine complets sont répertoriés dans le tableau suivant et doivent également être ajoutés à votre serveur DNS et/ou à une zone de DNS privé Azure :
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Si vous disposez d’une instance de calcul, utilisez `<instance-name>.<region>.instances.azureml.ms`, où `<instance-name>` est le nom de votre instance de calcul. Utilisez l’adresse IP privée du point de terminaison privé de l’espace de travail. L’instance de calcul est accessible uniquement à partir du réseau virtuel.
>
> Pour toutes ces adresses IP, utilisez la même adresse que les entrées `*.api.azureml.ms` retournées par les étapes précédentes.

Le tableau suivant montre des exemples d’adresses IP à partir de régions Azure China 21Vianet :

| FQDN | Adresse IP |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure Machine Learning avec un réseau virtuel, consultez [Présentation du réseau virtuel](how-to-network-security-overview.md).

Pour plus d’informations sur l’intégration des points de terminaison privés dans votre configuration DNS, consultez [Configuration DNS du point de terminaison privé Azure](../private-link/private-endpoint-dns.md).
