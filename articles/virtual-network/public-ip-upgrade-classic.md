---
title: Migrer une adresse IP réservée classique vers une adresse IP publique
titleSuffix: Azure Virtual Network
description: Dans cet article, Découvrez comment mettre à niveau une adresse IP réservée de modèle de déploiement classique vers une adresse IP publique Azure Resource Manager.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 05355db2d5227db3bf695b059df638e8bc07f4bd
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439039"
---
# <a name="migrate-a-classic-reserved-ip-address-to-a-public-ip-address"></a>Migrer une adresse IP réservée classique vers une adresse IP publique

Pour tirer parti des nouvelles fonctionnalités d’Azure Resource Manager, vous pouvez migrer les adresses IP statiques publiques existantes, adresses IP réservées, du modèle de déploiement classique vers Azure Resource Manager.  L’adresse IP publique migrée aura le type de référence SKU De base. 

Dans cet article, vous allez apprendre à mettre à niveau une adresse IP réservée classique vers une adresse IP publique de base.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Votre abonnement Azure est enregistré pour la migration. Pour en avoir plus, consultez [Migrer ver Resource Manager avec PowerShell](../virtual-machines/migration-classic-resource-manager-ps.md).
* Une adresse IP réservée de modèle de déploiement classique.
* Module de management des services Azure PowerShell installé pour PowerShell. Pour plus d’informations, consultez [Installation du module de management des services Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Interface de ligne de commande classique Azure installée pour les instructions Azure CLI. Pour plus d’informations, consultez [Installer Azure Classic CLI](/cli/azure/install-classic-cli).

## <a name="azure-powershell-service-management-module"></a>Module de management des services Azure PowerShell

Dans cette section, vous allez utiliser le module de management des services Azure PowerShell pour migrer une adresse IP réservée classique vers une adresse IP publique statique Azure Resource Manager.

> [!NOTE]
> L’adresse IP réservée doit être supprimée d’un service cloud auquel l’adresse IP est associée.

```azurepowershell-interactive
$validate = Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Validate
$validate.ValidationMessages

```

La commande précédente affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez exécuter les étapes suivantes pour **Préparer** et **Valider** la migration :

```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Prepare
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Commit
```
Un nouveau groupe de ressources dans Azure Resource Manager est créé à l’aide du nom de l’adresse IP réservée migrée. Dans l’exemple précédent, le groupe de ressources est **myReservedIP-Migrated**.

## <a name="azure-classic-cli"></a>Azure Classic CLI

Dans cette section, vous allez utiliser le l’interface de ligne de commande classique Azure pour migrer une adresse IP réservée classique vers une adresse IP publique statique Azure Resource Manager.

> [!NOTE]
> L’adresse IP réservée doit être supprimée d’un service cloud auquel l’adresse IP est associée.

```azurecli-interactive
azure network reserved-ip validate migration myReservedIP

```
La commande précédente affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez exécuter les étapes suivantes pour **Préparer** et **Valider** la migration :

```azurecli-interactive
azure network reserved-ip prepare-migration myReservedIP
azure network reserved-ip commit-migration myReservedIP
```
Un nouveau groupe de ressources dans Azure Resource Manager est créé à l’aide du nom de l’adresse IP réservée migrée. Dans l’exemple précédent, le groupe de ressources est **myReservedIP-Migrated**.

## <a name="next-steps"></a>Étapes suivantes


Pour plus d’informations, sur les adresses IP publiques dans Azure, consultez :

- [Adresses IP publiques dans Azure](public-ip-addresses.md)
- [Créer une adresse IP publique - Portail Azure](create-public-ip-portal.md)

