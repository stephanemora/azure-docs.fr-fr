---
title: Support d’Azure Resource Manager pour l’équilibrage de charge | Microsoft Docs
description: Utilisation de Powershell pour l’équilibrage de charge avec Azure Resource Manager. Utilisation de modèles pour l'équilibrage de charge
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 596ac871067886ee3124c0f21beb35cb3b8fe1ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888980"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Utilisation de la prise en charge d’Azure Resource Manager pour l’équilibrage de charge Azure



Azure Resource Manager est l’infrastructure de gestion des services par défaut dans Azure. Azure Load Balancer peut être géré à l’aide des outils et des API basés sur Azure Resource Manager.

## <a name="concepts"></a>Concepts

Avec Azure Resource Manager, Azure Load Balancer contient les ressources enfants suivantes :

* Configuration d’une adresse IP frontend : un équilibreur de charge peut inclure une ou plusieurs adresses IP frontend, également appelées « adresses IP virtuelles ». Ces adresses IP servent d'entrée pour le trafic.
* Pool d’adresses backend : il s’agit des adresses IP associées à la carte d’interface réseau (NIC) des machines virtuelles vers lesquelles la charge est distribuée.
* Règles d’équilibrage de charge : une propriété de règle mappe une adresse IP frontale donnée et la combinaison de port à un ensemble d’adresses IP de serveur principal et la combinaison port. Un même équilibreur de charge peut avoir plusieurs règles d’équilibrage de charge. Chaque règle est une combinaison d’une adresse IP frontale et port et back-end IP et port associés aux machines virtuelles.
* Sondes : les sondes vous permettent d'effectuer le suivi de l'intégrité des instances de machine virtuelle. En cas d’échec d’une sonde d’intégrité, l’instance de machine virtuelle est automatiquement mise hors service.
* Les règles NAT entrantes : NAT règles définissant le trafic entrant transitant par l’adresse IP frontale et distribué à l’adresse IP de serveur principal.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Azure Resource Manager vous permet d’approvisionner vos applications à l'aide d'un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services avec leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.

Les modèles peuvent inclure des définitions de machines virtuelles, de réseaux virtuels, de groupes à haute disponibilité, d’interfaces réseau (NIC), de comptes de stockage, d’équilibreurs de charge, de groupes de sécurité réseau et d’adresses IP publiques. Avec des modèles, vous pouvez créer tout ce dont vous avez besoin pour une application complexe. Le contrôle de version et la collaboration du fichier de modèle peuvent être vérifiés dans le système de gestion de contenu.

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

Vous pouvez également [commencer par créer un équilibrage de charge avec accès par Internet](load-balancer-get-started-internet-arm-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement spécifique de trafic réseau d’équilibrage de charge.

Découvrez comment gérer les [paramètres de délai d’expiration TCP inactif pour un équilibreur de charge](load-balancer-tcp-idle-timeout.md). C’est important lorsque votre application a besoin de conserver des connexions actives pour les serveurs situés derrière un équilibreur de charge.
