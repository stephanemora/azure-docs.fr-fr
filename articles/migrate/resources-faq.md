---
title: Azure Migrate - Forum aux questions | Microsoft Docs
description: Répond aux questions fréquemment posées sur Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 06/06/2018
ms.author: snehaa
ms.openlocfilehash: b18d2cecfd7556ad3f05d0f63435d16bc29ebab1
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825926"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - Forum aux questions

Cet article contient les questions fréquemment posées sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Généralités

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>En quoi Azure Migrate diffère-t-il d’Azure Site Recovery ?

Azure Migrate est un service qui vous permet de découvrir vos charges de travail locales et de planifier votre migration vers Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), en plus de constituer une solution de reprise d’activité, vous permet de migrer des charges de travail locales vers des machines virtuelles IaaS dans Azure. 

### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>En quoi Azure Migrate diffère-t-il du planificateur de déploiement Azure Site Recovery ?

Azure Migrate est un outil de planification de la migration et le planificateur de déploiement Azure Site Recovery est un outil de planification de reprise d’activité.

**Migration de VMware vers Azure** : si vous prévoyez de migrer vos charges de travail locales vers Azure, utilisez Azure Migrate pour la planification de la migration. Azure Migrate évalue les charges de travail locales, et fournit des conseils, des insights et des mécanismes pour vous aider à migrer vers Azure. Une fois que votre plan de migration est prêt, vous pouvez utiliser des services comme Azure Site Recovery et Azure Database Migration Service pour migrer les machines vers Azure.

**Migration d’Hyper-V vers Azure** : actuellement, Azure Migrate prend en charge seulement l’évaluation de machines virtuelles VMware pour la migration vers Azure. La prise en charge d’Hyper-V fait partie de la feuille de route d’Azure Migrate. Entre-temps, vous pouvez utiliser le planificateur de déploiement Azure Site Recovery. Une fois la prise en charge d’Hyper-V effective dans Azure Migrate, vous pourrez utiliser Azure Migrate pour planifier la migration des charges de travail Hyper-V.

**Reprise d’activité de VMware/Hyper-V vers Azure** : si vous prévoyez d’effectuer la reprise d’activité sur Azure avec Azure Site Recovery, utilisez le planificateur de déploiement Azure Site Recovery pour la planification de la reprise d’activité. Le planificateur de déploiement Azure Site Recovery effectue une évaluation approfondie spécifique à Azure Site Recovery de votre environnement local. Il fournit des recommandations qui sont nécessaires à Azure Site Recovery pour la réussite des opérations de reprise d’activité, comme la réplication et le basculement de vos machines virtuelles.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate a-t-il besoin de vCenter Server pour découvrir un environnement VMware ?

Oui, Azure Migrate nécessite vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par un serveur vCenter.

## <a name="discovery"></a>Découverte

### <a name="what-data-is-collected-by-azure-migrate"></a>Quelles données sont collectées par Azure Migrate ?

Azure Migrate prend en charge deux types de découverte : les découvertes basées sur les appliances et les découvertes basées sur les agents.
La découverte basée sur les appliances collecte des métadonnées sur les machines virtuelles locales. Voici la liste complète des métadonnées collectées par l’appliance :

**Données de configuration de la machine virtuelle**
- Nom d’affichage de la machine virtuelle (sur vCenter)
- Chemin d’inventaire de la machine virtuelle (hôte/cluster/dossier dans vCenter)
- Adresse IP
- Adresse MAC
- Système d’exploitation
- Nombre de cœurs, disques, cartes réseau
- Taille de la mémoire, taille des disques

**Données de performances de la machine virtuelle**
- Utilisation de l’UC
- Utilisation de la mémoire
- Pour chaque disque attaché à la machine virtuelle :
  - Débit du disque en lecture
  - Débit du disque en écriture
  - Opérations de lecture du disque par seconde
  - Opérations d’écriture du disque par seconde
- Pour chaque carte réseau attachée à la machine virtuelle :
  - Entrée réseau
  - Sortie réseau

La découverte basée sur les agents est une option disponible pour la découverte basée sur les appliances, qui permet aux clients de [visualiser les dépendances](how-to-create-group-machine-dependencies.md) des machines virtuelles locales. Les agents de dépendance collectent des informations détaillées comme le nom de domaine complet, le système d’exploitation, l’adresse IP, l’adresse MAC, les processus en cours d’exécution dans la machine virtuelle et les connexions TCP entrantes/sortantes de la machine virtuelle. La découverte basée sur les agents est facultative et vous pouvez choisir de ne pas installer les agents si vous ne voulez pas visualiser les dépendances des machines virtuelles.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Où sont stockées les données collectées et pendant combien de temps ?

Les données collectées par l’appliance sont stockées à l’emplacement Azure que vous spécifiez lors de la création du projet de migration. Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées quand l’utilisateur supprime le projet Azure Migrate.

Pour la visualisation des dépendances, si vous installez des agents sur les machines virtuelles, les données collectées par ces agents sont stockées aux États-Unis dans un espace de travail OMS créé dans l’abonnement de l’utilisateur. Ces données sont supprimées dès que l’utilisateur supprime l’espace de travail OMS de son abonnement. [Plus d’informations](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Comment le collecteur communique-t-il avec le serveur vCenter et le service Azure Migrate ?

L’appliance collecteur se connecte au serveur vCenter (port 443) en utilisant les informations d’identification fournies par l’utilisateur dans l’appliance. Elle interroge le serveur vCenter avec VMware PowerCLI pour collecter des métadonnées sur les machines virtuelles gérées par le serveur vCenter. Elle collecte auprès du serveur vCenter des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau, etc.), ainsi que l’historique des performances de chaque machine virtuelle pour le dernier mois. Les métadonnées collectées sont ensuite envoyées au service Azure Migrate (sur internet via HTTPS) pour évaluation. [En savoir plus](concepts-collector.md)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, les données collectées sont chiffrées à la fois au repos et en transit. Les métadonnées collectées par l’appliance sont envoyées de façon sécurisée au service Azure Migrate sur internet via HTTPS. Les métadonnées collectées sont stockées dans [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) et dans [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) dans un abonnement Microsoft, et sont chiffrées au repos.

Les données collectées par les agents de dépendance sont également chiffrées en transit (canal HTTPS sécurisé) et sont stockées dans un espace de travail Log Analytics dans l’abonnement de l’utilisateur. Elles sont également chiffrées au repos.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Comment puis-je découvrir un environnement multilocataire dans Azure Migrate ?

Si vous avez un environnement qui est partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire dans l’abonnement d’un autre locataire, vous pouvez utiliser le champ Étendue dans l’appliance collecteur pour limiter la découverte. Si les locataires partagent des hôtes, créez des informations d’identification qui ont un accès en lecture seule uniquement aux machines virtuelles appartenant au locataire spécifique, puis utilisez ces informations d’identification dans l’appliance collecteur et spécifiez comme étendue l’hôte où effectuer la découverte. Vous pouvez aussi créer des dossiers dans le serveur vCenter (par exemple dossier1 pour locataire1 et dossier2 pour locataire2) sous l’hôte partagé, déplacer les machines virtuelles pour locataire1 dans dossier1 et pour locataire2 dans dossier2, puis limiter en conséquence les découvertes dans le collecteur en spécifiant le dossier approprié.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Combien de machines virtuelles peuvent être détectées dans un même projet de migration ?

Vous pouvez découvrir 1 500 machines virtuelles dans un même projet de migration. Si vous avez plus de machines que cela dans votre environnement local, [consultez cette section ](how-to-scale-assessment.md) pour obtenir des informations sur la façon dont vous pouvez découvrir un grand environnement dans Azure Migrate.

## <a name="dependency-visualization"></a>Visualisation de dépendance

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Dois-je payer pour utiliser la fonctionnalité de visualisation des dépendances ?

Azure Migrate est disponible sans coût supplémentaire. En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Puis-je utiliser un espace de travail existant pour la visualisation des dépendances ?

Azure Migrate ne prend pas en charge l’utilisation d’un espace de travail existant pour la visualisation des dépendances. Cependant, Microsoft Monitoring Agent (MMA) prend en charge l’hébergement multiple et vous permet d’envoyer des données à plusieurs espaces de travail. Ainsi, si les agents sont déjà déployés et configurés pour un espace de travail, vous pouvez tirer parti de l’hébergement multiple de l’agent MMA, configurer celui-ci pour l’espace de travail Azure Migrate (en plus de l’espace de travail existant) et le faire fonctionner. [Voici](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) un blog sur la façon dont vous pouvez activer l’hébergement multiple dans un agent MMA.

## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble d’Azure Migrate](migrate-overview.md)
- Obtenir des informations sur la façon de [découvrir et d’évaluer](tutorial-assessment-vmware.md) un environnement VMware
