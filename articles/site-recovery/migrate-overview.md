---
title: Migrer des serveurs et des machines virtuelles sur Azure avec Azure Site Recovery
description: Explique comment migrer des machines locales et des machines virtuelles Azure IaaS sur Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281308"
---
# <a name="about-migration"></a>À propos de la migration

Utilisez le service Azure Migrate plutôt que le service Azure Site Recovery, pour migrer des machines virtuelles et des serveurs vers Azure. [Apprenez-en davantage](../migrate/migrate-services-overview.md) sur Azure Migrate.


## <a name="why-use-azure-migrate"></a>Pourquoi utiliser Azure Migrate ?

L’utilisation d’Azure Migrate pour la migration offre un certain nombre d’avantages :
 
 
- Azure Migrate fournit un hub centralisé pour la découverte, l’évaluation et la migration vers Azure.
- L’utilisation d’Azure Migrate fournit une interopérabilité et une extensibilité future avec les outils Azure Migrate, les autres services Azure et les outils tiers.
- L’outil Azure Migrate : Migration de serveurs est conçu spécialement pour la migration de serveurs vers Azure. Il est optimisé pour la migration. Vous n’avez pas besoin d’en savoir plus sur les concepts et les scénarios qui ne sont pas directement pertinents pour la migration. 
- Il n’y a pas de frais d’utilisation de l’outil pour la migration pendant 180 jours, à partir du moment où la réplication est démarrée pour une machine virtuelle. Cela vous donne le temps de terminer la migration. Vous payez uniquement pour les ressources de stockage et de réseau utilisées pour la réplication, et pour les frais de calcul consommés pendant les migrations de test.
- Pour les machines virtuelles VMware, Azure Migrate fournit une migration sans agent, en plus de la migration basée sur un agent.
- Nous donnons la priorité aux nouvelles fonctionnalités de migration pour l’outil Azure Migrate : Migration de serveurs uniquement.

## <a name="when-to-use-site-recovery"></a>Quand utiliser Azure Site Recovery ?

Site Recovery doit être utilisé :

- Pour la récupération d’urgence de machines locales vers Azure.
- Pour la récupération d’urgence de machines virtuelles Azure entre des régions Azure.

Bien que nous vous recommandons d’utiliser Azure Migrate pour migrer des serveurs locaux vers Azure, si vous avez déjà commencé votre migration avec Site Recovery, vous pouvez continuer à l’utiliser pour terminer votre migration.  

## <a name="next-steps"></a>Étapes suivantes

> [Passez en revue les questions courantes](../migrate/resources-faq.md) sur Azure Migrate.
