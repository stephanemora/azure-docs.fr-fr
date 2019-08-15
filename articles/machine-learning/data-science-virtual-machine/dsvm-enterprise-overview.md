---
title: Introduction aux environnements en équipe basés sur Data Science Virtual Machine – Azure | Microsoft Docs
description: Modèles de déploiement de la machine virtuelle Data Science dans un environnement en équipes d’entreprise.
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 40b8f101d968514ac599b43a79b7e62b7fd837a4
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779480"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Environnement analytique d’équipe et d’intelligence artificielle basé sur Data Science Virtual Machine 
La machine virtuelle [Data Science Virtual Machine](overview.md) (DSVM) fournit un environnement riche dans la plateforme Azure avec des logiciels prédéfinis pour l’intelligence artificielle (AI) et l’analytique données. 

En règle générale, la DSVM est utilisée comme un ordinateur de bureau d’analyse individuel. Les scientifiques des données individuels gagnent en productivité avec cette notion partagée de leur environnement analytique prédéfini. Étant donné que les grandes équipes d’analyse planifient leurs environnements d’analyse pour leurs scientifiques des données et leurs développeurs en intelligence artificielle, une infrastructure analytique partagée pour le développement et l’expérimentation est l’un des thèmes récurrents. Cette infrastructure est gérée en fonction des stratégies informatiques de l’entreprise, qui facilitent également la collaboration et la cohérence entre les équipes de science des données/d’analyse. 

Une infrastructure partagée permet également au département informatique de mieux utiliser l’environnement d’analyse. Certaines organisations appellent l’infrastructure de science des données/d’analyse basée sur l’équipe un « bac à sable analytique ». Elle permet aux scientifiques des données d’accéder à différentes ressources de données pour comprendre rapidement les données, faire des expériences, valider des hypothèses et créer des modèles prédictifs sans affecter l’environnement de production. 

Étant donné que la Data Science Virtual Machine (DSVM) fonctionne au niveau de l’infrastructure Azure, les administrateurs informatiques peuvent la configurer facilement afin qu’elle fonctionne conformément aux stratégies informatiques de l’entreprise. La DSVM offre une flexibilité parfaite d’implémentation de différentes architectures de partage tout en permettant un accès contrôlé aux ressources de données de l’entreprise. 

Cette section présente les modèles et recommandations que vous pouvez appliquer pour déployer la machine virtuelle DSVM en tant qu’infrastructure de science des données basée sur une équipe. Les blocs qui forment ces modèles proviennent de l’infrastructure as a service (IaaS) d’Azure et sont donc applicables à toutes les machines virtuelles Azure. Cette série d’articles est axée sur l’application de ces fonctionnalités d’infrastructure Azure standard à la machine virtuelle DSVM. 

Exemples de blocs de construction principaux d’un environnement d’analyse d’équipe d’entreprise :

* [Pool mis à l’échelle automatiquement de machines virtuelles DSVM](dsvm-pools.md)
* [Identité commune et accès à l’espace de travail à partir des machines virtuelles DSVM du pool](dsvm-common-identity.md)
* [Sécuriser l’accès aux sources de données](dsvm-secure-access-keys.md)


Cette série d’articles contient des conseils et des pointeurs qui couvrent chacun des aspects précédents. Elle n’aborde ni toutes les considérations, ni tous les besoins liés au déploiement de machines virtuelles DSVM dans les configurations de grande entreprise. Voici une autre documentation Azure que vous pouvez utiliser lors de l’implémentation d’instances de machines virtuelles DSVM dans votre entreprise : 

* [Sécurité réseau](https://docs.microsoft.com/azure/security/fundamentals/network-security
* [Surveillance](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) et [gestion](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Journalisation et audit](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Définition et application de stratégies](../../governance/policy/overview.md)
* [Logiciel anti-programme malveillant](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Chiffrement](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Découverte et gouvernance des données](https://docs.microsoft.com/azure/data-catalog/)

Le [Centre des architectures Azure](https://docs.microsoft.com/azure/architecture/) fournit une architecture de bout en bout et des modèles détaillés pour la création et la gestion de votre infrastructure d’analyse basée sur le cloud. 
