---
title: Exigences du serveur de configuration pour la récupération d’urgence VMware dans Azure Site Recovery.
description: Cet article concerne le support et les exigences qui sont associés au déploiement du serveur de configuration pour la récupération d’urgence VMware vers Azure avec Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: 71861ec8f3b1e10f0052fe46705b726f06e717dd
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527910"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Exigences du serveur de configuration pour la récupération d’urgence de VMware sur Azure

>[!NOTE]
> Les informations contenues dans cet article s’appliquent aux versions classiques d’Azure Site Recovery. Dans la préversion, pour la réplication des machines virtuelles, vous devez créer et utiliser l’appliance de réplication Azure Site Recovery. Pour obtenir des informations détaillées sur la configuration requise de l’appliance de réplication et sur son déploiement, [consultez cet article](deploy-vmware-azure-replication-appliance-preview.md).

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
