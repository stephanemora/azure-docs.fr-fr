---
title: Utiliser le portail pour déployer des machines virtuelles Azure Spot
description: Comment utiliser le Portail pour déployer des machines virtuelles Spot
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: a80356cf7045fc079e71d429f89f3b4b0fff31ba
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694374"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Déployer des machines virtuelles Azure Spot à l’aide du portail Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques uniformes

L’utilisation de [machines virtuelles Azure Spot](spot-vms.md) vous permet de disposer de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure aura besoin de récupérer la capacité, l’infrastructure Azure supprimera les machines virtuelles Azure Spot. Les machines virtuelles Azure Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

Les tarifs des machines virtuelles Azure Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix des machines virtuelles pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Pour plus d’informations sur la définition du prix maximal, consultez [Machines virtuelles Azure Spot - Tarifs](spot-vms.md#pricing).

Vous avez la possibilité de définir un prix maximal que vous êtes prêt à payer, par heure, pour la machine virtuelle. Le prix maximal d’une machine virtuelle Azure Spot peut être défini en dollars américains (USD), en utilisant jusqu’à 5 décimales. Par exemple, la valeur `0.05701` correspond à un prix maximal de 0,05701 $ USD par heure. Si vous définissez `-1` comme prix maximal, la machine virtuelle n’est pas supprimée en fonction du prix. Le prix de la machine virtuelle sera le prix actuel de Spot ou le prix d’une machine virtuelle standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles.

Une fois la machine virtuelle supprimée, vous avez la possibilité de supprimer la machine virtuelle et le disque sous-jacent ou de libérer la machine virtuelle afin qu’elle puisse être redémarrée ultérieurement.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Lorsque vous déployez une machine virtuelle, vous pouvez choisir d’utiliser une instance Azure Spot.


Sous l’onglet **Concepts de base**, dans la section **Détails de l’instance**, **Non** est la valeur par défaut pour l’utilisation d’une instance Azure Spot.

![Capture d’écran du choix de Non, ne pas utiliser d’instance Azure Spot](./media/spot-portal/no.png)

Si vous sélectionnez **Oui**, la section se développe et vous pouvez choisir vos [type d’éviction et stratégie d’éviction](spot-vms.md#eviction-policy). 

![Capture d’écran du choix de Oui, utiliser une instance Azure Spot](./media/spot-portal/yes.png)

Vous pouvez également comparer les tarifs et les taux d’éviction avec d’autres régions similaires en sélectionnant **Voir l'historique des prix et comparer les prix dans les régions proches**.

Dans cet exemple, la région Canada Centre est moins coûteuse et a un taux d’éviction inférieur à celui de la région USA Est.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Capture d’écran des options de région avec la différence de tarification et les taux d’éviction.":::

Vous pouvez modifier la région en sélectionnant le choix qui vous convient le mieux, puis en sélectionnant **OK**.

## <a name="simulate-an-eviction"></a>Simuler une éviction

Vous pouvez [simuler l’éviction](/rest/api/compute/virtualmachines/simulateeviction) d’une machine virtuelle Azure Spot afin de tester l’efficacité de la réponse de votre application en vue d’une éviction soudaine. 

Remplacez les éléments suivants avec vos informations : 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` signifie que l’éviction simulée a réussi. 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer des machines virtuelles Azure Spot avec [PowerShell](./windows/spot-powershell.md), [une interface CLI](./linux/spot-cli.md) ou un [modèle](./linux/spot-template.md).
