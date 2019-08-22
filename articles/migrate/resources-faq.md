---
title: Azure Migrate - Forum aux questions | Microsoft Docs
description: Répond aux questions fréquemment posées sur Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: bf591306220b2c8c1e6c8166686836d96432fc7d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856273"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - Forum aux questions

Cet article contient les questions fréquemment posées sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Généralités

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quelles sont les zones géographiques Azure prises en charge par Azure Migrate ?

Vous pouvez trouver la liste pour [VMware ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) et pour [Hyper-V ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>En quoi Azure Migrate diffère-t-il d’Azure Site Recovery ?

Azure Migrate offre un hub centralisé pour démarrer, exécuter et suivre la découverte, l’évaluation et la migration de machines et de charges de travail vers Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) est une solution de reprise d’activité. Azure Migrate Server Migration s’appuie sur Azure Site Recovery dans le back-end pour permettre des scénarios de migration dans le cas de migrations lift-and-shift de machines locales.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Appliance Azure Migrate (serveurs VMware/physiques)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Comment l’appliance Azure Migrate se connecte à Azure ?

La connexion peut se faire via Internet, ou en utilisant ExpressRoute avec peering public/Microsoft.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Quels sont les besoins de connectivité réseau pour Azure Migrate Server Assessment et Server Migration ?

Pour les URL et les ports nécessaires pour qu’Azure Migrate communique avec Azure, consultez les tableaux de prise en charge de [VMWare](migrate-support-matrix-vmware.md) et de [Hyper-V](migrate-support-matrix-hyper-v.md).

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>Puis-je renforcer la machine virtuelle de l’appliance que j’ai configurée avec le modèle ?

Des composants supplémentaires (par exemple des antivirus) peuvent être ajoutés au modèle, tant que les règles de communication et de pare-feu nécessaires pour l’appliance Azure Migrate ne sont pas modifiées.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Quelles données sont collectées par l’appliance Azure Migrate ?

Vous pouvez obtenir des détails sur les données collectées par l’appliance Azure Migrate [ici](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Y a-t-il un impact sur les performances de l’environnement VMware ou Hyper-V analysé ?

Avec le profilage continu des données de performances, l’appliance Azure Migrate profile les machines locales pour mesurer les données de performances des machines virtuelles. Ceci n’a pratiquement aucun impact sur les performances des hôtes Hyper-V/ESXi et du serveur vCenter.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Où sont stockées les données collectées et pendant combien de temps ?

Les données collectées par l’appliance Azure Migrate sont stockées à l’emplacement Azure que vous spécifiez lors de la création du projet de migration. Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées quand vous supprimez le projet Azure Migrate.

Pour la visualisation des dépendances, si vous installez des agents sur les machines virtuelles, les données collectées par ces agents sont stockées aux États-Unis, dans un espace de travail Log Analytics créé dans l’abonnement Azure. Ces données sont supprimées lorsque vous supprimez l’espace de travail Log Analytics de votre abonnement. [Plus d’informations](concepts-dependency-visualization.md)

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Quel est le volume de données téléchargées par l’appliance Azure Migrate pendant le profilage continu ?

Le volume de données qui est envoyé à Azure Migrate varie en fonction de plusieurs paramètres. À titre indicatif, un projet Azure Migrate avec 10 machines (chacune avec un disque et une carte réseau), envoie environ 50 Mo par jour. Il s’agit d’une valeur approximative, qui change en fonction du nombre de points de données pour les cartes réseau et les disques (les données envoyées sont non linéaires si le nombre de machines, de cartes réseau ou de disques augmente).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, les deux. Les métadonnées sont envoyées de façon sécurisée au service Azure Migrate sur Internet via HTTPS. Les métadonnées sont stockées dans une base de données [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) et dans [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) dans un abonnement Microsoft, et elles sont chiffrées au repos.

Les données collectées par les agents de dépendance sont également chiffrées en transit (HTTPS sécurisé) et sont stockées dans un espace de travail Log Analytics dans l’abonnement de l’utilisateur. Elles sont également chiffrées au repos.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Comment l’appliance Azure Migrate communique avec le serveur vCenter et le service Azure Migrate ?

L’appliance se connecte au serveur vCenter (port 443) en utilisant les informations d’identification fournies quand vous configurez l’appliance. Elle interroge le serveur vCenter avec VMware PowerCLI pour collecter des métadonnées sur les machines virtuelles gérées par le serveur vCenter. Elle collecte des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau, etc.) ainsi que l’historique des performances de chaque machine virtuelle pour le dernier mois. Les métadonnées collectées sont ensuite envoyées à Azure Migrate Server Assessment (sur internet via HTTPS) pour évaluation.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Puis-je connecter la même appliance à plusieurs serveurs vCenter ?

Non. Il existe un mappage un-à-un entre une appliance et le serveur vCenter Server. Si vous devez découvrir des machines virtuelles sur plusieurs serveurs vCenter Server, vous devez déployer plusieurs appliances. 


### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>J’ai modifié la taille de ma machine. Puis-je réexécuter une évaluation ?

L’appliance Azure Migrate collecte en permanence des informations sur l’environnement local. Cependant, une évaluation est une capture instantanée à un point dans le temps de machines virtuelles locales. Si vous modifiez les paramètres d’une machine virtuelle que vous voulez évaluer, utilisez l’option « Recalculer » pour mettre à jour l’évaluation avec les dernières modifications.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Comment découvrir un environnement multilocataire dans Azure Migrate Server Assessment ?

Pour VMware, si vous avez un environnement partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire dans l’abonnement d’un autre locataire, créez des informations d’identification vCenter Server avec un accès seulement aux machines virtuelles que vous voulez découvrir. Utilisez ensuite les informations d’identification lors du lancement de la découverte dans l’appliance Azure Migrate.

Pour Hyper-V, la découverte utilise les informations d’identification de l’hôte Hyper-V : si les machines virtuelles partagent le même hôte Hyper-V, il n’existe actuellement aucun moyen de séparer la découverte.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Combien de machines virtuelles peuvent être découvertes avec une seule appliance de migration ?

Vous pouvez découvrir jusqu’à 10 000 machines virtuelles VMware et jusqu’à 5 000 machines virtuelles Hyper-V avec une seule appliance de migration.  Si vous avez plus de machines dans votre environnement local, découvrez comment mettre à l’échelle l’évaluation [Hyper-V](scale-hyper-v-assessment.md) et [VMware](scale-vmware-assessment.md).

## <a name="azure-migrate-server-assessment"></a>Azure Migrate : Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate : Server Assessment prend-il charge l’évaluation de serveurs physiques ?

Non, Azure Migrate ne prend pas en charge l’évaluation des serveurs physiques actuellement.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate a-t-il besoin de vCenter Server pour découvrir un environnement VMware ?

Oui, Azure Migrate a besoin de vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Quelle est la différence entre l’utilisation d’Azure Migrate : Server Assessment et Map Toolkit ?

Azure Migrate : Server Assessment fournit une évaluation de la migration pour faciliter la préparation à la migration et l’évaluation des charges de travail pour la migration vers Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) a d’autres fonctionnalités, comme la planification de la migration pour les versions plus récentes de systèmes d’exploitation Windows clients et serveurs ainsi que le suivi de l’utilisation des logiciels. Pour ces scénarios, continuez à utiliser MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>En quoi Azure Migrate : Server Assessment diffère du planificateur de déploiement Azure Site Recovery ?

Azure Migrate : Server Assessment est un outil de planification de la migration. Le planificateur de déploiement Azure Site Recovery est un outil de planification de la reprise d’activité.

- **Migration depuis VMware/Hyper-V vers Azure** : Si vous prévoyez de migrer vos serveurs locaux vers Azure, utilisez l’outil Azure Migrate : Server Assessment pour la planification de la migration. L’outil évalue les charges de travail locales, et fournit des conseils, des insights et des mécanismes pour vous aider à migrer vers Azure. Une fois que votre plan de migration est prêt, vous pouvez utiliser des outils comme Azure Migrate : Server Migration pour migrer les machines vers Azure.
- **Récupération d’urgence de VMware/Hyper-V vers Azure** : Pour la reprise d’activité sur Azure avec Site Recovery, utilisez le planificateur de déploiement Site Recovery pour la planification de la reprise d’activité. Le planificateur de déploiement Site Recovery effectue une évaluation approfondie spécifique à Site Recovery de votre environnement local. Il fournit des recommandations quant à ce qui est nécessaire pour que Site Recovery réussisse des opérations de reprise d’activité, comme la réplication et le basculement de machines virtuelles.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate prend-t-il en charge l’estimation des coûts basée sur le Contrat Entreprise ?

Azure Migrate ne prend actuellement pas en charge l’estimation des coûts pour l’[offre Contrat Entreprise](https://azure.microsoft.com/offers/enterprise-agreement-support/). La solution de contournement consiste à spécifier « Paiement à l’utilisation » pour l’offre, et à spécifier manuellement le pourcentage de remise (applicable à l’abonnement) dans le champ « Remise » des propriétés d’évaluation.

  ![Remise](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Quelle est la différence entre le dimensionnement local et le dimensionnement basé sur les performances ?

- Dans un dimensionnement local, Azure Migrate ne tient pas compte des données de performances des machines virtuelles. Il redimensionne les machines virtuelles en fonction de la configuration locale. Dans le dimensionnement basé sur les performances, le dimensionnement est basé sur les données d’utilisation.
- Par exemple, si une machine virtuelle locale a 4 cœurs et une mémoire de 8 Go, avec une utilisation du processeur de 50 % et une utilisation de la mémoire de 50 %, le dimensionnement local recommande une référence SKU de machine virtuelle Azure avec quatre cœurs et 8 Go de mémoire. Le dimensionnement basé sur les performances recommande cependant une référence SKU de machine virtuelle de deux cœurs et de 4 Go, car le pourcentage d’utilisation est pris en compte.
- De même, le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
= Si le critère de dimensionnement est défini en fonction des performances et que le type de stockage est automatique, les IOPS et les valeurs de débit du disque sont prises en compte pour identifier le type de disque cible (Standard ou Premium).
- Si le critère de dimensionnement est basé sur les performances et que le type de stockage est Premium, un disque Premium est recommandé. La référence SKU de disque Premium est sélectionnée en fonction de la taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est le dimensionnement locale et que le type de stockage est Standard ou Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Quel est l’impact de l’utilisation de l’historique des performances et des centiles sur les suggestions de taille ?

Ces propriétés sont uniquement applicables pour le dimensionnement en fonction des performances.

- Azure Migrate collecte l’historique des performances des machines locales et l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure.
- L’appliance profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes. L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes. Pour créer le point de données unique, l’appliance sélectionne la valeur maximale de tous les échantillons de 20 secondes, puis l’envoie à Azure.
- Quand vous créez une évaluation dans Azure (en fonction de la durée des performances et de la valeur de centile de l’historique des performances), Azure Migrate calcule la valeur d’utilisation effective et l’utilise pour le dimensionnement.
- Par exemple, si vous définissez la durée des performances à un jour et la valeur de centile à 95 centiles, Azure Migrate utilise les points d’échantillonnage de 15 minutes envoyés par le collecteur pour le dernier jour, les trie dans l’ordre croissant et récupère la valeur du 95e centile comme utilisation effective.
- La valeur du 95e centile garantit que vous ignorez les aberrations, celles-ci pouvant apparaître si vous choisissez le 99e centile. Si vous désirez choisir l’utilisation maximale de la période et que vous ne souhaitez pas manquer les aberrations, vous devriez sélectionner le 99e centile.

### <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

La visualisation des dépendances vous permet d’évaluer les groupes de machines virtuelles pour la migration avec une plus grande confiance. Elle vérifie les dépendances croisées des machines avant d’exécuter une évaluation. La visualisation des dépendances vous aide à vérifier que rien n’est oublié, et à éviter des interruptions inattendues quand vous migrez vers Azure. Azure Migrate s’appuie sur la solution Service Map dans les journaux Azure Monitor pour permettre la visualisation des dépendances.

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Dois-je payer pour utiliser la fonctionnalité de visualisation des dépendances ?

Non. [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Dois-je installer quelque chose pour la visualisation des dépendances ?

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez évaluer.

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) doit être installé sur chaque machine.
- Le programme [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) doit être installé sur chaque machine.
- En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin de ces agents, sauf si vous utilisez la visualisation des dépendances.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Puis-je utiliser un espace de travail existant pour la visualisation des dépendances ?

Oui, vous pouvez attacher un espace de travail existant au projet de migration et l’exploiter pour la visualisation des dépendances. [Plus d’informations](concepts-dependency-visualization.md#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>Puis-je exporter le rapport de visualisation des dépendances ?

Non, la visualisation des dépendances ne peut pas être exportée. Toutefois, étant donné qu’Azure Migrate utilise Service Map pour la visualisation des dépendances, vous pouvez utiliser les [API REST de Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) pour obtenir les dépendances au format JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Comment puis-je automatiser l’installation de Microsoft Monitoring Agent (MMA) et de l’agent de dépendances ?

[Utilisez ce script](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) pour l’installation des agents. [Suivez ces instructions](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) pour installer MMA en utilisant la ligne de commande ou l’automation. Pour MMA, utilisez [ce script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

En plus des scripts, vous pouvez utiliser des outils de déploiement comme System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), etc. pour déployer les agents.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quels systèmes d’exploitation sont pris en charge par MMA ?

- [Passez en revue](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) la liste des systèmes d’exploitation Windows pris en charge par MMA.
- [Passez en revue](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) la liste des systèmes d’exploitation Linux pris en charge par MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Quels sont les systèmes d’exploitation pris en charge par Dependency Agent ?

[Passez en revue](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) les systèmes d’exploitation Windows pris en charge par Dependency Agent.
[Passez en revue](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) la liste des systèmes d’exploitation Linux pris en charge par Dependency Agent.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Puis-je visualiser des dépendances dans Azure Migrate pour une durée supérieure à une heure ?
Non, vous pouvez visualiser les dépendances pour jusqu’à une heure. Vous pouvez revenir à une date particulière dans l’historique, jusqu’au dernier mois, mais la durée maximale de visualisation est d’une heure. Par exemple, vous pouvez utiliser la durée dans la carte des dépendances pour voir les dépendances d’hier, mais seulement pour une fenêtre d’une heure. Vous pouvez cependant utiliser les journaux Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une durée plus longue.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>La visualisation des dépendances est-elle prise en charge pour les groupes avec plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances pour des groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) contenant jusqu’à 10 machines virtuelles. Si vous avez un groupe avec plus de 10 machines virtuelles, nous vous recommandons de diviser le groupe en groupes plus petits, puis de visualiser les dépendances.

## <a name="azure-migrate-server-migration"></a>Azure Migrate : Server Migration

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>En quoi Azure Migrate : Server Migration diffère d’Azure Site Recovery ?

Azure Migrate : Server Migration s’appuie sur le moteur de réplication de Site Recovery pour la migration basée sur les agents des machines virtuelles VMware, la migration des machines virtuelles Hyper-V et la migration des serveurs physiques vers Azure. L’option sans agent pour migrer des machines virtuelles VMware est intégrée en mode natif dans Server Migration.

## <a name="next-steps"></a>Étapes suivantes
Lire la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md)
