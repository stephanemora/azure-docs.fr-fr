---
title: Exigences du serveur de configuration pour la récupération d’urgence de VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article décrit le support et les exigences lors du déploiement du serveur de configuration pour la récupération d’urgence de VMware sur Azure avec Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: d83b99ea540d6232f4c0786d3a743f97332e1c9f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792317"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Exigences du serveur de configuration pour la récupération d’urgence de VMware sur Azure

Vous devez déployer un serveur de configuration local quand vous utilisez [Azure Site Recovery](site-recovery-overview.md) pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure.

- Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure. Il gère également la réplication des données.
- [Découvrez-en plus](vmware-azure-architecture.md) sur les composants et les processus du serveur de configuration.

## <a name="configuration-server-deployment"></a>Déploiement du serveur de configuration

Pour la reprise d’activité de machines virtuelles VMware sur Azure, vous devez déployer un serveur de configuration en tant que machine VMware.

- Site Recovery fournit un modèle OVA que vous téléchargez à partir du portail Azure et importez dans vCenter Server pour configurer la machine virtuelle du serveur de configuration.
- Lorsque vous déployez le serveur de configuration à l’aide du modèle OVA, la machine virtuelle répond automatiquement aux exigences énumérées dans cet article.
- Nous vous recommandons fortement de configurer le serveur de configuration à l’aide du modèle OVA. Toutefois, si vous configurez une reprise d’activité pour les machines virtuelles VMware mais que vous ne pouvez pas utiliser le modèle OVA, vous pouvez déployer le serveur de configuration en utilisant [ces instructions](physical-azure-set-up-source.md).
- Si vous déployez le serveur de configuration pour la reprise d’activité de machines physiques locales sur Azure, suivez les instructions de [cet article](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Étapes suivantes
Configurez la récupération d’urgence des [machines virtuelles VMware](vmware-azure-tutorial.md) vers Azure.
