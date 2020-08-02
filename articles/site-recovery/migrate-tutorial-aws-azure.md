---
title: Effectuer une migration des machines virtuelles AWS vers Azure avec le service Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment migrer des machines virtuelles Windows s’exécutant dans Amazon Web Services (AWS) vers Azure en utilisant Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281291"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrer des machines virtuelles Amazon Web Services (AWS) vers Azure

Cet article décrit les options de migration d’instances Amazon Web Services (AWS) vers Azure.

## <a name="migrate-with-azure-migrate"></a>Migrer avec Azure Migrate

Nous vous recommandons de migrer les instances AWS vers Azure à l’aide du service [Azure Migrate](../migrate/migrate-services-overview.md). Azure Migrate fournit un hub centralisé pour l’évaluation et la migration de machines locales vers Azure, à l’aide d’Azure Migrate, d’autres services Azure et d’outils tiers.

[Découvrez comment](../migrate/tutorial-migrate-aws-virtual-machines.md) migrer des instances AWS avec Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrer avec Site Recovery

Site Recovery ne doit être utilisé que pour la récupération d’urgence, pas pour la migration.

Si vous utilisez déjà Azure Site Recovery et souhaitez continuer à l’utiliser pour la migration AWS, procédez comme pour configurer la [récupération d’urgence de machines physiques](physical-azure-disaster-recovery.md).


> [!NOTE]
> Lorsque vous opérez un basculement à des fins de récupération d’urgence, lors de la dernière étape, vous validez le basculement. Lorsque vous migrez des instances AWS, l’option **Valider** n’est pas pertinente. Au lieu de cela, vous sélectionnez l’option **Terminer la migration**. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passez en revue les questions courantes](../migrate/resources-faq.md) sur Azure Migrate.
