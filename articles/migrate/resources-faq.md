---
title: Azure Migrate - Forum aux questions | Microsoft Docs
description: Répond aux questions fréquemment posées sur Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 366240c273feed559edb6e569640020046cc9471
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578643"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - Forum aux questions

Cet article contient les questions fréquemment posées sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Généralités

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Azure Migrate ne prend-il en charge que l’évaluation des charges de travail VMware ?

Oui, Azure Migrate ne prend actuellement en charge que l’évaluation des charges de travail VMware. Prise en charge pour Hyper-V est disponible en version préliminaire, veuillez vous inscrire [ici](https://aka.ms/migratefuture) pour accéder à la version préliminaire. Prise en charge pour les serveurs physiques est activée à l’avenir.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate a-t-il besoin de vCenter Server pour découvrir un environnement VMware ?

Oui, Azure Migrate nécessite vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par un serveur vCenter.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>En quoi Azure Migrate diffère-t-il d’Azure Site Recovery ?

Azure Migrate est un service qui vous permet de découvrir vos charges de travail locales et de planifier votre migration vers Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), en plus de constituer une solution de reprise d’activité, vous permet de migrer des charges de travail locales vers des machines virtuelles IaaS dans Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Quelle est la différence entre l’utilisation d’Azure Migrate et de Map Toolkit pour les évaluations ?

[Azure Migrate](migrate-overview.md) offre une évaluation spécifique de la migration pour vous aider à la préparer et à évaluer les charges de travail locales dans Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) a d’autres fonctionnalités, telles que la planification de la migration pour les versions plus récentes de systèmes d’exploitation Windows clients et serveurs ainsi que le suivi de l’utilisation des logiciels. Pour ces scénarios, continuez à utiliser MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>En quoi Azure Migrate diffère-t-il du planificateur de déploiement Azure Site Recovery ?

Azure Migrate est un outil de planification de la migration et le planificateur de déploiement Azure Site Recovery est un outil de planification de reprise d’activité.

**Migration de VMware vers Azure** : si vous prévoyez de migrer vos charges de travail locales vers Azure, utilisez Azure Migrate pour la planification de la migration. Azure Migrate évalue les charges de travail locales, et fournit des conseils, des insights et des mécanismes pour vous aider à migrer vers Azure. Une fois que votre plan de migration est prêt, vous pouvez utiliser des services comme Azure Site Recovery et Azure Database Migration Service pour migrer les machines vers Azure.

**Migration d’Hyper-V vers Azure** : La version à la disposition générale d’Azure Migrate prend actuellement en charge l’évaluation des machines virtuelles VMware pour la migration vers Azure. Prise en charge pour Hyper-V est actuellement en version préliminaire et prise en charge de production. Si vous souhaitez essayer la version d’évaluation, veuillez vous inscrire [ici](https://aka.ms/migratefuture).

**Récupération d’urgence de VMware/Hyper-V vers Azure** : si vous prévoyez d’effectuer une récupération d'urgence sur Azure Site Recovery (Site Recovery), utilisez le planificateur de déploiement Azure Site Recovery pour la planifier. Le planificateur de déploiement Site Recovery effectue une évaluation approfondie spécifique à Azure Site Recovery de votre environnement local. Il fait des suggestions concernant ce qui est nécessaire pour que Site Recovery réussisse des opérations de récupération d’urgence, comme la réplication et le basculement de vos machines virtuelles.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quelles sont les zones géographiques Azure prises en charge par Azure Migrate ?

Azure Migrate prend actuellement en charge l’Europe, les États-Unis et Azure Government comme zones géographiques de projet. Même si vous ne pouvez créer des projets de migration que dans ces zones, vous pouvez toujours évaluer vos machines pour [plusieurs emplacements cible](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). La zone géographique du projet est uniquement utilisée pour stocker les métadonnées détectées.

**Zone géographique** | **Emplacement de stockage des métadonnées**
--- | ---
Azure Government | Gouvernement américain - Virginie
Asie | Asie Sud-Est
Europe | Europe Nord ou Europe Ouest
États-Unis | USA Est ou USA Centre-Ouest

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Comment le site local se connecte-t-il à Azure Migrate ?

La connexion peut se faire via Internet, ou en utilisant ExpressRoute avec peering public.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Puis-je renforcer la machine virtuelle configurée avec le modèle OVA ?

Des composants supplémentaires (par exemple, des antivirus) peuvent être ajoutés au modèle OVA tant que les règles de communication et de pare-feu requises pour que l’appliance Azure Migrate fonctionne ne sont pas modifiées.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Pour renforcer l’appliance Azure Migrate, quelles sont les exclusions d’antivirus recommandées ?

Vous devez exclure les dossiers de l’appliance suivants pour l’analyse antivirus :

- Dossier contenant les fichiers binaires du service Azure Migrate. Exclure tous les sous-dossiers.
  %ProgramFiles%\ProfilerService  
- Application web Azure Migrate. Exclure tous les sous-dossiers.
  %SystemDrive%\inetpub\wwwroot
- Cache local pour la base de données et les fichiers journaux. Le service Azure Migrate nécessite un accès en lecture/écriture pour ce dossier.
  %SystemDrive%\Profiler

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

La détection d’agent est disponible sur la détection basée sur l’appliance et permet aux clients de [visualiser des dépendances](how-to-create-group-machine-dependencies.md) de l’ordinateur local des machines virtuelles. Les agents de dépendance collectent des informations détaillées comme le nom de domaine complet, le système d’exploitation, l’adresse IP, l’adresse MAC, les processus en cours d’exécution dans la machine virtuelle et les connexions TCP entrantes/sortantes de la machine virtuelle. La découverte basée sur les agents est facultative et vous pouvez choisir de ne pas installer les agents si vous ne voulez pas visualiser les dépendances des machines virtuelles.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Y aurait-il un impact sur les performances de l’environnement hôte ESXi analysé ?

Avec le profilage continue des données de performances, il est inutile de modifier le niveau de statistiques de serveur pour exécuter une évaluation en fonction des performances de vCenter. L’appliance collecteur sera profiler les machines locales pour mesurer les données de performances des machines virtuelles. Cela n’a pratiquement aucun impact sur les performances des hôtes ESXi et du serveur vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Où sont stockées les données collectées et pendant combien de temps ?

Les données collectées par l’appliance sont stockées à l’emplacement Azure que vous spécifiez lors de la création du projet de migration. Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées quand l’utilisateur supprime le projet Azure Migrate.

Pour la visualisation des dépendances, si vous installez des agents sur les machines virtuelles, les données collectées par ces agents sont stockées aux États-Unis dans un espace de travail Log Analytics créé dans l’abonnement de l’utilisateur. Ces données sont supprimées lorsque vous supprimez l’espace de travail Log Analytics de votre abonnement. [Plus d’informations](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

### <a name="what-is-the-volume-of-data-which-is-uploaded-by-azure-migrate-in-the-case-of-continuous-profiling"></a>Quel est le volume de données téléchargées par Azure Migrate en cas de profilage continu ?

Le volume de données qui est envoyé à Azure Migrate varie en fonction de plusieurs paramètres. Pour donner un nombre indicatif, un projet ayant dix machines (chacune dotée d’un disque et d’une carte réseau), enverrait environ 50 Mo par jour. Il s’agit d’une valeur approximative qui changerait en fonction du nombre de points de données pour les cartes réseau et les disques (les données envoyées seraient non linéaires si le nombre de machines, de cartes réseau ou de disques augmentait).

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

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Quelle différence y a-t-il entre le dimensionnement local et le dimensionnement basé sur les performances ?

Lorsque vous spécifiez le critère de dimensionnement sur le dimensionnement local, Azure Migrate ne tient pas compte des données de performances des machines virtuelles et dimensionne ces machines virtuelles en fonction de la configuration locale. Si le critère de dimensionnement est basé sur les performances, le dimensionnement est effectué en fonction des données d’utilisation. Par exemple, s’il existe une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, présentant 50 % d’utilisation du processeur et 50 % d’utilisation de la mémoire. Si le critère de dimensionnement est défini localement, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire. Cependant, si le critère de dimensionnement est défini en fonction des performances, il est recommandé d’utiliser une référence SKU de machine virtuelle avec 2 cœurs et 4 Go de mémoire, car le pourcentage d’utilisation est pris en compte lors de la recommandation de la taille. De même, pour les disques, le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage. Si le critère de dimensionnement est défini en fonction des performances et que le type de stockage est automatique, les IOPS et les valeurs de débit du disque sont prises en compte pour identifier le type de disque cible (Standard ou Premium). Si le critère de dimensionnement est défini en fonction des performances tandis que le type de stockage est Premium, il est recommandé d’utiliser un disque Premium, la référence SKU du disque Premium dans Azure est sélectionnée en fonction de la taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque lorsque le critère de dimensionnement est défini localement et que le type de stockage est Standard ou Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Quel est l’impact de l’utilisation de l’historique des performances et des centiles sur les suggestions de taille ?

Ces propriétés sont uniquement applicables pour le dimensionnement en fonction des performances. Azure Migrate collecte l’historique des performances des machines locales et l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure. L’appliance collecteur profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes. L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes. Pour créer le point de données unique, l’appliance sélectionne la valeur maximale de tous les échantillons de 20 secondes, puis l’envoie à Azure. Lorsque vous créez une évaluation dans Azure, en fonction de la durée des performances et de la valeur de centile de l’historique des performances, Azure Migrate calcule la meilleure valeur d’utilisation et l’utilise pour le dimensionnement. Par exemple, si vous avez défini la durée des performances à 1 jour et la valeur de centile à 95 centiles, Azure Migrate utilise des points d’échantillonnage de 15 minutes envoyés par le collecteur pour le dernier jour, les trie dans l’ordre croissant et récupère la valeur du 95e centile en tant que meilleure valeur d’utilisation. Le 95e centile permet de s’assurer que vous ignorez les aberrations, celles-ci pouvant apparaître si vous choisissez le 99e centile. Si vous désirez choisir l’utilisation maximale de la période et que vous ne souhaitez pas manquer les aberrations, vous devriez sélectionner le 99e centile.

## <a name="dependency-visualization"></a>Visualisation de dépendance

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

### <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

La visualisation des dépendances vous permet d’évaluer des groupes de machines virtuelles à des fins de migration avec plus de confiance, en effectuant une vérification croisée des dépendances entre machines avant de lancer une évaluation. La visualisation des dépendances vous aide à vérifier que rien n’est oublié afin d’éviter des interruptions inattendues lorsque vous migrez vers Azure. Azure Migrate s’appuie sur la solution Service Map dans les journaux d’Azure Monitor pour permettre la visualisation des dépendances.

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

[Voici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) un script que vous pouvez utiliser pour l’installation de l’agent de dépendances. [Voici](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) des instructions sur la façon d’installer MMA à l’aide de la ligne de commande ou de méthodes automatisées. Pour MMA, vous pouvez également exploiter un script disponible [ici](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) sur Technet.

En plus des scripts, vous pouvez également tirer parti des outils de déploiement tels que System Center Configuration Manager (SCCM), [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), etc. pour déployer les agents.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quels sont les systèmes d’exploitation pris en charge par MMA ?

La liste des systèmes d’exploitation Windows pris en charge par MMA est disponible [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
La liste des systèmes d’exploitation Linux pris en charge par MMA est disponible [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quels sont les systèmes d’exploitation pris en charge par l’agent de dépendances ?

La liste des systèmes d’exploitation Windows pris en charge par l’agent de dépendances est disponible [ici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
La liste des systèmes d’exploitation Linux pris en charge par l’agent de dépendances est disponible [ici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Puis-je visualiser des dépendances dans Azure Migrate pour une durée supérieure à une heure ?
Non. Azure Migrate vous permet de visualiser des dépendances avec une durée d’une heure maximum. Azure Migrate vous permet de revenir en arrière dans l’historique jusqu’à une date spécifique sur une période d’un mois, mais la durée maximale pour laquelle vous pouvez visualiser des dépendances est 1 heure. Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement pour une fenêtre d’une heure. Toutefois, vous pouvez utiliser les journaux Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) pendant une durée plus longue.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>La visualisation des dépendances est-elle prise en charge pour les groupes avec plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances de groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ayant 10 machines virtuelles au maximum. Si vous avez un groupe avec plus de 10 machines virtuelles, nous vous recommandons de diviser le groupe en groupes plus petits pour visualiser les dépendances.


## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble d’Azure Migrate](migrate-overview.md)
- Obtenir des informations sur la façon de [découvrir et d’évaluer](tutorial-assessment-vmware.md) un environnement VMware
