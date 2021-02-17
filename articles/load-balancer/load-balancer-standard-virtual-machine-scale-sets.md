---
title: Ajouter des règles pour Azure Standard Load Balancer et les groupes de machines virtuelles identiques
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Ce parcours d’apprentissage vous permet de vous familiariser avec Azure Standard Load Balancer et les groupes de machines virtuelles identiques.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592277"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Ajouter des règles pour Azure Load Balancer avec des groupes de machines virtuelles identiques

Lorsque vous utilisez des groupes de machines virtuelles identiques et Azure Load Balancer, tenez compte des recommandations suivantes.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Réacheminement de port et règles NAT de trafic entrant

Une fois le groupe identique créé,vous ne pouvez plus modifier le port principal pour une règle d'équilibrage de charge qu’utilise une sonde d'intégrité de l’équilibreur de charge. Pour modifier le port, supprimez la sonde d’intégrité en mettant à jour le groupe de machines virtuelles identiques et le port. Reconfigurez ensuite la sonde d’intégrité.

Lorsque vous utilisez le groupe de machines virtuelles identiques dans le pool principal de l’équilibreur de charge, les règles NAT de trafic entrant par défaut sont automatiquement créées.
  
## <a name="inbound-nat-pool"></a>Pool NAT de trafic entrant

Chaque groupe de machines virtuelles identiques doit comporter au moins un pool NAT de trafic entrant. Un pool NAT de trafic entrant est un ensemble de règles NAT de trafic entrant. Un pool NAT de trafic entrant ne peut pas prendre en charge plusieurs groupes de machines virtuelles identiques.

## <a name="load-balancing-rules"></a>Règles d’équilibrage de la charge

Lorsque vous utilisez le groupe de machines virtuelles identiques dans le pool principal de l’équilibreur de charge, la règle d’équilibrage de charge par défaut est automatiquement créée.
  
## <a name="outbound-rules"></a>Règles de trafic sortant

Pour créer une règle de trafic sortant pour un pool principal auquel une règle d’équilibrage de charge fait référence, une fois la règle d’équilibrage de charge du trafic entrant créée, dans le portail Azure, sous **Créer des règles de trafic sortant implicites**, sélectionnez **Non**.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Capture d’écran montrant la création d’une règle d’équilibrage de charge." border="true":::

Pour déployer un groupe identique de machines virtuelles avec une instance existante de Load Balancer, utilisez l’une des méthodes suivantes :

* [Configurer un groupe de machines virtuelles identiques avec une instance existante d’Azure Load Balancer à l’aide du portail Azure](./configure-vm-scale-set-portal.md).
* [Configurer un groupe de machines virtuelles identiques avec une instance existante d’Azure Load Balancer à l’aide d’Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Configurer un groupe de machines virtuelles identiques avec une instance existante d’Azure Load Balancer à l’aide d’Azure CLI](./configure-vm-scale-set-cli.md).
* [Mettre à jour ou supprimer une instance existante d’Azure Load Balancer utilisée par un groupe de machines virtuelles identiques](./update-load-balancer-with-vm-scale-set.md).
