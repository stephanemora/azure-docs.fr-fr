---
title: Nous retirerons les machines virtuelles Azure Classic le 1er mars 2023
description: Cet article fournit une vue d’ensemble de haut niveau du retrait des machines virtuelles Classic
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 8c3e55d13c0b5fbf5b813f1669587836bf8b5e2d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503011"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrez vos ressources IaaS vers Azure Resource Manager avant le 1er mars 2023 

En 2014, nous avons lancé IaaS sur Azure Resource Manager et nous en avons depuis amélioré les fonctionnalités. Étant donné qu’[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) intègre désormais des fonctionnalités IaaS complètes et d’autres avancées, nous avons déconseillé la gestion des machines virtuelles IaaS via Azure Service Manager le 28 février 2020, et cette fonctionnalité sera entièrement supprimée le 1er mars 2023. 

Aujourd’hui, environ 90 % des machines virtuelles IaaS utilisent Azure Resource Manager. Si vous utilisez des ressources IaaS via Azure Service Manager (ASM), commencez à planifier votre migration dès maintenant, et achevez-la pour le 1er mars 2023 afin de tirer parti d’[Azure Resource Manager](../azure-resource-manager/management/index.yml).

Les machines virtuelles Classic suivent la [stratégie de cycle de vie moderne](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pour leur mise hors service.

## <a name="how-does-this-affect-me"></a>Dans quelle mesure cela m’affecte-t-il ? 

1) Depuis le 28 février 2020, les clients qui n’utilisaient pas de machine virtuelle IaaS via Azure Service Manager (ASM) au mois de février 2020 ne peuvent plus créer de machines virtuelles Classic. 
2) Le 1er mars 2023, les clients pourront plus démarrer des machines virtuelles IaaS à l’aide d’Azure Service Manager et celles qui seront toujours en cours d’exécution ou allouées seront arrêtées et libérées. 
2) Le 1er mars 2023, les abonnements qui n’auront pas migré vers Azure Resource Manager seront informés du calendrier de suppression des machines virtuelles Classic restantes.  

Les services et fonctionnalités Azure suivants ne seront **PAS** affectés par cette mise hors service : 
- Cloud Services 
- Comptes de stockage **non** utilisés par des machines virtuelles classiques 
- Réseaux virtuels **non** utilisés par des machines virtuelles Classic. 
- Autres ressources Classic

## <a name="what-actions-should-i-take"></a>Que dois-je faire ? 

- Commencez dès aujourd’hui la planification de votre migration vers Azure Resource Manager. 

- [Apprenez-en davantage](./windows/migration-classic-resource-manager-overview.md) sur la migration de vos machines virtuelles [Linux](./linux/migration-classic-resource-manager-plan.md) et [Windows](./windows/migration-classic-resource-manager-plan.md) Classic vers Azure Resource Manager.

- Pour plus d’informations, voir [Questions fréquemment posées sur la migration de Classic vers Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md)

- Pour toute question technique, tout problème et l’ajout d’abonnements à la liste verte, [contacter le support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Pour toute autre question ne figurant pas dans le FAQ et les commentaires, commentez ci-dessous.
