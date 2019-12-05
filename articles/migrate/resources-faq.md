---
title: Questions courantes sur Azure Migrate
description: Obtenez des réponses aux questions courantes au sujet du service Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284466"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Généralités

### <a name="which-azure-geographies-are-supported"></a>Quelles sont les zones géographiques Azure prises en charge ?

Passez en revue les zones géographiques prises en charge par Azure Migrate pour les [machines virtuelles VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) et pour les [machines virtuelles Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Quelle est la différence entre Azure Migrate et Site Recovery ?

Azure Migrate offre un hub centralisé pour gérer et suivre la découverte, l’évaluation et la migration de machines et de charges de travail locales dans Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) est une solution de reprise d’activité. Azure Migrate : L’outil Migration de serveur utilise des fonctionnalités Site Recovery de back-end pour la migration lift-and-shift de certains ordinateurs locaux.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Comment supprimer un projet Azure Migrate ?

Suivez [ces instructions](how-to-delete-project.md) pour supprimer un projet. [Passez en revue les ressources](how-to-delete-project.md#created-resources) créées à mesure que vous découvrez, évaluez et migrez des ordinateurs et des charges de travail dans un projet Azure Migrate.


## <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

### <a name="how-does-the-appliance-connect-to-azure"></a>Comment l’appliance se connecte-t-elle à Azure ?

La connexion peut se faire via Internet ou en utilisant Azure ExpressRoute avec peering public/Microsoft.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Quelle connectivité réseau est nécessaire pour l’évaluation et la migration de serveurs Azure Migrate ?

Passez en revue les matrices de prise en charge [VMware](migrate-support-matrix-vmware.md) et [Hyper-V](migrate-support-matrix-hyper-v.md) afin d’obtenir des informations sur les URL et les ports nécessaires pour qu’Azure Migrate communique avec Azure.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Puis-je renforcer la machine virtuelle de l’appliance créée avec le modèle ?

Vous pouvez ajouter des composants (par exemple des antivirus) au modèle, tant que vous ne modifiez pas les règles de communication et de pare-feu nécessaires pour l’appliance Azure Migrate.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Quelles données sont collectées par l’appliance Azure Migrate ?

Passez en revue les données collectées par l’appliance comme suit :
- [Données de performances](migrate-appliance.md#collected-performance-data-vmware) et [métadonnées](migrate-appliance.md#collected-metadata-vmware) des machines virtuelles VMware.
- [Données de performances](migrate-appliance.md#collected-performance-data-hyper-v) et [métadonnées](migrate-appliance.md#collected-metadata-hyper-v) des machines virtuelles Hyper-V.


### <a name="does-appliance-analysis-impact-performance"></a>L’analyse de l’appliance a-t-elle un impact sur les performances ?

L’appliance Azure Migrate profile les machines locales pour mesurer continuellement les données de performances des machines virtuelles. Ce profilage n’a pratiquement aucun impact sur les performances des hôtes Hyper-V/ESXi ou vCenter Server.

### <a name="where-and-how-long-is-collected-data-stored"></a>Où et pendant combien de temps les données collectées sont-elles stockées ?

Les données collectées par l’appliance Azure Migrate sont stockées à l’emplacement Azure que vous choisissez lorsque vous créez le projet de migration. Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées quand vous supprimez le projet Azure Migrate.

Pour la visualisation des dépendances, les données collectées sont stockées aux États-Unis, dans un espace de travail Log Analytics créé dans l’abonnement Azure. Ces données sont supprimées lorsque vous supprimez l’espace de travail Log Analytics de votre abonnement. [En savoir plus](concepts-dependency-visualization.md) sur visualisation de dépendance.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Quel est le volume de données chargées lors du profilage continu ?

Le volume de données envoyé à Azure Migrate varie selon plusieurs paramètres. Pour vous donner une idée de ce volume, un projet Azure Migrate avec 10 machines (chacune disposant d’un disque et d’une carte réseau) envoie environ 50 Mo par jour. Cette valeur est approximative et change en fonction du nombre de points de données des cartes réseau et des disques. L’augmentation des données envoyées n’est pas linéaire si le nombre d’ordinateurs, de cartes réseau ou de disques augmente.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, dans ces deux cas.
- Les métadonnées sont envoyées de façon sécurisée au service Azure Migrate sur Internet via HTTPS.
- Les métadonnées sont stockées dans une [base de données Cosmos DB](../cosmos-db/database-encryption-at-rest.md) et dans le [Stockage Blob Azure](../storage/common/storage-service-encryption.md) dans un abonnement Microsoft. Les métadonnées sont chiffrées au repos.
- Les données pour l’analyse des dépendances sont également chiffrées en transit (HTTPS sécurisé). Elles sont stockées dans un espace de travail Log Analytics dans votre abonnement. Elles sont également chiffrées au repos.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Comment l’appliance communique-t-elle avec vCenter Server et Azure Migrate ?

1. L’appliance se connecte à vCenter Server (port 443) en utilisant les informations d’identification que vous avez fournies lors de sa configuration.
2. L’appliance utilise VMware PowerCLI pour interroger vCenter Server afin de collecter des métadonnées sur les machines virtuelles gérées par vCenter Server. Elle collecte des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau) ainsi que l’historique des performances de chaque machine virtuelle pour le mois dernier.
3. Les métadonnées collectées sont ensuite envoyées à Azure Migrate : Server Assessment (sur Internet via HTTPS) pour l’évaluation.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Puis-je connecter la même appliance à plusieurs instances vCenter Server ?

Non. Il existe un mappage un-à-un entre une appliance et le serveur vCenter Server. Pour découvrir des machines virtuelles sur plusieurs instances vCenter Server, vous devez déployer plusieurs appliances.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>La taille de la machine a changé. Puis-je réexécuter l’évaluation ?

L’appliance Azure Migrate collecte en permanence des informations sur l’environnement local. Cependant, une évaluation est une capture instantanée à une date et heure de machines virtuelles locales. Si vous changez les paramètres d’une machine virtuelle que vous voulez évaluer, utilisez l’option de recalcul pour mettre à jour l’évaluation avec les derniers changements.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Comment effectuer une découverte dans un environnement multilocataire ?

- Pour VMware, si votre environnement est partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire se trouvant dans l’abonnement d’un autre locataire, créez des informations d’identification vCenter Server permettant d’accéder uniquement aux machines virtuelles que vous voulez découvrir. Utilisez ensuite ces informations d’identification quand vous démarrez la détection dans l’appliance Azure Migrate.
- Pour Hyper-V, la détection utilise les informations d’identification de l’hôte Hyper-V. Si les machines virtuelles partagent le même hôte Hyper-V, aucun moyen ne permet de séparer la détection.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Combien de machines virtuelles puis-je découvrir avec une seule appliance ?

Vous pouvez découvrir jusqu’à 10 000 machines virtuelles VMware et jusqu’à 5 000 machines virtuelles Hyper-V avec une seule appliance de migration. Si vous avez plus de machines dans votre environnement local, découvrez comment mettre à l’échelle l’évaluation [Hyper-V](scale-hyper-v-assessment.md) et [VMware](scale-vmware-assessment.md).

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Puis-je supprimer l’appliance Azure Migrate du projet ?

La suppression de l’appliance du projet n’est actuellement pas prise en charge.

- La seule façon de supprimer l’appliance consiste à supprimer le groupe de ressources qui contient le projet Azure Migrate associé à l’appliance.
- Toutefois, la suppression du groupe de ressources a pour effet de supprimer également les autres appliances inscrites, l’inventaire détecté, les évaluations et tous les autres composants Azure associés au projet dans le groupe de ressources.

## <a name="azure-migrate-server-assessment"></a>Évaluation de serveur Azure Migrate


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Azure Migrate a-t-il besoin de vCenter Server pour la découverte de machines virtuelles VMware ?

Oui, Azure Migrate requiert vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Quelle est la différence entre Azure Migrate Server Assessment et Map Toolkit ?

Server Assessment fournit une évaluation pour faciliter la préparation à la migration et l’évaluation des charges de travail pour la migration vers Azure. La boîte à outils [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) facilite d’autres tâches, notamment la planification de la migration pour les versions plus récentes de systèmes d’exploitation Windows clients et serveurs, ainsi que le suivi de l’utilisation des logiciels. Pour ces scénarios, continuez à utiliser la boîte à outils MAP Toolkit.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Quelle est la différence entre Server Assessment et le Planificateur de déploiement Site Recovery ?

Server Assessment est un outil de planification de la migration. Le Planificateur de déploiement Site Recovery est un outil de planification de la reprise d’activité après sinistre.

- **Planifier la migration locale vers Azure** : si vous envisagez de migrer vos serveurs locaux vers Azure, utilisez Server Assessment pour la planification de la migration. Il évalue les charges de travail locales, et fournit des conseils et des outils pour vous aider à effectuer la migration. Une fois le plan de migration en place, vous pouvez utiliser des outils, notamment Azure Migrate Server Migration, pour migrer les machines vers Azure.
- **Planifier la reprise d’activité après sinistre dans Azure** : si vous envisagez de configurer la reprise d’activité après sinistre d’un emplacement local vers Azure avec Site Recovery, utilisez le Planificateur de déploiement Site Recovery. Le planificateur de déploiement fournit une évaluation approfondie spécifique à Site Recovery de votre environnement local pour les besoins de la reprise d’activité après sinistre. Il fournit des recommandations relatives à la reprise d’activité, comme la réplication et le basculement.

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Quelle est la différence entre le dimensionnement local et le dimensionnement basé sur les performances ?

Avec un dimensionnement local, Azure Migrate ne tient pas compte des données de performances des machines virtuelles pour l’évaluation. Il évalue les tailles des machines virtuelles en fonction de la configuration locale. Avec un dimensionnement basé sur les performances, le dimensionnement est basé sur les données d’utilisation.

- Par exemple, si une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, présentant 50 % d’utilisation du processeur et 50 % d’utilisation de la mémoire, voici ce qui se produit :
    - Le dimensionnement local recommandera une référence (SKU) de machine virtuelle Azure dotée de quatre cœurs et de 8 Go de mémoire.
    - Le dimensionnement basé sur les performances recommandera une référence (SKU) de machine virtuelle de deux cœurs et de 4 Go de mémoire, car le pourcentage d’utilisation est pris en compte.

- De même, le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
    - Si le critère de dimensionnement est basé sur les performances et que le type de stockage est automatique, Azure Migrate prend en compte les valeurs de débit et d’IOPS du disque quand il identifie le type de disque cible (standard ou premium).
    - Si le critère de dimensionnement est basé sur les performances et que le type de stockage est Premium, Azure Migrate recommande une référence (SKU) de disque Premium, en fonction de la taille du disque local. La même logique est appliquée au dimensionnement du disque quand le dimensionnement est local et que le type de stockage est standard ou premium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>L’historique des performances et le centile d’utilisation ont-ils un impact sur les recommandations de taille ?

Ces propriétés sont uniquement applicables pour le dimensionnement en fonction des performances.

- Azure Migrate collecte l’historique des performances des machines locales et l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure.
- L’appliance profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes.
- L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes.
- Pour créer le point de données, l’appliance sélectionne la valeur maximale dans les échantillons de 20 secondes.
- L’appliance envoie ce point de données à Azure.

Quand vous créez une évaluation dans Azure, en fonction de la durée des performances et de la valeur de centile de l’historique des performances, Azure Migrate calcule la valeur d’utilisation effective et l’utilise pour le dimensionnement.

- Par exemple, si vous définissez la durée des performances à un jour et la valeur de centile à 95 centiles, Azure Migrate trie les points d’échantillonnage de 15 minutes envoyés par le collecteur pour le dernier jour dans l’ordre croissant et récupère la valeur du 95e centile comme utilisation effective.
- L’utilisation de la valeur du 95e centile garantit que les valeurs hors norme sont ignorées. Les valeurs hors norme peuvent être incluses si vous utilisez le 99e centile. Si vous souhaitez choisir l’utilisation maximale de la période, sans manquer les valeurs hors norme, sélectionnez le 99e centile.

## <a name="server-assessment---dependency-visualization"></a>Évaluation de serveurs - Visualisation des dépendances


### <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

Utilisez la visualisation des dépendances pour évaluer les groupes de machines virtuelles en vue d’une migration plus fiable. La visualisation des dépendances vérifie les dépendances croisées des machines avant que vous exécutiez une évaluation. Elle vous aide à vérifier que rien n’est oublié, permettant ainsi d’éviter des interruptions inattendues quand vous migrez vers Azure. Azure Migrate utilise la solution Service Map dans Azure Monitor pour activer la visualisation des dépendances. [En savoir plus](concepts-dependency-visualization.md).

> [!NOTE]
> La visualisation des dépendances n’est pas disponible dans Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Dois-je payer pour utiliser la visualisation des dépendances ?
Non. [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Dois-je installer quelque chose pour la visualisation des dépendances ?

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez évaluer.

Vous devez installer les agents suivants sur chaque machine :
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent de dépendances](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin de ces agents, sauf si vous utilisez la visualisation des dépendances.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Puis-je utiliser un espace de travail existant pour la visualisation des dépendances ?

Oui, vous pouvez attacher un espace de travail existant au projet de migration et l’utiliser pour la visualisation des dépendances. [Plus d’informations](concepts-dependency-visualization.md#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>Puis-je exporter le rapport de visualisation des dépendances ?

Non, la visualisation des dépendances ne peut pas être exportée. Toutefois, Azure Migrate utilise Service Map, et vous pouvez utiliser les [API REST Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) pour récupérer les dépendances au format JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Comment puis-je automatiser l’installation de Microsoft Monitoring Agent (MMA) et de l’agent de dépendances ?

Utilisez ce [script pour installer l’agent de dépendances](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Suivez ces [instructions pour installer MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) en utilisant la ligne de commande ou l’automation. Pour MMA, utilisez [ce script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

En plus des scripts, vous pouvez également utiliser des outils de déploiement tels que System Center Configuration Manager et [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) pour déployer les agents.


### <a name="what-operating-systems-are-supported-by-mma"></a>Quels systèmes d’exploitation sont pris en charge par MMA ?

- Affichez la liste des [systèmes d’exploitation Windows pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Affichez la liste des [systèmes d’exploitation Linux pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Puis-je visualiser des dépendances pendant plus d’une heure ?
Non. Vous pouvez visualiser les dépendances jusqu’à une heure. Vous pouvez revenir à une date donnée de l’historique, jusqu’à un mois, mais la durée maximale de visualisation est d’une heure. Par exemple, vous pouvez utiliser la durée mentionnée dans la carte des dépendances pour voir les dépendances d’hier, mais seulement pour une fenêtre d’une heure. Vous pouvez cependant utiliser les journaux Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une durée plus longue.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Puis-je utiliser la visualisation des dépendances pour des groupes de plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) pour des groupes contenant au maximum 10 machines virtuelles. En présence d’un groupe de plus de 10 machines virtuelles, nous vous recommandons de diviser le groupe en groupes plus petits, puis de visualiser les dépendances.

## <a name="azure-migrate-server-migration"></a>Migration de serveur Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Quelle est la différence entre Azure Migrate Server Migration et Site Recovery ?

- La migration sans agent de machines virtuelles VMware locales est native dans Azure Migrate Server Migration.
- Pour la migration de machines virtuelles Hyper-V, de serveurs physiques et de machines virtuelles VMware basées sur un agent, Azure Migrate Server Migration utilise le moteur de réplication Azure Site Recovery.


## <a name="next-steps"></a>Étapes suivantes
Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
