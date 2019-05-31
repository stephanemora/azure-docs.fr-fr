---
title: Sur la configuration Azure Site Recovery, les processus et les serveurs cibles maîtres | Microsoft Docs
description: Cet article fournit une vue d’ensemble de la configuration, les processus et les serveurs cible maître à l’aide lors de la récupération d’urgence de machines virtuelles de VMware locales vers Azure avec Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417744"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sur les composants de Site Recovery (configuration, processus, serveur cible maître)

Cet article décrit la configuration, les processus et les serveurs cibles maîtres utilisés lors de la réplication des machines virtuelles VMware et des serveurs physiques vers Azure avec le [Site Recovery](site-recovery-overview.md) service.

## <a name="configuration-server"></a>Serveur de configuration

Récupération d’urgence de machines virtuelles VMware locales sur des serveurs physiques, vous avez besoin d’une récupération de Site serveur de configuration déployé en local.

**Paramètre** | **Détails** | **Links**
--- | --- | ---
**Composants**  | L’ordinateur de serveur de configuration s’exécute tous les composants de Site Recovery en local, qui incluent le serveur de configuration, le serveur de processus et le serveur cible maître.<br/><br/> Lorsque vous configurez le serveur de configuration, tous les composants sont installés automatiquement. | [Lecture](vmware-azure-common-questions.md#configuration-server) le Forum aux questions du serveur de configuration.
**Rôle** | Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données. | En savoir plus sur l’architecture de [VMware](vmware-azure-architecture.md) et [serveur physique](physical-azure-architecture.md) récupération d’urgence vers Azure.
**Conditions requises VMware** | Récupération d’urgence de machines virtuelles de VMware en local, vous devez installer et exécuter le serveur de configuration comme un local, hautement disponible VMware VM. | [En savoir plus sur](vmware-azure-deploy-configuration-server.md#prerequisites) les conditions préalables.
**Déploiement de VMware** | Nous vous recommandons de déployer le serveur de configuration à l’aide d’un modèle OVA téléchargé. Cette méthode fournit un simple moyen de configurer un serveur de configuration qui est conforme à toutes les exigences et prérequis.<br/><br/> Si pour une raison quelconque, vous ne parvenez pas à déployer une VM VMware à l’aide d’un modèle OVA, vous pouvez configurer les ordinateurs de serveur de configuration manuellement, comme décrit ci-dessous pour la récupération d’urgence de machines physiques. | [Déployer](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) avec un modèle de fichier OVA.
**Exigences des serveurs physiques** | Récupération d’urgence sur des serveurs physiques en local, vous déployez le serveur de configuration manuellement. | [En savoir plus sur](physical-azure-set-up-source.md#prerequisites) les conditions préalables.
**Déploiement de serveur physique** | S’il ne peut pas être installé comme une VM VMware, vous pouvez l’installer sur un serveur physique. | [Déployer](physical-azure-set-up-source.md#set-up-the-source-environment) le serveur de configuration manuellement.


## <a name="process-server"></a>Serveur de traitement

**Paramètre** | **Détails** | **Links**
--- | --- | ---
**Déploiement**  | Récupération d’urgence et la réplication de machines virtuelles VMware locales sur des serveurs physiques, vous avez besoin d’un serveur de processus en local. Par défaut, le serveur de processus est installé sur le serveur de configuration lors de son déploiement. | [Plus d’informations](vmware-azure-architecture.md?#architectural-components)
**Rôle (en local** | -Reçoit les données de réplication de machines activées pour la réplication.<br/> -Optimise les données de réplication avec la mise en cache, la compression et le chiffrement et l’envoie au stockage Azure.<br/> -Effectue une installation push du Service mobilité Site Recovery sur des machines virtuelles VMware en local et des serveurs physiques que vous souhaitez répliquer.<br/> -Assure la détection automatique des machines locales. | [Plus d’informations](vmware-physical-azure-config-process-server-overview.md#process-server) 
**Rôle (restauration automatique à partir d’Azure)** | Après le basculement à partir de votre site local, vous définissez un serveur de processus dans Azure, comme une machine virtuelle Azure, pour gérer la restauration automatique vers votre emplacement local.<br/><br/> Le serveur de processus dans Azure est temporaire. La machine virtuelle Azure peut être supprimée une fois la restauration automatique est effectuée. | [Plus d’informations](vmware-azure-set-up-process-server-azure.md)
**Mise à l'échelle** | Pour les déploiements plus importants, en local que vous pouvez définir des serveurs de traitement supplémentaires, la montée en puissance. Serveurs supplémentaires montent en charge de la capacité, en gérant les plus grands nombres de réplication des machines et des volumes plus importants de trafic de réplication.<br/><br/> Vous pouvez déplacer des machines entre deux serveurs de processus, afin d’équilibrer le trafic de réplication. | [En savoir plus](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Serveur cible maître

Le serveur cible maître gère les données de réplication pendant la restauration automatique à partir d’Azure.

- Il est installé par défaut sur le serveur de configuration.
- Pour les déploiements à grande échelle, vous pouvez ajouter un serveur cible maître distinct à des fins de restauration automatique.


## <a name="next-steps"></a>Étapes suivantes
- Examinez le [architecture](vmware-azure-architecture.md) pour la récupération d’urgence de machines virtuelles VMware et des serveurs physiques.
- Examinez le [exigences et prérequis](vmware-physical-azure-support-matrix.md) pour la récupération d’urgence de machines virtuelles VMware et des serveurs physiques vers Azure. 
