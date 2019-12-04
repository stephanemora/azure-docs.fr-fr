---
title: Support d’Azure Resource Manager pour l’équilibrage de charge
description: Dans cet article, utilisez Azure PowerShell et des modèles avec Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215384"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Prise en charge d’Azure Resource Manager avec Azure Load Balancer



Azure Resource Manager est l’infrastructure de gestion des services par défaut dans Azure. Azure Load Balancer peut être géré à l’aide des outils et des API basés sur Azure Resource Manager.

## <a name="concepts"></a>Concepts

Avec Azure Resource Manager, Azure Load Balancer contient les ressources enfants suivantes :

* Configuration d’une adresse IP frontend : un équilibreur de charge peut inclure une ou plusieurs adresses IP frontend, également appelées « adresses IP virtuelles ». Ces adresses IP servent d'entrée pour le trafic.
* Pool d’adresses backend : ce pool est une collection d’adresses IP associées à la carte d’interface réseau (NIC) des machines virtuelles vers lesquelles la charge est distribuée.
* Règles d’équilibrage de charge : une propriété de règle mappe une paire adresse IP/port frontend à une combinaison d’adresses IP/port backend. Un même équilibreur de charge peut avoir plusieurs règles d’équilibrage de charge. Chaque règle correspond à la combinaison d’une paire adresse IP/port frontend et d’une paire adresse IP/port backend associées aux machines virtuelles.
* Sondes : les sondes vous permettent d'effectuer le suivi de l'intégrité des instances de machine virtuelle. En cas d’échec d’une sonde d’intégrité, l’instance de machine virtuelle est automatiquement mise hors service.
* Règles NAT de trafic entrant : règles NAT définissant le trafic entrant qui transite via l’adresse IP frontend et est distribué à l’adresse IP backend.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Azure Resource Manager vous permet d’approvisionner vos applications à l'aide d'un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.

Les modèles peuvent inclure des définitions pour :
* **Machines virtuelles**
* **Réseaux virtuels**
* **Groupes à haute disponibilité**
* **Interfaces réseau (cartes réseau)**
* **Comptes de stockage**
* **Équilibreurs de charge**
* **Groupes de sécurité réseau**
* **Adresses IP publiques.** 

Avec des modèles, vous pouvez créer tout ce dont vous avez besoin pour une application complexe. Le contrôle de version et la collaboration du fichier de modèle peuvent être vérifiés dans le système de gestion de contenu.

[En savoir plus sur les modèles](../azure-resource-manager/resource-manager-template-walkthrough.md)

[En savoir plus sur les ressources de réseau](../networking/networking-overview.md)

Pour obtenir des modèles de démarrage rapide qui utilisent Azure Load Balancer, consultez le [dépôt GitHub](https://github.com/Azure/azure-quickstart-templates) où se trouve un ensemble de modèles générés par la communauté.

Exemples de modèles :

* [2 machines virtuelles dans un équilibrage de charge et les règles d'équilibrage de charge](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 machines virtuelles dans un réseau virtuel avec un équilibrage de charge interne et les règles d’équilibrage de charge](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 machines virtuelles dans un équilibrage de charge et la configuration des règles NAT sur l'équilibrage de charge](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configuration de l'équilibrage de charge Azure avec PowerShell ou la CLI

Prise en main des applets de commande, des outils de ligne de commande et des API REST Azure Resource Manager

* [cmdlets de mise en réseau Azure](https://docs.microsoft.com/powershell/module/az.network#networking) peuvent être utilisées pour créer un équilibrage de charge.
* [Création d’un équilibrage de charge à l’aide d’Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Utilisation de la CLI Azure avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md)
* [API REST de l'équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Étapes suivantes

[Commencez par créer un équilibrage de charge avec accès par Internet](load-balancer-get-started-internet-arm-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement spécifique de trafic réseau.

Découvrez comment gérer les [paramètres de délai d’expiration TCP inactif pour un équilibreur de charge](load-balancer-tcp-idle-timeout.md). Ces paramètres sont importants lorsque votre application a besoin de conserver des connexions actives pour les serveurs situés derrière un équilibreur de charge.
