---
title: Migrer votre centre de données local vers Azure | Microsoft Docs
description: Parcourez un livre blanc sur la migration de vos centres de données locaux vers Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167753"
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migration de vos charges de travail locales vers Azure


Microsoft Azure offre un accès à un ensemble complet de services cloud que vous, en tant que développeur et professionnel de l’informatique, utilisez pour générer, déployer et gérer des applications sur un éventail d’outils et d’infrastructures via un réseau mondial de centres de données. Votre entreprise étant confrontée aux défis associés à la transition numérique, le cloud Azure vous permet de trouver la solution pour optimiser les ressources et les opérations, communiquer avec vos clients et employés et transformer vos produits.

Toutefois, Azure reconnaît que, malgré tous les avantages proposés par le cloud en termes de vitesse et de flexibilité, de réduction des coûts, de performances et de fiabilité, de nombreuses organisations vont devoir exécuter des centres de données locaux pendant un certain temps. C’est pour répondre à ces barrières concernant l’adoption du cloud qu’Azure propose une stratégie de cloud hybride qui crée des ponts entre vos centres de données locaux et le cloud public Azure. Par exemple, vous pouvez utiliser des ressources cloud Azure comme la sauvegarde pour protéger des ressources locales ou encore utiliser des analyses Azure pour obtenir des informations sur les charges de travail locales. 

Dans le cadre de notre stratégie de cloud hybride, Azure fournit toujours plus de solutions pour migrer les applications et les charges de travail locales vers le cloud. En suivant une procédure simple, vous pouvez évaluer l’intégralité de vos ressources locales pour déterminer comment elles s’exécuteront dans le cloud Azure. Une fois cette évaluation approfondie obtenue, vous pouvez migrer des ressources vers Azure en toute confiance. Quand les ressources sont opérationnelles dans Azure, vous pouvez les optimiser pour conserver et améliorer l’accès, la flexibilité, la sécurité et la fiabilité.

Cette série d’articles sur la migration vous explique comment planifier et mettre en œuvre une stratégie de migration pour votre entreprise. Les articles illustrent plusieurs scénarios toujours plus complexes que nous ajouterons au fil du temps. Ils sont résumés dans le tableau ci-dessous. Pour chacun d’entre eux, nous proposons une vue d’ensemble, une architecture de migration et présentons la procédure de migration. 

**Scénario** | **Solution** | **Services** | **Article** 
--- | --- | --- | ---
[Scénario 1 : Détection et évaluation](migrate-scenarios-assessment.md) | Détecter et évaluer des applications, des données et l’infrastructure locales pour la migration vers Azure | Assistant Migration de données, service Azure Migrate  | Déjà disponible
**[Scénario 2 : Réhéberger l’application](migrate-scenarios-lift-and-shift.md)** | Migration « lift-and-shift » des applications vers Azure. | Azure Site Recovery, Azure Database Migration Service, Azure SQL Database Managed Instance | Déjà disponible
**Scénario 3 : Refactoriser l’application** | Refactorisation des applications pendant la migration vers Azure. | En cours de planification | Prévu
**Scénario 4 : Remanier l’architecture de l’application** | Remaniement de l’architecture des applications pendant la migration vers Azure. | En cours de planification | Prévu
**Scénario 5 : Regénérer l’application** |Regénération des applications pendant la migration vers Azure. | En cours de planification | Prévu




