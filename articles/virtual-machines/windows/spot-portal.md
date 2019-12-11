---
title: Utiliser le portail pour déployer des machines virtuelles Azure Spot
description: Découvrez comment utiliser Azure PowerShell afin de déployer des machines virtuelles Spot pour réaliser des économies sur les coûts.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 682ee86c373c715080ef1baf82b473242a2e43db
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781865"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Aperçu : Déployer des machines virtuelles Spot à l’aide du portail Azure

L’utilisation de [machines virtuelles Spot](spot-vms.md) vous permet de tirer parti de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les machines virtuelles Spot. Les machines virtuelles Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

Les tarifs des machines virtuelles Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix des machines virtuelles pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Pour plus d’informations sur la définition du prix maximal, consultez [Machines virtuelles Spot - Tarifs](spot-vms.md#pricing).

Vous avez la possibilité de définir un prix maximal que vous êtes prêt à payer, par heure, pour la machine virtuelle. Le prix maximal d’une machine virtuelle Spot peut être défini en dollars américains (USD), en utilisant jusqu’à 5 décimales. Par exemple, la valeur `0.05701` correspond à un prix maximal de 0,05701 $ USD par heure. Si vous définissez `-1` comme prix maximal, la machine virtuelle n’est pas supprimée en fonction du prix. Le prix de la machine virtuelle sera le prix actuel de Spot ou le prix d’une machine virtuelle standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles.

> [!IMPORTANT]
> Les instances Spot sont actuellement en préversion publique.
> Cette préversion n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Dans la première partie de la préversion publique, les instances Spot auront un prix fixe, de sorte qu’il n’y aura aucune éviction basée sur les prix.

## <a name="create-the-vm"></a>Création de la machine virtuelle

Le processus de création d’une machine virtuelle qui utilise des machines virtuelles Spot est le même que celui décrit dans le [guide de démarrage rapide](quick-create-portal.md). Lorsque vous déployez une machine virtuelle, vous pouvez choisir d’utiliser une instance Azure Spot.


Sous l’onglet **Concepts de base**, dans la section **Détails de l’instance**, **Non** est la valeur par défaut pour l’utilisation d’une instance Azure Spot.

![Capture d’écran du choix de Non, ne pas utiliser d’instance Azure Spot](media/spot-portal/no.png)

Si vous sélectionnez **Oui**, la section se développe et vous pouvez choisir vos [type d’éviction et stratégie d’éviction](spot-vms.md#eviction-policy). 

![Capture d’écran du choix de Oui, utiliser une instance Azure Spot](media/spot-portal/yes.png)


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer des machines virtuelles Spot avec [PowerShell](spot-powershell.md).