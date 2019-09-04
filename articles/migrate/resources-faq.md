---
title: Azure Migrate - Forum aux questions (FAQ) | Microsoft Docs
description: Répond aux questions fréquemment posées sur Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: af95ad892b62cb5d8bece554d6026525d9279777
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102868"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate : Forum Aux Questions (FAQ)

Cette page répond aux questions fréquentes à propos d’Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Généralités

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Quelles sont les zones géographiques Azure qui sont prises en charge par Azure Migrate ?

Consultez les listes pour [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) et [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Quelle est la différence entre Azure Migrate et Azure Site Recovery ?

Azure Migrate offre un hub centralisé pour démarrer votre migration, exécuter et suivre la découverte, l’évaluation de machines et de charges de travail, et exécuter et suivre la migration de machines et de charges de travail vers Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) est une solution de reprise d’activité. Azure Migrate Server Migration utilise Azure Site Recovery sur le serveur principal pour permettre des scénarios de migration dans le cas de migrations lift-and-shift de machines locales.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Appliance Azure Migrate (serveurs VMware/physiques)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Comment l’appliance Azure Migrate se connecte à Azure ?

La connexion peut se faire via Internet, ou en utilisant Azure ExpressRoute avec peering public/Microsoft.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Quels sont les besoins de connectivité réseau pour l'évaluation et la migration de serveur Azure Migrate ?

Consultez les matrices de prise en charge [VMware](migrate-support-matrix-vmware.md) et [Hyper-V](migrate-support-matrix-hyper-v.md) pour des informations sur les URL et les ports nécessaires afin de permettre à Azure Migrate de communiquer avec Azure.

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Puis-je renforcer la machine virtuelle de l’appliance que j’ai configurée avec le modèle ?

Vous pouvez ajouter des composants (par exemple des antivirus) au modèle, tant que vous ne modifiez pas les règles de communication et de pare-feu nécessaires pour l’appliance Azure Migrate.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Quelles données sont collectées par l’appliance Azure Migrate ?

Vous pouvez afficher les détails relatifs aux données collectées par l'appliance Azure Migrate [dans la documentation Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Y a-t-il un impact sur les performances de l’environnement VMware ou Hyper-V analysé ?

L’appliance Azure Migrate profile les machines locales pour mesurer continuellement les données de performances des machines virtuelles. Ce profilage n’a pratiquement aucun impact sur les performances des hôtes Hyper-V/ESXi ou vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Où sont stockées les données collectées et pendant combien de temps ?

Les données collectées par l’appliance Azure Migrate sont stockées à l’emplacement Azure que vous choisissez lorsque vous créez le projet de migration. Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées lorsque vous supprimez le projet Azure Migrate.

Pour la visualisation des dépendances, si vous installez des agents sur les machines virtuelles, les données collectées par ces agents sont stockées aux États-Unis, dans un espace de travail Log Analytics créé dans l’abonnement Azure. Ces données sont supprimées lorsque vous supprimez l’espace de travail Log Analytics de votre abonnement. Pour plus d’informations, consultez [Visualisation des dépendances](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Quel est le volume de données téléchargées par l’appliance Azure Migrate lors du profilage continu ?

Le volume de données envoyé à Azure Migrate varie selon plusieurs paramètres. À titre d'exemple, un projet Azure Migrate avec 10 machines (chacune avec un disque et une carte réseau), envoie environ 50 Mo par jour. Cette valeur est une approximation. Elle change en fonction du nombre de points de données des cartes réseau et disques. (L’augmentation des données envoyées n'est pas linéaire si le nombre d’ordinateurs, de cartes réseau ou de disques augmente.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, dans ces deux cas. Les métadonnées sont envoyées de façon sécurisée au service Azure Migrate sur Internet via HTTPS. Les métadonnées sont stockées dans une base de données [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) et dans [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) au sein d'un abonnement Microsoft. Les métadonnées sont chiffrées au repos.

Les données collectées par les agents de dépendances sont également chiffrées en transit (HTTPS sécurisé). Elles sont stockées dans un espace de travail Log Analytics dans votre abonnement. Elles sont également chiffrées au repos.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Comment l’appliance Azure Migrate communique-t-elle avec le vCenter Server et le service Azure Migrate ?

L’appliance se connecte vCenter Server (port 443) en utilisant les informations d’identification fournies lors de sa configuration. Elle utilise VMware PowerCLI pour interroger vCenter Server et collecter des métadonnées sur les machines virtuelles gérées par vCenter Server. Elle collecte des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau, etc.) ainsi que l’historique des performances de chaque machine virtuelle pour le dernier mois. Les métadonnées collectées sont ensuite envoyées à Azure Migrate Server Assessment (sur internet via HTTPS) pour évaluation.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Puis-je connecter la même appliance à plusieurs instances vCenter Server ?

Non. Il existe un mappage un-à-un entre une appliance et le serveur vCenter Server. Si vous devez découvrir des machines virtuelles sur plusieurs instances vCenter Server, vous devez déployer plusieurs appliances.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>J’ai modifié la taille de ma machine. Puis-je réexécuter l’évaluation ?

L’appliance Azure Migrate collecte en permanence des informations sur l’environnement local. Cependant, une évaluation est une capture instantanée à une date et heure de machines virtuelles locales. Si vous modifiez les paramètres d’une machine virtuelle que vous voulez évaluer, utilisez l’option « Recalculer » pour mettre à jour l’évaluation avec les dernières modifications.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Comment découvrir un environnement multilocataire dans Azure Migrate Server Assessment ?

Pour VMware, si vous avez un environnement partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire dans l’abonnement d’un autre locataire, créez des informations d’identification vCenter Server avec un accès seulement aux machines virtuelles que vous voulez découvrir. Utilisez ensuite ces informations d’identification lorsque vous lancez la détection dans l’appliance Azure Migrate.

Pour Hyper-V, la détection utilise les informations d’identification de l’hôte Hyper-V. Si les machines virtuelles partagent le même hôte Hyper-V, aucun moyen ne permet de séparer la détection.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Combien de machines virtuelles peuvent-elles être découvertes avec une seule appliance de migration ?

Vous pouvez découvrir jusqu’à 10 000 machines virtuelles VMware et jusqu’à 5 000 machines virtuelles Hyper-V avec une seule appliance de migration. Si vous avez plus de machines dans votre environnement local, découvrez comment mettre à l’échelle l’évaluation [Hyper-V](scale-hyper-v-assessment.md) et [VMware](scale-vmware-assessment.md).

## <a name="azure-migrate-server-assessment"></a>Évaluation de serveur Azure Migrate

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate Server Assessment prend-il charge l’évaluation de serveurs physiques ?

Non, actuellement, Azure Migrate ne prend pas en charge l’évaluation des serveurs physiques.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Azure Migrate a-t-il besoin de vCenter Server pour découvrir un environnement VMware ?

Oui, Azure Migrate requiert vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Quelle est la différence entre Azure Migrate Server Assessment et Map Toolkit ?

Azure Migrate Server Assessment fournit une évaluation de la migration pour faciliter la préparation à la migration et l’évaluation des charges de travail pour la migration vers Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) facilite d'autres tâches, telles que la planification de la migration pour les versions plus récentes de systèmes d’exploitation Windows clients et serveurs ainsi que le suivi de l’utilisation des logiciels. Pour ces scénarios, continuez à utiliser MAP Toolkit.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Quelle est la différence entre Azure Migrate Server Assessment et le planificateur de déploiement Azure Site Recovery ?

Azure Migrate Server Assessment est un outil de planification de la migration. Le planificateur de déploiement Azure Site Recovery est un outil de planification de la reprise d’activité.

**Migration depuis VMware/Hyper-V vers Azure** : si vous envisagez de migrer vos serveurs locaux vers Azure, utilisez Azure Migrate Server Assessment pour la planification de la migration. Il évalue les charges de travail locales, et fournit des conseils, des insights et des outils pour vous aider à migrer vos serveurs vers Azure. Une fois votre plan de migration prêt, vous pouvez utiliser des outils comme Azure Migrate Server Migration pour migrer les machines vers Azure.

**Récupération d’urgence de VMware/Hyper-V vers Azure** : Pour la reprise d’activité sur Azure via Site Recovery, utilisez le planificateur de déploiement Site Recovery à des fins de planification. Le planificateur de déploiement Site Recovery effectue une évaluation approfondie spécifique à Site Recovery de votre environnement local. Il fournit des recommandations quant à ce qui est nécessaire pour que Site Recovery réussisse des opérations de reprise d’activité, comme la réplication et le basculement de machines virtuelles.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Azure Migrate prend-t-il en charge l’estimation des coûts dans le cadre du programme Contrat Entreprise ?

Actuellement, Azure Migrate ne prend pas en charge l’estimation des coûts dans la cadre du [programme Contrat Entreprise](https://azure.microsoft.com/offers/enterprise-agreement-support/). La solution de contournement consiste à spécifier **Paiement à l’utilisation** pour l’**offre**, et à spécifier manuellement le pourcentage de remise (applicable à l’abonnement) dans la zone **Remise** des propriétés d’évaluation :

  ![Propriétés de l’évaluation](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Quelle est la différence entre le dimensionnement local et le dimensionnement basé sur les performances ?

Avec un dimensionnement local, Azure Migrate ne tient pas compte des données de performances des machines virtuelles. Il redimensionne les machines virtuelles en fonction de la configuration locale. Avec un dimensionnement basé sur les performances, le dimensionnement est basé sur les données d’utilisation.

Prenons par exemple une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, présentant 50 % d’utilisation du processeur et 50 % d’utilisation de la mémoire. Pour cette machine virtuelle, le dimensionnement local recommande une référence SKU de machine virtuelle Azure dotée de quatre cœurs et 8 Go de mémoire. Le dimensionnement basé sur les performances recommande une référence SKU de machine virtuelle de deux cœurs et de 4 Go de mémoire, car le pourcentage d’utilisation est pris en compte.

De même, le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage. Si le critère de dimensionnement est basé sur les performances et que le type de stockage est automatique, Azure Migrate prend en compte les valeurs de débit et d’IOPS du disque pour identifier le type de disque cible (standard ou premium).

Si le critère de dimensionnement est basé sur les performances et que le type de stockage est premium, Azure Migrate recommande un disque Premium. La référence SKU de disque Premium est sélectionnée en fonction de la taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est le dimensionnement locale et que le type de stockage est standard ou premium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Quel est l’impact de l’utilisation de l’historique des performances et des centiles sur les recommandations de taille ?

Ces propriétés sont uniquement applicables pour le dimensionnement en fonction des performances.

Azure Migrate collecte l’historique des performances des machines locales et l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure.

L’appliance profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes. L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes. Pour créer le point de données, l’appliance sélectionne la valeur maximale dans les échantillons de 20 secondes. L’appliance envoie ce point de données à Azure.

Lorsque vous créez une évaluation dans Azure (en fonction de la durée des performances et de la valeur de centile de l’historique des performances), Azure Migrate calcule la valeur d’utilisation effective et l’utilise pour le dimensionnement.

Par exemple, supposons que vous définissez la durée des performances sur un jour et la valeur de centile sur 95 centiles. Azure Migrate trie les points d’échantillonnage de 15 minutes envoyés par le collecteur pour le jour précédent dans l’ordre croissant et sélectionne la valeur du 95e centile comme utilisation effective.

La valeur du 95e centile vous permet d'ignorer les valeurs hors norme. Les valeurs hors norme peuvent être incluses si vous utilisez le 99e centile. Si vous souhaitez choisir l’utilisation maximale de la période, sans manquer les valeurs hors norme, sélectionnez le 99e centile.

### <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

La visualisation des dépendances vous permet d’évaluer les groupes de machines virtuelles pour la migration avec une plus grande confiance. Elle vérifie les dépendances croisées des machines avant d’exécuter une évaluation. La visualisation des dépendances vous aide à vérifier que rien n’est oublié, et à éviter des interruptions inattendues lorsque vous migrez vers Azure. Azure Migrate utilise la solution Service Map dans les journaux d'activité Azure Monitor pour activer la visualisation des dépendances.

> [!NOTE]
> La visualisation des dépendances n’est pas disponible dans Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Dois-je payer pour utiliser la visualisation des dépendances ?

Non. Pour plus d’informations, consultez [Tarification Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Dois-je installer quelque chose pour la visualisation des dépendances ?

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez évaluer.

Vous devez installer les agents suivants sur chaque machine :
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent de dépendances](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin de ces agents, sauf si vous utilisez la visualisation des dépendances.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Puis-je utiliser un espace de travail existant pour la visualisation des dépendances ?

Oui, vous pouvez attacher un espace de travail existant au projet de migration et l’utiliser pour la visualisation des dépendances. Pour plus d’informations, consultez « Fonctionnement » dans l’article [Visualisation des dépendances](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Puis-je exporter le rapport de visualisation des dépendances ?

Non, la visualisation des dépendances ne peut pas être exportée. Étant donné qu’Azure Migrate utilise Service Map pour la visualisation des dépendances, vous pouvez utiliser les [API REST de Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) pour obtenir les dépendances au format JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Comment puis-je automatiser l’installation de Microsoft Monitoring Agent (MMA) et de l’agent de dépendances ?

Utilisez ce [script pour installer les agents](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples). Suivez ces [instructions pour installer MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) en utilisant la ligne de commande ou l’automation. Pour MMA, utilisez [ce script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

En plus des scripts, vous pouvez également utiliser des outils de déploiement tels que System Center Configuration Manager et [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) pour déployer les agents.

### <a name="what-operating-systems-are-supported-by-mma"></a>Quels systèmes d’exploitation sont pris en charge par MMA ?

- Affichez la liste des [systèmes d’exploitation Windows pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Affichez la liste des [systèmes d’exploitation Linux pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Quels sont les systèmes d’exploitation pris en charge par l'agent des dépendances ?

- Affichez la liste des [systèmes d’exploitation Windows pris en charge par l'agent des dépendances](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
- Affichez la liste des [systèmes d’exploitation Linux pris en charge par l'agent des dépendances](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Puis-je visualiser des dépendances dans Azure Migrate pour une durée supérieure à une heure ?
Non. Vous pouvez visualiser les dépendances jusqu’à une heure. Vous pouvez revenir à une date donnée de l’historique, jusqu’à un mois, mais la durée maximale de visualisation est d’une heure. Par exemple, vous pouvez utiliser la durée dans la carte des dépendances pour voir les dépendances d’hier, mais seulement pour une fenêtre d’une heure. Vous pouvez cependant utiliser les journaux Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une durée plus longue.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Puis-je utiliser la visualisation des dépendances pour les groupes contenant plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances de groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) contenant 10 machines virtuelles au maximum. En présence d'un groupe contenant plus de 10 machines virtuelles, nous vous recommandons de diviser le groupe en groupes plus petits, puis de visualiser les dépendances.

## <a name="azure-migrate-server-migration"></a>Migration de serveur Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Quelle est la différence entre Azure Migrate Server Migration et Azure Site Recovery ?

Azure Migrate Server Migration utilise le moteur de réplication de Site Recovery pour la migration basée sur les agents des machines virtuelles VMware, la migration des machines virtuelles Hyper-V et la migration des serveurs physiques vers Azure. L’option sans agent pour migrer des machines virtuelles VMware est intégrée en mode natif dans Server Migration.

## <a name="next-steps"></a>Étapes suivantes
Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
