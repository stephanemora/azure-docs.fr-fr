---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471451"
---
Quand vous achetez une instance de machine virtuelle réservée, vous pouvez choisir d’optimiser la flexibilité de la taille ou la priorité de capacité de l’instance. Pour plus d’informations sur la définition ou la modification du paramètre d’optimisation pour des instances de machine virtuelle réservées, consultez [Modifier le paramètre d’optimisation pour des instances de machine virtuelle réservées](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Avec une instance de machine virtuelle réservée optimisée pour la flexibilité de la taille de l’instance, la réservation que vous achetez peut s’appliquer aux tailles de machine virtuelle d’un même groupe de flexibilité de la taille d'instance. Par exemple, si vous achetez une réservation pour une taille de machine virtuelle qui est répertoriée dans la série DSv2, comme Standard_DS5_v2, la remise sur réservation peut s’appliquer aux quatre autres tailles répertoriées dans ce même groupe :

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mais cette remise sur réservation ne s’applique pas aux tailles de machine virtuelle qui figurent dans des groupes de flexibilité de la taille d'instance différents, comme des références SKU dans la table de mémoire haute de la série DSv2 : Standard_DS11_v2, Standard_DS12_v2, etc.

Dans le groupe de flexibilité de la taille d'instance, le nombre de machines virtuelles auxquelles la remise sur réservation s’applique dépend de la taille de machine virtuelle que vous choisissez lorsque vous achetez une réservation. Ce nombre dépend également des tailles des machines virtuelles que vous utilisez. La colonne de ratio compare l’empreinte relative de chaque taille de machine virtuelle de ce groupe de flexibilité de la taille d'instance. Utilisez la valeur de ratio pour calculer la façon dont la remise sur réservation s’applique aux machines virtuelles vous utilisez.

## <a name="examples"></a>Exemples

Les exemples suivants utilisent les tailles et ratios de la table de la série DSv2.

Vous achetez une instance de machine virtuelle réservée avec la taille Standard_DS4_v2, où le ratio ou l’empreinte relative par rapport aux autres tailles de cette série correspond à la valeur 8.

- Scénario 1 : Utiliser huit machines virtuelles de taille Standard_DS1_v2 avec un ratio de valeur 1. Votre remise sur réservation s’applique à ces huit machines virtuelles.
- Scénario 2 : Utiliser deux machines virtuelles de taille Standard_DS2_v2 avec chacune un ratio de valeur 2. Utiliser également une machine virtuelle de taille Standard_DS3_v2 avec un ratio de valeur 4. Empreinte totale : 2 + 2 + 4 = 8. Votre remise sur réservation s’applique à ces trois machines virtuelles.
- Scénario 3 : Utiliser une machine virtuelle de taille Standard_DS5_v2 avec un ratio de valeur 16. Votre remise sur réservation s’applique à la moitié du coût du calcul de cette machine virtuelle.

Les sections suivantes présentent les tailles qui figurent dans le même groupe de séries de taille lorsque vous achetez une instance de machine virtuelle réservée optimisée pour la flexibilité de taille d’instance.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Ratio de flexibilité de la taille d’instance pour les machines virtuelles 

Le fichier CSV ci-dessous contient les groupes de flexibilité de la taille d'instance, ArmSkuName et les ratios.  

[Ratios de flexibilité de la taille d'instance](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Nous conservons l’URL du fichier et le schéma fixes pour que vous puissiez utiliser le fichier par programmation. Les données seront également disponibles prochainement via l’API.
