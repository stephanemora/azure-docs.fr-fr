---
title: Introduction aux environnements en équipe basés sur les machines virtuelles de science des données - Azure | Microsoft Docs
description: Modèles de déploiement de machine virtuelle de science des données en tant qu’environnement en équipes d’entreprise.
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361369"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Environnement AI et Team Analytics basé sur les machines virtuelles de science des données 
La [machine virtuelle de science des données](overview.md) (DSVM) fournit un environnement riche dans Azure Cloud avec des logiciels prédéfinis pour l’analyse des données et AI. En règle générale, la machine virtuelle DSVM est utilisée comme bureau individuel d’analyse, et les chercheurs de données gagnent en productivité avec cette notion partagée de leur environnement d’analyse prédéfini. Lorsque des équipes d’analyse de grande taille planifient leurs environnements d’analyse pour leurs chercheurs de données et développeurs AI, l’un des thèmes récurrents concerne l’infrastructure de développement et d’expérimentation d’analyse partagée qui est gérée conformément aux stratégies informatiques d’entreprise et qui facilite également la collaboration et la cohérence entre les équipes de science des données et d’analyse. Une infrastructure partagée permet également au département informatique de mieux utiliser l’environnement d’analyse. L’infrastructure d’analyse et de science des données basée sur une équipe est également appelée par certaines organisations « bac à sable (sandbox) d’analyse » qui permet aux chercheurs de données de comprendre rapidement les données, d’effectuer des expériences, de valider des hypothèses, de créer des modèles prédictifs de manière sécurisée sans impacter l’environnement de production tout en ayant accès aux différentes ressources de données. 

Comme la machine virtuelle DSVM fonctionne au niveau de l’infrastructure Azure, les administrateurs informatiques peuvent la configurer facilement pour qu’elle fonctionne conformément aux stratégies informatiques de l’entreprise et qu’elle offre une flexibilité totale dans l’implémentation des différentes architectures de partage avec un accès aux ressources de données d’entreprise de manière contrôlée. 

Cette section présente les modèles et recommandations qui peuvent être appliqués pour déployer la machine virtuelle DSVM en tant qu’infrastructure de science des données basée sur une équipe.  Les blocs de construction de ces modèles sont directement tirés d’Azure IaaS (infrastructure as a service). De ce fait, il s’appliquent à toutes les machines virtuelles Azure. Cette série d’articles est essentiellement axée sur l’application de ces fonctionnalités d’infrastructure Azure standard à la machine virtuelle DSVM. 

Exemples de blocs de construction principaux d’un environnement d’analyse d’équipe d’entreprise :

* [Pool mis à l’échelle automatiquement de machines virtuelles DSVM](dsvm-pools.md)
* [Identité commune et accès à l’espace de travail à partir des machines virtuelles DSVM du pool](dsvm-common-identity.md)
* [Sécuriser l’accès aux sources de données](dsvm-secure-access-keys.md)


Cette série d’articles contient des conseils et des pointeurs qui couvrent chacun des aspects ci-dessus. Le déploiement de DSVM dans des configurations d’entreprise d’envergure implique évidemment la prise en compte de plusieurs autres considérations et besoins, qui ne sont pas encore abordés dans cette série d’articles. Voici quelques-uns des autres aspects et pointeurs vers la documentation Azure générale que vous pouvez utiliser lors de cette implémentation sur les instances DSVM de votre entreprise. 

* [Sécurité du réseau](https://docs.microsoft.com/azure/security/azure-network-security)
* [Surveillance](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) et [Gestion](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Journalisation et audit](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Définition et application de stratégies](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Logiciel anti-programme malveillant](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Chiffrement](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Découverte et gouvernance des données](https://docs.microsoft.com/azure/data-catalog/)

Le [Centre des architectures Azure](https://docs.microsoft.com/en-us/azure/architecture/) constitue également une ressource particulièrement utile qui fournit une architecture de bout en bout et des modèles détaillés pour la création et la gestion de votre infrastructure d’analyse basée sur le cloud. 
