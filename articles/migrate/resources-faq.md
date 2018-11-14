---
title: Azure Migrate - Forum aux questions | Microsoft Docs
description: Répond aux questions fréquemment posées sur Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: snehaa
ms.openlocfilehash: 2f04fe103d010a64a77b7d80730cf80007c3c126
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256373"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - Forum aux questions

Cet article contient les questions fréquemment posées sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

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

Azure Migrate prend actuellement en charge les USA Est et les USA Centre-Ouest en tant qu’emplacements de projets de migration. Même si vous ne pouvez créer des projets de migration que dans les régions USA Centre-Ouest et USA Est, vous pouvez toujours évaluer vos machines pour [plusieurs emplacements cible](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). L’emplacement du projet est uniquement utilisé pour stocker les données détectées.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Comment le site local se connecte-t-il à Azure Migrate ?

La connexion peut se faire via Internet, ou en utilisant ExpressRoute avec peering public.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Puis-je renforcer la machine virtuelle configurée avec le modèle OVA ?

Des composants supplémentaires (par exemple, des antivirus) peuvent être ajoutés au modèle OVA tant que les règles de communication et de pare-feu requises pour que l’appliance Azure Migrate fonctionne ne sont pas modifiées.   

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

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Y aurait-il un impact sur les performances de l’environnement hôte ESXi analysé ?

Dans le cas de l’[approche de découverte ponctuelle](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods) en vue de collecter les données de performances, le niveau de statistiques doit être fixé à 3 sur le serveur vCenter. Vous collecterez ainsi une grande quantité de données de dépannage, qui seront stockées dans la base de données du serveur vCenter. Par conséquent, cela peut entraîner des problèmes de performances sur le serveur vCenter. L’impact est négligeable sur l’hôte ESXi.

Nous avons introduit le profilage continu des données de performances (en préversion). Avec le profilage continu, il n’est plus nécessaire de modifier le niveau de statistiques du serveur vCenter pour effectuer une évaluation basée sur les performances. L’appliance collector va maintenant profiler les machines localement pour mesurer les données de performance des machines virtuelles. Cela n’a pratiquement aucun impact sur les performances des hôtes ESXi et du serveur vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Où sont stockées les données collectées et pendant combien de temps ?

Les données collectées par l’appliance sont stockées à l’emplacement Azure que vous spécifiez lors de la création du projet de migration. Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées quand l’utilisateur supprime le projet Azure Migrate.

Pour la visualisation des dépendances, si vous installez des agents sur les machines virtuelles, les données collectées par ces agents sont stockées aux États-Unis dans un espace de travail Log Analytics créé dans l’abonnement de l’utilisateur. Ces données sont supprimées lorsque vous supprimez l’espace de travail Log Analytics de votre abonnement. [Plus d’informations](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, les données collectées sont chiffrées à la fois au repos et en transit. Les métadonnées collectées par l’appliance sont envoyées de façon sécurisée au service Azure Migrate sur internet via HTTPS. Les métadonnées collectées sont stockées dans [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) et dans [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) dans un abonnement Microsoft, et sont chiffrées au repos.

Les données collectées par les agents de dépendance sont également chiffrées en transit (canal HTTPS sécurisé) et sont stockées dans un espace de travail Log Analytics dans l’abonnement de l’utilisateur. Elles sont également chiffrées au repos.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Comment le collecteur communique-t-il avec le serveur vCenter et le service Azure Migrate ?

L’appliance collecteur se connecte au serveur vCenter (port 443) en utilisant les informations d’identification fournies par l’utilisateur dans l’appliance. Elle interroge le serveur vCenter avec VMware PowerCLI pour collecter des métadonnées sur les machines virtuelles gérées par le serveur vCenter. Elle collecte auprès du serveur vCenter des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau, etc.), ainsi que l’historique des performances de chaque machine virtuelle pour le dernier mois. Les métadonnées collectées sont ensuite envoyées au service Azure Migrate (sur internet via HTTPS) pour évaluation. [En savoir plus](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Puis-je connecter la même appliance de collecteur à plusieurs serveurs vCenter ?

Oui, une seule appliance de collecteur peut être utilisée pour détecter plusieurs serveurs vCenter, mais pas simultanément. Vous devez exécuter les détections l’une après l’autre.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Le modèle OVA utilisé par Site Recovery est-il intégré au modèle OVA utilisé par Azure Migrate ?

À ce jour, il n’y a pas eu d’intégration. Le modèle .OVA dans Site Recovery est utilisé pour configurer un serveur de configuration Site Recovery pour la réplication de serveurs physiques/de machines virtuelles VMware. Le modèle .OVA utilisé par Azure Migrate permet de détecter des machines virtuelles VMware gérées par un serveur vCenter dans le cadre de l’évaluation de la migration.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>J’ai modifié la taille de ma machine. Puis-je réexécuter l’évaluation ?

Si vous modifiez les paramètres d’une machine que vous souhaitez évaluer, déclenchez à nouveau la détection avec l’appliance de collecteur. Pour ce faire, dans l’appliance, utilisez l’option **Redémarrer la collecte**. Une fois la collection terminée, sélectionnez l’option **Recalculer** pour effectuer l’évaluation dans le portail, pour obtenir des résultats d’évaluation de mise à jour.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Comment puis-je découvrir un environnement multilocataire dans Azure Migrate ?

Si vous avez un environnement qui est partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire dans l’abonnement d’un autre locataire, vous pouvez utiliser le champ Étendue dans l’appliance collecteur pour limiter la découverte. Si les locataires ont des hôtes en commun, créez des informations d’identification qui ont un accès en lecture seule uniquement aux machines virtuelles appartenant au locataire spécifique, puis utilisez ces informations d’identification dans l’appliance du collecteur et spécifiez comme étendue l’hôte où effectuer la détection. Vous pouvez aussi créer des dossiers dans le serveur vCenter (par exemple dossier1 pour locataire1 et dossier2 pour locataire2) sous l’hôte partagé, déplacer les machines virtuelles pour locataire1 dans dossier1 et pour locataire2 dans dossier2, puis limiter en conséquence les découvertes dans le collecteur en spécifiant le dossier approprié.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Combien de machines virtuelles peuvent être détectées dans un même projet de migration ?

Vous pouvez découvrir 1 500 machines virtuelles dans un même projet de migration. Si vous avez plus de machines que cela dans votre environnement local, [consultez cette section ](how-to-scale-assessment.md) pour obtenir des informations sur la façon dont vous pouvez découvrir un grand environnement dans Azure Migrate.

## <a name="assessment"></a>Évaluation

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate prend-il en charge l’estimation de coût basée sur l’Accord Entreprise (EA) ?

Azure Migrate ne prend actuellement pas en charge les estimation de coût pour l’[offre Accord Entreprise](https://azure.microsoft.com/offers/enterprise-agreement-support/). La solution de contournement consiste à spécifier Paiement à l’utilisation pour l’offre, en précisant manuellement le pourcentage de remise (applicable à l’abonnement) dans le champ « Remise » des propriétés d’évaluation.

  ![Remise](./media/resources-faq/discount.png)
  

## <a name="dependency-visualization"></a>Visualisation des dépendances

### <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

La visualisation des dépendances vous permet d’évaluer des groupes de machines virtuelles à des fins de migration avec plus de confiance, en effectuant une vérification croisée des dépendances entre machines avant de lancer une évaluation. La visualisation des dépendances vous aide à vérifier que rien n’est oublié afin d’éviter des interruptions inattendues lorsque vous migrez vers Azure. Azure Migrate se sert de la solution Service Map dans Log Analytics pour permettre la visualisation des dépendances.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Dois-je payer pour utiliser la fonctionnalité de visualisation des dépendances ?

Non. En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Dois-je installer quelque chose pour la visualisation des dépendances ?

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez évaluer. 

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) doit être installé sur chaque machine.
- Le programme [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) doit être installé sur chaque machine.
- En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin ces agents sur les machines que vous souhaitez évaluer, sauf si vous utilisez la visualisation des dépendances.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Puis-je utiliser un espace de travail existant pour la visualisation des dépendances ?

Oui. Azure Migrate vous permet maintenant de joindre un espace de travail existant au projet de migration et de l’exploiter pour la visualisation des dépendances. [Plus d’informations](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>Puis-je exporter le rapport de visualisation des dépendances ?

Non. La visualisation des dépendances ne peut pas être exportée. Toutefois, étant donné qu’Azure Migrate utilise Service Map pour la visualisation des dépendances, vous pouvez utiliser les [API REST de Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) pour obtenir les dépendances au format JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Comment puis-je automatiser l’installation de Microsoft Monitoring Agent (MMA) et de l’agent de dépendances ?

[Voici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) un script que vous pouvez utiliser pour l’installation de l’agent de dépendances. Pour MMA, [voici](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) un script disponible sur TechNet que vous pouvez utiliser.

En plus des scripts, vous pouvez également tirer parti des outils de déploiement tels que System Center Configuration Manager (SCCM), [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), etc. pour déployer les agents.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quels sont les systèmes d’exploitation pris en charge par MMA ?

La liste des systèmes d’exploitation Windows pris en charge par MMA est disponible [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
La liste des systèmes d’exploitation Linux pris en charge par MMA est disponible [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quels sont les systèmes d’exploitation pris en charge par l’agent de dépendances ?

La liste des systèmes d’exploitation Windows pris en charge par l’agent de dépendances est disponible [ici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
La liste des systèmes d’exploitation Linux pris en charge par l’agent de dépendances est disponible [ici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Puis-je visualiser des dépendances dans Azure Migrate pour une durée supérieure à une heure ?
Non. Azure Migrate vous permet de visualiser des dépendances avec une durée d’une heure maximum. Azure Migrate vous permet de revenir en arrière dans l’historique jusqu’à une date spécifique sur une période d’un mois, mais la durée maximale pour laquelle vous pouvez visualiser des dépendances est 1 heure. Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement pour une fenêtre d’une heure.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>La visualisation des dépendances est-elle prise en charge pour les groupes avec plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances de groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ayant 10 machines virtuelles maximum. Si vous avez ce type de groupe, nous vous recommandons de diviser le groupe en groupes plus petits pour visualiser les dépendances.


## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble d’Azure Migrate](migrate-overview.md)
- Obtenir des informations sur la façon de [découvrir et d’évaluer](tutorial-assessment-vmware.md) un environnement VMware
