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
ms.openlocfilehash: 911d6484421cc9fddad0530bf8d9ab4f01d48bf8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Environnement AI et Team Analytics basé sur les machines virtuelles de science des données 
La [machine virtuelle de science des données](overview.md) (DSVM) fournit un environnement riche dans Azure Cloud avec des logiciels prédéfinis pour l’analyse des données et AI. En règle générale, la machine virtuelle DSVM est utilisée comme bureau individuel d’analyse, et les chercheurs de données gagnent en productivité avec cette notion partagée de leur environnement d’analyse prédéfini. Lorsque des équipes d’analyse de grande taille planifient leurs environnements d’analyse pour leurs chercheurs de données et développeurs AI, l’un des thèmes récurrents concerne l’infrastructure de développement et d’expérimentation d’analyse partagée qui est gérée conformément aux stratégies informatiques d’entreprise et qui facilite également la collaboration et la cohérence entre les équipes de science des données et d’analyse. Une infrastructure partagée permet également au département informatique de mieux utiliser l’environnement d’analyse. L’infrastructure d’analyse et de science des données basée sur une équipe est également appelée par certaines organisations « bac à sable (sandbox) d’analyse » qui permet aux chercheurs de données de comprendre rapidement les données, d’effectuer des expériences, de valider des hypothèses, de créer des modèles prédictifs de manière sécurisée sans impacter l’environnement de production tout en ayant accès aux différentes ressources de données. 

Comme la machine virtuelle DSVM fonctionne au niveau de l’infrastructure Azure, les administrateurs informatiques peuvent la configurer facilement pour qu’elle fonctionne conformément aux stratégies informatiques de l’entreprise et qu’elle offre une flexibilité totale dans l’implémentation des différentes architectures de partage avec un accès aux ressources de données d’entreprise de manière contrôlée. 

Cette section présente les modèles et recommandations qui peuvent être appliqués pour déployer la machine virtuelle DSVM en tant qu’infrastructure de science des données basée sur une équipe.  Les blocs de construction de ces modèles sont directement tirés d’Azure IaaS (infrastructure as a service). De ce fait, il s’appliquent à toutes les machines virtuelles Azure. Cette série d’articles est essentiellement axée sur l’application de ces fonctionnalités d’infrastructure Azure standard à la machine virtuelle DSVM. 

Exemples de blocs de construction principaux d’un environnement d’analyse d’équipe d’entreprise :

* [Pool mis à l’échelle automatiquement de machines virtuelles DSVM](dsvm-pools.md)
* [Identité commune et accès à l’espace de travail à partir des machines virtuelles DSVM du pool](dsvm-common-identity.md)
* [Sécuriser l’accès aux sources de données](dsvm-secure-access-keys.md)
* Gouvernance et détection des jeux de données d’entreprise et ouverts

Cette série d’articles contient des conseils et des pointeurs qui couvrent chacun de ces aspects. Le [centre d’architecture Azure](https://docs.microsoft.com/en-us/azure/architecture/) fournit une architecture de bout en bout plus détaillée pour votre infrastructure d’analyse.  
