---
title: Architecture de l’appliance de réplication Azure Migrate
description: Fournit une vue d’ensemble de l’appliance de réplication Azure Migrate pour une migration basée sur un agent.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186556"
---
# <a name="replication-appliance"></a>Appliance de réplication

Cet article décrit l’appliance de réplication utilisée par Azure Migrate : Évaluation du serveur lors de la migration de machines virtuelles VMware, de machines physiques et de machines virtuelles de cloud privé/public vers Azure à l’aide d’une migration basée sur un agent. 

L’outil est disponible dans le hub [Azure Migrate](migrate-overview.md). Le hub fournit des outils natifs pour l’évaluation et la migration, ainsi que des outils d’autres services Azure et des éditeurs de logiciels indépendants (ISV) tiers.


## <a name="appliance-overview"></a>Vue d’ensemble de l’appliance

L’appliance de réplication est déployée en tant qu’ordinateur local unique, comme machine virtuelle VMware ou comme serveur physique. Il exécute :
- **Appliance de réplication** : L’appliance de réplication coordonne les communications et gère la réplication des données pour les machines virtuelles VMware locales et les serveurs physiques répliqués sur Azure.
- **Serveur de traitement**: Le serveur de processus, qui est installé par défaut sur l’appliance de réplication, effectue les opérations suivantes :
    - **Passerelle de réplication** : Fait office de passerelle de réplication. Il reçoit les données de réplication des machines activées pour la réplication. Optimise les données de réplication grâce à la mise en cache, la compression et le chiffrement et les envoie vers Azure.
    - **Programme d’installation de l’agent** : Effectue une installation d’envoi (push) du service Mobilité. Ce service doit être installé et en cours d’exécution sur chaque ordinateur local que vous souhaitez répliquer pour la migration.

## <a name="appliance-deployment"></a>Étapes de déploiement d’appliance

**Déployer en tant que** | **Utilisé pour** | **Détails**
--- | --- |  ---
Machine virtuelle VMware | Généralement utilisé lors de la migration de machines virtuelles VMware à l’aide de l’outil de migration Azure Migrate avec la migration basée sur l’agent. | Vous téléchargez le modèle OVA à partir du hub Azure Migrate, puis vous importez sur vCenter Server pour créer la machine virtuelle de l’appliance.
Une machine physique | Utilisé lors de la migration de serveurs physiques locaux si vous n’avez pas d’infrastructure VMware ou si vous ne pouvez pas créer une machine virtuelle VMware à l’aide d’un modèle OVA. | Vous téléchargez un programme d’installation de logiciel à partir du hub Azure Migrate et vous l’exécutez pour configurer la machine de l’appliance.

## <a name="appliance-deployment-requirements"></a>Exigences en matière de déploiement de l’appliance

[Revoyez](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) les exigences relatives au déploiement.



## <a name="appliance-license"></a>Licence de l’appliance
L’appliance est fournie avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours. Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation de la machine virtuelle de l’appliance.

## <a name="replication-process"></a>Processus de réplication

1. Lorsque vous activez la réplication pour une machine virtuelle, la réplication initiale vers Stockage Azure commence, conformément à la stratégie de réplication spécifiée. 
2. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet. La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.
3. Au terme de la réplication initiale, la réplication delta commence. Les modifications suivies pour une machine sont journalisées.
4. La communication s’effectue comme suit :
    - Les machines virtuelles communiquent avec l’appliance de réplication sur le port HTTPS 443 entrant, pour la gestion de la réplication.
    - L’appliance de réplication orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
    - Les machines virtuelles envoient des données de réplication au serveur de processus (en cours d’exécution sur l’appliance de réplication) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
    - Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.
5. Les données de réplication se trouvent tout d’abord dans un compte de stockage de cache dans Azure. Ces journaux sont traités et les données sont stockées dans un disque managé Azure.

![Architecture](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Mises à niveau d’appliance

L’appliance est mise à niveau manuellement à partir du hub Azure Migrate. Nous vous recommandons de toujours exécuter la dernière version.

1. Dans Azure Migrate > Servers > Azure Migrate : Évaluation du serveur, serveurs d’infrastructure, cliquez sur **Serveurs de configuration**.
2. Dans les **Serveurs de configuration**, un lien apparaît dans la **Version de l’agent** lorsqu’une nouvelle version de l’appliance de réplication est disponible. 
3. Téléchargez le programme d’installation sur la machine de l’appliance de réplication et installez la mise à niveau. Le programme d’installation détecte la version actuelle en cours d’exécution sur l’appliance.
 
## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](tutorial-assess-vmware.md#set-up-the-appliance-vm) configurer l’appliance pour VMware.
[Découvrez comment](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) configurer l’appliance pour Hyper-V.

