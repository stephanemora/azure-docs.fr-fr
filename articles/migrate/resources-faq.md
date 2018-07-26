---
title: Azure Migrate - Forum aux questions | Microsoft Docs
description: Répond aux questions fréquemment posées sur Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: snehaa
ms.openlocfilehash: 3f035f38b1ad68e9e39d151ffad3fc650a0a1d80
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952747"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - Forum aux questions

Cet article contient les questions fréquemment posées sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Généralités

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Azure Migrate ne prend-il en charge que l’évaluation des charges de travail VMware ?

Oui, Azure Migrate ne prend actuellement en charge que l’évaluation des charges de travail VMware. La prise en charge de serveurs physiques et Hyper-V sera possible dans le futur.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate a-t-il besoin de vCenter Server pour découvrir un environnement VMware ?

Oui, Azure Migrate nécessite vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par un serveur vCenter.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>En quoi Azure Migrate diffère-t-il d’Azure Site Recovery ?

Azure Migrate est un service qui vous permet de découvrir vos charges de travail locales et de planifier votre migration vers Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), en plus de constituer une solution de reprise d’activité, vous permet de migrer des charges de travail locales vers des machines virtuelles IaaS dans Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Quelle est la différence entre l’utilisation d’Azure Migrate et de Map Toolkit pour les évaluations ?

[Azure Migrate](migrate-overview.md) offre une évaluation spécifique de la migration pour vous aider à la préparer et à évaluer les charges de travail locales dans Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) a d’autres fonctionnalités. Par exemple, la planification de la migration pour les versions plus récentes de systèmes d’exploitation Windows clients et serveurs, le suivi de l’utilisation des logiciels, etc. Pour ces scénarios, continuez à utiliser MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>En quoi Azure Migrate diffère-t-il du planificateur de déploiement Azure Site Recovery ?

Azure Migrate est un outil de planification de la migration et le planificateur de déploiement Azure Site Recovery est un outil de planification de reprise d’activité.

**Migration de VMware vers Azure** : si vous prévoyez de migrer vos charges de travail locales vers Azure, utilisez Azure Migrate pour la planification de la migration. Azure Migrate évalue les charges de travail locales, et fournit des conseils, des insights et des mécanismes pour vous aider à migrer vers Azure. Une fois que votre plan de migration est prêt, vous pouvez utiliser des services comme Azure Site Recovery et Azure Database Migration Service pour migrer les machines vers Azure.

**Migration d’Hyper-V vers Azure** : actuellement, Azure Migrate prend en charge seulement l’évaluation de machines virtuelles VMware pour la migration vers Azure. La prise en charge d’Hyper-V fait partie de la feuille de route d’Azure Migrate. Entre-temps, vous pouvez utiliser le planificateur de déploiement Site Recovery. Une fois la prise en charge d’Hyper-V effective dans Azure Migrate, vous pourrez utiliser Azure Migrate pour planifier la migration des charges de travail Hyper-V.

**Récupération d’urgence de VMware/Hyper-V vers Azure** : si vous prévoyez d’effectuer une récupération d'urgence sur Azure Site Recovery (Site Recovery), utilisez le planificateur de déploiement Azure Site Recovery pour la planifier. Le planificateur de déploiement Site Recovery effectue une évaluation approfondie spécifique à Azure Site Recovery de votre environnement local. Il fait des suggestions concernant ce qui est nécessaire pour que Site Recovery réussisse des opérations de récupération d’urgence, comme la réplication et le basculement de vos machines virtuelles.  

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Quelles sont les régions Azure prises en charge par Azure Migrate ?

Azure Migrate prend actuellement en charge les USA Est et les USA Centre-Ouest en tant qu’emplacements de projets de migration. Notez que même si vous ne pouvez créer des projets de migration que dans l’Ouest du centre des États-Unis et aux USA Est, vous pouvez toujours évaluer vos machines pour [plusieurs emplacements cible](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). L’emplacement du projet est uniquement utilisé pour stocker les données détectées.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Comment le site local se connecte-t-il à Azure Migrate ?

La connexion peut se faire via Internet, ou en utilisant ExpressRoute avec peering public.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Puis-je renforcer la machine virtuelle configurée avec le. Modèle .OVA ?

Des composants supplémentaires (par exemple, des antivirus) peuvent être ajoutés au modèle .OVA tant que les règles de communication et de pare-feu requises pour que l’appliance Azure Migrate fonctionne ne sont pas modifiées.   

## <a name="discovery-and-assessment"></a>Détection et évaluation

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

Pour la visualisation des dépendances, si vous installez des agents sur les machines virtuelles, les données collectées par ces agents sont stockées aux États-Unis dans un espace de travail OMS créé dans l’abonnement de l’utilisateur. Ces données sont supprimées lorsque vous supprimez l’espace de travail OMS dans votre abonnement. [Plus d’informations](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, les données collectées sont chiffrées à la fois au repos et en transit. Les métadonnées collectées par l’appliance sont envoyées de façon sécurisée au service Azure Migrate sur internet via HTTPS. Les métadonnées collectées sont stockées dans [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) et dans [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) dans un abonnement Microsoft, et sont chiffrées au repos.

Les données collectées par les agents de dépendance sont également chiffrées en transit (canal HTTPS sécurisé) et sont stockées dans un espace de travail Log Analytics dans l’abonnement de l’utilisateur. Elles sont également chiffrées au repos.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Comment le collecteur communique-t-il avec le serveur vCenter et le service Azure Migrate ?

L’appliance collecteur se connecte au serveur vCenter (port 443) en utilisant les informations d’identification fournies par l’utilisateur dans l’appliance. Elle interroge le serveur vCenter avec VMware PowerCLI pour collecter des métadonnées sur les machines virtuelles gérées par le serveur vCenter. Elle collecte auprès du serveur vCenter des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau, etc.), ainsi que l’historique des performances de chaque machine virtuelle pour le dernier mois. Les métadonnées collectées sont ensuite envoyées au service Azure Migrate (sur internet via HTTPS) pour évaluation. [En savoir plus](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Puis-je connecter la même appliance de collecteur à plusieurs serveurs vCenter ?

Oui, une seule appliance de collecteur peut être utilisée pour détecter plusieurs serveurs vCenter, mais pas simultanément. Vous devez exécuter les détections l’une après l’autre.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Le modèle .OVA utilisé par Site Recovery est-il intégré au modèle .OVA utilisé par Azure Migrate ?

À ce jour, il n’y a pas eu d’intégration. Le modèle .OVA dans Site Recovery est utilisé pour configurer un serveur de configuration Site Recovery pour la réplication de serveurs physiques/de machines virtuelles VMware. Le modèle .OVA utilisé par Azure Migrate permet de détecter des machines virtuelles VMware gérées par un serveur vCenter dans le cadre de l’évaluation de la migration.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>J’ai modifié la taille de ma machine. Puis-je réexécuter l’évaluation ?

Si vous modifiez les paramètres d’une machine que vous souhaitez évaluer, déclenchez à nouveau la détection avec l’appliance de collecteur. Pour ce faire, dans l’appliance, utilisez l’option **Redémarrer la collecte**. Une fois la collection terminée, sélectionnez l’option **Recalculer** pour effectuer l’évaluation dans le portail, pour obtenir des résultats d’évaluation de mise à jour.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Comment puis-je découvrir un environnement multilocataire dans Azure Migrate ?

Si vous avez un environnement qui est partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire dans l’abonnement d’un autre locataire, vous pouvez utiliser le champ Étendue dans l’appliance collecteur pour limiter la découverte. Si les locataires ont des hôtes en commun, créez des informations d’identification qui ont un accès en lecture seule uniquement aux machines virtuelles appartenant au locataire spécifique, puis utilisez ces informations d’identification dans l’appliance du collecteur et spécifiez comme étendue l’hôte où effectuer la détection. Vous pouvez aussi créer des dossiers dans le serveur vCenter (par exemple dossier1 pour locataire1 et dossier2 pour locataire2) sous l’hôte partagé, déplacer les machines virtuelles pour locataire1 dans dossier1 et pour locataire2 dans dossier2, puis limiter en conséquence les découvertes dans le collecteur en spécifiant le dossier approprié.

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
