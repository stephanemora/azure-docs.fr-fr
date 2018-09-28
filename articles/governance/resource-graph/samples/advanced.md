---
title: Requêtes Azure Resource Graph avancées
description: Utilisez Azure Resource Graph pour exécuter des requêtes avancées.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6798ef3f81ff8e733886fccc56f6474afc2ee5e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992488"
---
# <a name="advanced-resource-graph-queries"></a>Requêtes Resource Graph avancées

Pour comprendre comment fonctionne les requêtes dans Azure Resource Graph, vous devez au préalable vous familiariser avec le [langage de requête](../concepts/query-language.md). Si vous ne connaissez pas bien KQL (Kusto Query Language), nous vous recommandons de passer en revue les notions de base de ce langage pour apprendre à composer des requêtes pour les ressources qui vous intéressent.

Nous allons vous guider tout au long des requêtes avancées suivantes :

> [!div class="checklist"]
> - [Trouver les groupes de machines virtuelles identiques sans chiffrement de disque ou sur lesquels le chiffrement n’est pas activé](#vmss-not-encrypted)
> - [Lister tous les noms d’étiquette](#list-all-tags)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="language-support"></a>Prise en charge des langages

Azure CLI (par le biais d’une extension) et Azure PowerShell (par le biais d’un module) prennent en charge Azure Resource Graph. Avant d’exécuter les requêtes suivantes, vérifiez que votre environnement est prêt. Consultez [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) et [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pour savoir comment installer et valider l’environnement de votre interpréteur de commandes.

## <a name="vmss-not-encrypted"></a>Trouver les groupes de machines virtuelles identiques sans chiffrement de disque ou sur lesquels le chiffrement n’est pas activé

Cette requête recherche les ressources de groupe de machines virtuelles identiques, développe les détails des extensions, examine les propriétés **Type** et **EncryptionOperation** des extensions, effectue une synthèse et retourne les ressources où le nombre de configurations attendues est 0.

```Query
where type =~ 'microsoft.compute/virtualmachinescalesets'
| project id, extension = properties.virtualMachineProfile.extensionProfile.extensions
| where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"'
| project id
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
```

## <a name="list-all-tags"></a>Lister tous les noms d’étiquette

Cette requête commence par l’étiquette et génère un objet JSON listant tous les noms d’étiquette uniques et leurs types correspondants.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples de [requêtes de démarrage](starter.md)
- En savoir plus sur le [langage de requête](../concepts/query-language.md)
- Apprendre à [explorer les ressources](../concepts/explore-resources.md)