---
title: Informations sur les serveurs de configuration, les serveurs de processus et les serveurs cible maîtres Azure Site Recovery | Microsoft Docs
description: Cet article offre une vue d’ensemble sur l’utilisation des serveurs de configuration, des serveurs de processus et des serveurs cible maîtres lors de la configuration de la récupération d’urgence de machines virtuelles VMware locales sur Azure avec Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: d835eda044c2a6d1e0e7c678073711e45fde7395
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972147"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informations sur les composants Site Recovery (configuration, processus, cible maître)

Cet article décrit les serveurs de configuration, les serveurs de processus et les serveurs cible maîtres utilisés lors de la réplication de machines virtuelles VMware et de serveurs physiques sur Azure avec le service [Site Recovery](site-recovery-overview.md).

## <a name="configuration-server"></a>Serveur de configuration

Pour la récupération d’urgence de machines virtuelles VMware locales et de serveurs physiques, vous avez besoin d’un serveur de configuration Site Recovery déployé localement.

**Paramètre** | **Détails** | **Liens**
--- | --- | ---
**Composants**  | La machine serveur de configuration exécute tous les composants locaux de Site Recovery, y compris le serveur de configuration, le serveur de processus et le serveur cible maître.<br/><br/> Lorsque vous configurez le serveur de configuration, tous les composants sont installés automatiquement. | [Lisez](vmware-azure-common-questions.md#configuration-server) le Forum aux questions sur le serveur de configuration.
**Rôle** | Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données. | Découvrez l’architecture de la récupération d’urgence des [machines virtuelles VMware](vmware-azure-architecture.md) et des [serveurs physiques](physical-azure-architecture.md) dans Azure.
**Conditions requises VMware** | Pour la récupération d’urgence des machines virtuelles VMware locales, vous devez installer et exécuter le serveur de configuration en tant que machine virtuelle VMware locale hautement disponible. | [En savoir plus sur](vmware-azure-deploy-configuration-server.md#prerequisites) les conditions préalables.
**Déploiement de VMware** | Nous vous recommandons de déployer le serveur de configuration en utilisant un modèle OVA téléchargé. Cette méthode permet de configurer simplement un serveur de configuration qui répond à toutes les exigences et conditions préalables.<br/><br/> Si, pour une raison quelconque, vous ne parvenez pas à déployer une machine virtuelle VMware à l’aide d’un modèle OVA, vous pouvez configurer manuellement les machines du serveur de configuration, comme décrit ci-dessous pour la récupération d’urgence des machines physiques. | [Déployez](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) un modèle OVA.
**Exigences des serveurs physiques** | Pour la récupération d’urgence sur des serveurs physiques locaux, vous déployez le serveur de configuration manuellement. | [En savoir plus sur](physical-azure-set-up-source.md#prerequisites) les conditions préalables.
**Déploiement de serveur physique** | S’il ne peut pas être installé en tant que machine virtuelle VMware, vous pouvez l’installer sur un serveur physique. | [Déployez](physical-azure-set-up-source.md#set-up-the-source-environment) le serveur de configuration manuellement.


## <a name="process-server"></a>Serveur de traitement

**Paramètre** | **Détails** | **Liens**
--- | --- | ---
**Déploiement**  | Pour la récupération d’urgence et la réplication des machines virtuelles VMware locales et des serveurs physiques, vous avez besoin d’un serveur de processus sur site. Par défaut, le serveur de processus par défaut est installé sur le serveur de configuration lorsque vous le déployez. | [Plus d’informations](vmware-azure-architecture.md?#architectural-components)
**Rôle (local** | - Reçoit les données de réplication des machines activées pour la réplication.<br/> - Optimise les données de réplication grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.<br/> - Effectue une installation push Mobility Service d’Azure Site Recovery sur les machines virtuelles VMware locales et les serveurs physiques que vous voulez répliquer.<br/> - Effectue la découverte automatique des machines locales. | [Plus d’informations](vmware-physical-azure-config-process-server-overview.md#process-server) 
**Rôle (restauration automatique à partir de Microsoft Azure)** | Après le basculement à partir de votre site local, vous configurez un serveur de processus dans Azure, en tant que machine virtuelle Azure, pour gérer le basculement vers votre emplacement local.<br/><br/> Dans Azure, le serveur de traitement est temporaire. La machine virtuelle Azure peut être supprimée à l’issue de la restauration automatique. | [Plus d’informations](vmware-azure-set-up-process-server-azure.md)
**Mise à l'échelle** | Pour des déploiements plus importants, vous pouvez configurer sur site des serveurs de processus supplémentaires et évolutifs. Des serveurs supplémentaires augmentent la capacité en gérant un plus grand nombre de machines de réplication et un plus grand volume de trafic de réplication.<br/><br/> Vous pouvez déplacer les machines entre deux serveurs de processus afin d’équilibrer le trafic de réplication. | [En savoir plus](vmware-azure-set-up-process-server-scale.md).


## <a name="master-target-server"></a>Serveur cible maître

Le serveur cible maître gère les données de réplication pendant la restauration automatique à partir d’Azure.

- Cette fonctionnalité est installée par défaut sur le serveur de configuration.
- Pour les déploiements à grande échelle, vous pouvez ajouter un serveur cible maître distinct à des fins de restauration automatique.


## <a name="next-steps"></a>Étapes suivantes
- Examinez [l’architecture](vmware-azure-architecture.md) de la récupération d’urgence des machines virtuelles VMware et des serveurs physiques.
- Passez en revue les [exigences et les prérequis](vmware-physical-azure-support-matrix.md) de la récupération d’urgence des machines virtuelles VMware et des serveurs physiques vers Azure. 
