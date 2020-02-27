---
title: Questions courantes sur la découverte, l’évaluation et l’analyse des dépendances dans Azure Migrate
description: Obtenez des réponses aux questions courantes sur la découverte, l’évaluation et l’analyse des dépendances dans Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 179be4f5b39806c717e94789c9fe52842d2a07d8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589230"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Questions courantes sur la découverte, l’évaluation et l’analyse des dépendances

Cet article donne des réponses aux questions courantes sur la découverte, l’évaluation et l’analyse des dépendances dans Azure Migrate. Si vous avez d’autres questions, consultez ces articles :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- [Questions](common-questions-appliance.md) sur l’appliance Azure Migrate.
- [Questions](common-questions-server-migration.md) sur la migration de serveur.
- Postez des questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Combien de machines virtuelles peut-on détecter avec une appliance ?

Vous pouvez découvrir jusqu’à 10 000 machines virtuelles VMware, jusqu’à 5 000 machines virtuelles Hyper-V et jusqu’à 250 serveurs avec une seule appliance. Si vous avez plus de machines dans votre environnement local, découvrez comment adapter l’évaluation des machines [Hyper-V](scale-hyper-v-assessment.md) et [VMware](scale-vmware-assessment.md) ainsi que des évaluations [physiques](scale-physical-assessment.md).



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>La taille d’une machine virtuelle a changé. Puis-je réexécuter une évaluation ?

L’appliance Azure Migrate collecte en permanence des informations sur l’environnement local. Cependant, une évaluation est une capture instantanée à une date et heure de machines virtuelles locales. Si vous changez les paramètres d’une machine virtuelle que vous voulez évaluer, utilisez l’option de recalcul pour mettre à jour l’évaluation avec les derniers changements.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Comment faire pour découvrir les machines virtuelles dans un environnement multilocataire ?

- Pour VMware, si votre environnement est partagé entre des locataires et que vous ne voulez pas découvrir les machines virtuelles d’un locataire se trouvant dans l’abonnement d’un autre locataire, créez des informations d’identification vCenter Server permettant d’accéder uniquement aux machines virtuelles que vous voulez découvrir. Utilisez ensuite ces informations d’identification quand vous démarrez la détection dans l’appliance Azure Migrate.
- Pour Hyper-V, la détection utilise les informations d’identification de l’hôte Hyper-V. Si les machines virtuelles partagent le même hôte Hyper-V, aucun moyen ne permet de séparer la détection.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>Ai-je besoin de vCenter Server pour la découverte de machines virtuelles VMware ?

Oui, Azure Migrate requiert vCenter Server pour découvrir un environnement VMware. Il ne prend pas en charge la découverte des hôtes ESXi qui ne sont pas gérés par vCenter Server.


## <a name="whats-the-difference-sizing-options"></a>En quoi diffèrent les options de dimensionnement ?

Avec un dimensionnement local, Azure Migrate ne tient pas compte des données de performances des machines virtuelles pour l’évaluation. Il évalue les tailles des machines virtuelles en fonction de la configuration locale. Avec un dimensionnement basé sur les performances, le dimensionnement est basé sur les données d’utilisation.

- Par exemple, si une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, présentant 50 % d’utilisation du processeur et 50 % d’utilisation de la mémoire, voici ce qui se produit :
    - Le dimensionnement local recommandera une référence SKU de machine virtuelle Azure dotée de 4 cœurs et de 8 Go de mémoire.
    - Le dimensionnement basé sur les performances recommandera une référence SKU de machine virtuelle avec 2 cœurs et 4 Go de mémoire, car le pourcentage d’utilisation est pris en compte.

- De même, le dimensionnement de disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
    - Si le critère de dimensionnement est basé sur les performances et que le type de stockage est automatique, Azure Migrate prend en compte les valeurs de débit et d’IOPS du disque quand il identifie le type de disque cible (standard ou premium).
    - Si le critère de dimensionnement est basé sur les performances et que le type de stockage est Premium, Azure Migrate recommande une référence (SKU) de disque Premium, en fonction de la taille du disque local. La même logique est appliquée au dimensionnement du disque quand le dimensionnement est local et que le type de stockage est standard ou premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>L’historique des performances et le niveau d’utilisation ont-ils un impact sur le dimensionnement ?

Ces propriétés sont uniquement applicables pour le dimensionnement en fonction des performances.

- Azure Migrate collecte l’historique des performances des machines locales et l’utilise pour recommander la taille de la machine virtuelle et le type de disque dans Azure.
- L’appliance profile en continu l’environnement local pour collecter les données d’utilisation en temps réel toutes les 20 secondes.
- L’appliance cumule les échantillons de 20 secondes et crée un point de données unique toutes les 15 minutes.
- Pour créer le point de données, l’appliance sélectionne la valeur maximale dans les échantillons de 20 secondes.
- L’appliance envoie ce point de données à Azure.

Quand vous créez une évaluation dans Azure, en fonction de la durée des performances et de la valeur de centile de l’historique des performances, Azure Migrate calcule la valeur d’utilisation effective et l’utilise pour le dimensionnement.

- Par exemple, si vous définissez la durée des performances à un jour et la valeur de centile à 95 centiles, Azure Migrate trie les points d’échantillonnage de 15 minutes envoyés par le collecteur pour le dernier jour dans l’ordre croissant et récupère la valeur du 95e centile comme utilisation effective.
- L’utilisation de la valeur du 95e centile garantit que les valeurs hors norme sont ignorées. Les valeurs hors norme peuvent être incluses si vous utilisez le 99e centile. Si vous souhaitez choisir l’utilisation maximale de la période, sans manquer les valeurs hors norme, sélectionnez le 99e centile.

## <a name="what-is-dependency-visualization"></a>Qu’est-ce que la visualisation des dépendances ?

Utilisez la visualisation des dépendances pour évaluer les groupes de machines virtuelles en vue d’une migration plus fiable. La visualisation des dépendances vérifie les dépendances croisées des machines avant que vous exécutiez une évaluation. Elle vous aide à vérifier que rien n’est oublié, permettant ainsi d’éviter des interruptions inattendues quand vous migrez vers Azure. Azure Migrate utilise la solution Service Map dans Azure Monitor pour activer la visualisation des dépendances. [En savoir plus](concepts-dependency-visualization.md).

> [!NOTE]
> La visualisation des dépendances n’est pas disponible dans Azure Government.

## <a name="do-i-pay-for-dependency-visualization"></a>Dois-je payer pour la visualisation des dépendances ?
Non. [En savoir plus](https://azure.microsoft.com/pricing/details/azure-migrate/) sur la tarification Azure Migrate.

## <a name="what-do-i-install-for-dependency-visualization"></a>Que dois-je installer pour la visualisation des dépendances ?

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez évaluer.

Vous devez installer les agents suivants sur chaque machine :
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent de dépendances](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin de ces agents, sauf si vous utilisez la visualisation des dépendances.

## <a name="can-i-use-an-existing-workspace"></a>Puis-je utiliser un espace de travail existant ?

Oui, vous pouvez attacher un espace de travail existant au projet de migration et l’utiliser pour la visualisation des dépendances. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Puis-je exporter le rapport de visualisation des dépendances ?

Non, la visualisation des dépendances ne peut pas être exportée. Toutefois, Azure Migrate utilise Service Map, et vous pouvez utiliser les [API REST Service Map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) pour récupérer les dépendances au format JSON.

## <a name="can-i-automate--mmadependency-agent-installation"></a>Puis-je automatiser l’installation de l’agent MMA/Dependency ?

Utilisez ce [script pour installer l’agent de dépendances](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Suivez ces [instructions pour installer MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) en utilisant la ligne de commande ou l’automation. Pour MMA, utilisez [ce script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Outre les scripts, vous pouvez également utiliser les outils de déploiement tels que Microsoft Endpoint Configuration Manager et [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) pour déployer les agents.


## <a name="what-operating-systems-does-mma-support"></a>Quels sont les systèmes d’exploitation pris en charge par MMA ?

- Affichez la liste des [systèmes d’exploitation Windows pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Affichez la liste des [systèmes d’exploitation Linux pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Puis-je visualiser des dépendances pendant plus d’une heure ?
Non. Vous pouvez visualiser les dépendances jusqu’à une heure. Vous pouvez revenir à une date donnée de l’historique, jusqu’à un mois, mais la durée maximale de visualisation est d’une heure. Par exemple, vous pouvez utiliser la durée mentionnée dans la carte des dépendances pour voir les dépendances d’hier, mais seulement pour une fenêtre d’une heure. Vous pouvez cependant utiliser les journaux Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une durée plus longue.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Puis-je visualiser les dépendances pour les groupes de plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) pour des groupes contenant au maximum 10 machines virtuelles. En présence d’un groupe de plus de 10 machines virtuelles, nous vous recommandons de diviser le groupe en groupes plus petits, puis de visualiser les dépendances.




## <a name="next-steps"></a>Étapes suivantes
Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
