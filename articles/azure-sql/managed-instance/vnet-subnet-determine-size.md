---
title: Déterminer la taille de sous-réseau requise
titleSuffix: Azure SQL Managed Instance
description: Cette rubrique explique comment calculer la taille du sous-réseau dans lequel les instances Azure SQL Managed Instance seront déployées.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84039520"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Déterminer la taille de sous-réseau d’un réseau virtuel pour Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance doit être déployé au sein d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) Azure.

Le nombre d’instances SQL Managed Instance pouvant être déployées dans le sous-réseau du réseau virtuel dépend de la taille du sous-réseau (plage de sous-réseau).

Lorsque vous créez une instance SQL Managed Instance, Azure alloue plusieurs machines virtuelles en fonction du niveau sélectionné pendant l’approvisionnement. Étant donné que ces machines virtuelles sont associées à votre sous-réseau, elles nécessitent des adresses IP. Pour garantir la haute disponibilité pendant les opérations normales et la maintenance du service, Azure peut allouer des machines virtuelles supplémentaires. Par conséquent, le nombre d’adresses IP nécessaires dans un sous-réseau est supérieur au nombre d’instances SQL Managed Instance dans ce sous-réseau.

Par défaut, une instance SQL Managed Instance a besoin d’un minimum de 16 adresses IP dans un sous-réseau et peut en utiliser jusqu’à 256. Ainsi, vous pouvez utiliser un masque de sous-réseau entre /28 et /24 lors de la définition de vos plages d’adresses IP de sous-réseau. Un bit de masque de réseau de /28 (14 hôtes par réseau) convient à un usage général ou à un déploiement critique pour l'entreprise. Un bit de masque de /27 (30 hôtes par réseau) est idéal pour plusieurs déploiements de SQL Managed Instance au sein du même réseau virtuel. Les paramètres de bit de masque de /26 (62 hôtes) et /24 (254 hôtes) permettent une meilleure mise à l'échelle du réseau virtuel afin de prendre en charge plus de déploiements de SQL Managed Instance.

> [!IMPORTANT]
> Une taille de sous-réseau avec 16 adresses IP est le strict minimum, avec un potentiel limité où une opération de mise à l’échelle telle que la modification de taille vCore n’est pas prise en charge. Le choix d’un sous-réseau avec le préfixe /27 ou le préfixe le plus long est fortement recommandé.

## <a name="determine-subnet-size"></a>Déterminer la taille du sous-réseau

Si vous envisagez de déployer plusieurs instances SQL Managed Instance à l’intérieur du sous-réseau et avez besoin d’optimiser la taille de ce dernier, utilisez ces paramètres pour former un calcul :

- Azure utilise cinq adresses IP dans le sous-réseau pour ses besoins propres.
- Chaque instance à usage général nécessite deux adresses.
- Chaque instance Critique pour l’entreprise a besoin de quatre adresses

**Exemple** : vous prévoyez d’utiliser trois instances SQL Managed Instance de type Usage général et deux de type Critique pour l’entreprise. Cela signifie que vous avez besoin de 5 + 3 * 2 + 2 * 4 = 19 adresses IP. Comme les plages d’adresses IP sont définies par puissance de 2, vous avez besoin d’une plage de 32 (2 ^ 5) adresses IP. Ainsi, vous devez réserver le sous-réseau avec un masque de sous-réseau de /27.

> [!IMPORTANT]
> Le calcul affiché ci-dessus deviendra obsolète au fur et à mesure des améliorations.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Découvrez plus en détail l’[architecture de connectivité pour SQL Managed Instance](connectivity-architecture-overview.md).
- Découvrez comment [créer le réseau virtuel dans lequel seront déployées les instances SQL Managed Instance](virtual-network-subnet-create-arm-template.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](custom-dns-configure.md).
