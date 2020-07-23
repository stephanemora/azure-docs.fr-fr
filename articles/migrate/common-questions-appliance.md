---
title: Questions fréquentes (FAQ) sur l’appliance Azure Migrate
description: Retrouvez les réponses aux questions courantes sur l’appliance Azure Migrate.
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 9c3547667ed91331d3cb4d319279c9494eb7a3d2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530115"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur l’appliance Azure Migrate. Si vous avez d’autres questions, consultez les ressources suivantes :

- [Questions générales](resources-faq.md) sur Azure Migrate
- Questions sur la [découverte, l’évaluation et la visualisation des dépendances](common-questions-discovery-assessment.md)
- Questions sur la [migration de serveur](common-questions-server-migration.md)
- Obtenez des réponses à vos questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Qu’est-ce que l’appliance Azure Migrate ?

L’appliance Azure Migrate est une appliance légère utilisée par l’outil Azure Migrate: Server Assessment pour détecter et évaluer les serveurs locaux. Azure Migrate : Server Migration utilise aussi l’appliance pour la migration sans agent de machines virtuelles VMware locales.

Voici plus d’informations sur l’appliance Azure Migrate :

- L’appliance est déployée localement en tant que machine virtuelle ou physique.
- Elle détecte les machines locales et envoie en continu des métadonnées et des données de performances sur les machines à Azure Migrate.
- La découverte de l’appliance se fait sans agent. Rien n’est installé sur les machines découvertes.

[En savoir plus](migrate-appliance.md) sur l’appliance.

## <a name="how-can-i-deploy-the-appliance"></a>Comment puis-je déployer l’appliance ?

L’appliance peut être déployée comme suit :

- À l’aide d’un modèle pour les machines virtuelles VMware et les machines virtuelles Hyper-V (modèle OVA pour VMware ou VHD pour Hyper-V).
- Si vous ne souhaitez pas utiliser de modèle, ou si vous êtes dans Azure Government, vous pouvez déployer l’appliance pour VMware ou Hyper-V à l’aide d’un script PowerShell.
- Pour les serveurs physiques, vous déployez toujours l’appliance à l’aide d’un script.


## <a name="how-does-the-appliance-connect-to-azure"></a>Comment l’appliance se connecte-t-elle à Azure ?

L’appliance peut se connecter via Internet ou en utilisant Azure ExpressRoute avec le peering public/Microsoft.

## <a name="does-appliance-analysis-affect-performance"></a>L’analyse de l’appliance affecte-t-elle les performances ?

L’appliance Azure Migrate profile les machines locales en continu pour mesurer les données de performances. Ce profilage n’a quasiment aucun impact sur les performances des machines profilées.

## <a name="can-i-harden-the-appliance-vm"></a>Peut-on renforcer la machine virtuelle de l’appliance ?

Quand vous utilisez le modèle téléchargé pour créer la machine virtuelle de l’appliance, vous pouvez ajouter des composants (par exemple un antivirus) au modèle si vous conservez les règles de communication et de pare-feu nécessaires à l’appliance Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Quelle est la connectivité réseau nécessaire ?


L’appliance doit avoir accès aux URL Azure. [Examinez](migrate-appliance.md#url-access) la liste des URL.

## <a name="what-data-does-the-appliance-collect"></a>Quelles données l’appliance collecte-t-elle ?

Pour plus d’informations sur les données recueillies par l’appliance Azure Migrate sur les machines virtuelles, consultez les articles suivants :

- **Machine virtuelle VMware** : [Examinez](migrate-appliance.md#collected-data---vmware) les données collectées.
- **Machine virtuelle Hyper-V** : [Examinez](migrate-appliance.md#collected-data---hyper-v) les données collectées.

## <a name="how-is-data-stored"></a>Comment les données sont stockées ?

Les données recueillies par l’appliance Azure Migrate sont stockées à l’emplacement Azure où vous avez créé le projet Azure Migrate.

Voici plus d’informations sur la façon dont les données sont stockées :

- Les données recueillies sont stockées de façon sécurisée dans CosmosDB dans un abonnement Microsoft. Les données sont supprimées quand vous supprimez le projet Azure Migrate. Le stockage est géré par Azure Migrate. Vous ne pouvez pas choisir un compte de stockage spécifique pour les données recueillies.
- Si vous utilisez la [visualisation des dépendances](concepts-dependency-visualization.md), les données recueillies sont stockées aux États-Unis, dans un espace de travail Azure Log Analytics créé dans l’abonnement Azure. Ces données sont supprimées quand vous supprimez l’espace de travail Log Analytics de votre abonnement.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quelle est la quantité de données chargée pendant le profilage continu ?

Le volume de données envoyées à Azure Migrate dépend de plusieurs paramètres. Par exemple, un projet Azure Migrate qui a 10 machines (chacune avec un disque et une carte réseau) envoie environ 50 Mo de données par jour. Cette valeur est approximative ; la valeur réelle varie en fonction du nombre de points de données pour les disques et les cartes réseau. Si le nombre de machines, de disques ou de cartes réseau augmente, l’augmentation des données envoyées est non linéaire.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, elles le sont dans les deux cas :

- Les métadonnées sont envoyées de façon sécurisée au service Azure Migrate sur Internet par le biais du protocole HTTPS.
- Les métadonnées sont stockées dans une base de données [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) et dans le [Stockage Blob Azure](../storage/common/storage-service-encryption.md) au sein d’un abonnement Microsoft. Les métadonnées sont chiffrées au repos pour le stockage.
- Les données pour l’analyse des dépendances sont également chiffrées en transit (par HTTPS sécurisé). Elles sont stockées dans un espace de travail Log Analytics dans votre abonnement. Les données sont chiffrées au repos pour l’analyse des dépendances.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Comment l’appliance se connecte-t-elle au serveur vCenter ?

Ces étapes décrivent comment l’appliance se connecte au serveur VMware vCenter :

1. L’appliance se connecte vCenter Server (port 443) en utilisant les informations d’identification fournies lors de sa configuration.
2. L’appliance utilise VMware PowerCLI pour interroger vCenter Server afin de recueillir des métadonnées sur les machines virtuelles managées par vCenter Server.
3. L’appliance collecte des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau) ainsi que l’historique des performances de chaque machine virtuelle pour le mois dernier.
4. Les métadonnées recueillies sont envoyées à l’outil Azure Migrate: Server Assessment (sur Internet par le biais du protocole HTTPS) pour l’évaluation.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>L’appliance Azure Migrate peut-elle se connecter à plusieurs serveurs vCenter ?

Non. Il existe un mappage un-à-un entre une [appliance Azure Migrate](migrate-appliance.md) et vCenter Server. Pour découvrir des machines virtuelles sur plusieurs instances de vCenter Server, vous devez déployer plusieurs appliances. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Un projet Azure Migrate peut-il avoir plusieurs appliances ?
Plusieurs appliances peuvent être attachées à un même projet. Toutefois, une appliance ne peut être associée qu’à un seul projet. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>L’appliance Azure Migrate/de réplication peut-elle se connecter au même vCenter ?
Oui. Vous pouvez ajouter à la fois l’appliance Azure Migrate (utilisée pour l’évaluation et la migration VMware sans agent) et l’appliance de réplication (utilisée pour la migration basée sur agent des machines virtuelles VMware) vers le même serveur vCenter.


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Combien de machines virtuelles ou de serveurs puis-je découvrir avec une appliance ?

Vous pouvez détecter jusqu’à 10 000 machines virtuelles VMware, jusqu’à 5 000 machines virtuelles Hyper-V et jusqu’à 1000 serveurs physiques avec une seule appliance. Si vous avez davantage de machines dans votre environnement local, découvrez comment [mettre à l’échelle une évaluation Hyper-V](scale-hyper-v-assessment.md), [mettre à l’échelle une évaluation VMware](scale-vmware-assessment.md) et [mettre à l’échelle une évaluation de serveur physique](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Peut-on supprimer une appliance ?

La suppression d’une appliance du projet n’est actuellement pas prise en charge.

La seule façon de supprimer l’appliance consiste à supprimer le groupe de ressources qui contient le projet Azure Migrate associé à l’appliance.

Toutefois, la suppression du groupe de ressources a pour effet de supprimer également les autres appliances inscrites, l’inventaire détecté, les évaluations et tous les autres composants Azure du groupe de ressources associés au projet.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Puis-je utiliser l’appliance avec un autre abonnement ou projet ?

Après avoir utilisé l’appliance pour lancer la détection, vous ne pouvez pas la reconfigurer de façon à utiliser un autre abonnement Azure, et vous ne pouvez pas l’utiliser dans un projet Azure Migrate différent. Vous ne pouvez pas non plus détecter des machines virtuelles sur une autre instance de vCenter Server. Configurez une nouvelle appliance pour ces tâches.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Puis-je configurer l’appliance sur une machine virtuelle Azure ?

Non. Actuellement, cette option n’est pas prise en charge. 

## <a name="can-i-discover-on-an-esxi-host"></a>Puis-je lancer une détection sur un hôte ESXi ?

Non. Pour découvrir des machines virtuelles VMware, vous devez disposer de vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Comment mettre à jour l’appliance ?

Par défaut, l’appliance et ses agents installés sont mis à jour automatiquement. L’appliance vérifie les mises à jour toutes les 24 heures. Les mises à jour qui échouent sont retentées. 

Seuls l’appliance et les agents de l’appliance sont mis à jour par ces mises à jour automatiques. Le système d’exploitation n’est pas mis à jour par les mises à jour automatiques Azure Migrate. Utilisez Windows Updates pour tenir le système d’exploitation à jour.

## <a name="can-i-check-agent-health"></a>Puis-je vérifier l’intégrité de l’agent ?

Oui. Dans le portail, accédez à la page **Intégrité de l’agent** pour l’outil Azure Migrate: Server Assessment ou Azure Migrate : d’Azure Migrate. Vous pouvez y vérifier l’état de la connexion entre Azure et les agents de détection et d’évaluation sur l’appliance.

## <a name="next-steps"></a>Étapes suivantes

Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
