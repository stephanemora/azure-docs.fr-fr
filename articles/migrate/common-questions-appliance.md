---
title: Questions courantes sur l’appliance Azure Migrate
description: Retrouvez les réponses à des questions courantes sur l’appliance Azure Migrate.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d82655b89d501bfa7ac4b5a7d008b26a2308f885
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563931"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur l’appliance Azure Migrate. Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Si vous avez d’autres questions, consultez ces articles :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- [Questions](common-questions-discovery-assessment.md) sur la découverte, l’évaluation et la visualisation des dépendances.


## <a name="what-is-the-azure-migrate-appliance"></a>Qu’est-ce que l’appliance Azure Migrate ?

L’appliance Azure Migrate est une appliance légère utilisée par l’outil Azure Migrate Server Assessment pour détecter et évaluer les serveurs locaux, et par l’outil Azure Migrate Server Migration pour la migration sans agent de machines virtuelles VMware locales. 

L’appliance est déployée localement en tant que machine virtuelle ou physique. Elle détecte les machines locales et envoie en continu des métadonnées et des données de performances sur les machines à Azure Migrate. La découverte de l’appliance se fait sans agent. Rien ne doit explicitement être installé sur les machines découvertes. [En savoir plus](migrate-appliance.md) sur l’appliance.

## <a name="how-does-the-appliance-connect-to-azure"></a>Comment l’appliance se connecte-t-elle à Azure ?

La connexion peut se faire via Internet ou en utilisant Azure ExpressRoute avec peering public/Microsoft.

## <a name="does-appliance-analysis-impact-performance"></a>L’analyse de l’appliance a-t-elle un impact sur les performances ?

L’appliance Azure Migrate profile les machines locales pour mesurer continuellement les données de performances des machines virtuelles. Ce profilage n’a pratiquement aucun impact sur les performances des hôtes Hyper-V/ESXi ou du serveur VMware vCenter.

### <a name="can-i-harden-the-appliance-vm"></a>Peut-on renforcer la machine virtuelle de l’appliance ?

Lorsque vous créez la machine virtuelle de l’appliance suivant le modèle téléchargé, vous pouvez ajouter des composants (par exemple, un antivirus) au modèle, tant que vous ne modifiez pas les règles de communication et de pare-feu nécessaires à l’appliance Azure Migrate.


## <a name="what-network-connectivity-is-needed"></a>Quelle est la connectivité réseau requise ?

Consultez les pages suivantes :
- Évaluation VMware de l’appliance : conditions d’accès [URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements) et [port](migrate-support-matrix-vmware.md#assessment-port-requirements).
- Migration VMware sans agent de l’appliance : conditions d’accès [URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) et [port](migrate-support-matrix-vmware.md#agentless-migration-port-requirements).
- Évaluation Hyper-V de l’appliance : conditions d’accès [URL](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) et [port](migrate-support-matrix-hyper-v.md#assessment-port-requirements).


## <a name="what-data-does-the-appliance-collect"></a>Quelles données l’appliance collecte-t-elle ?

Consultez les données collectées :

- [Données de performances](migrate-appliance.md#collected-performance-data-vmware) et [métadonnées](migrate-appliance.md#collected-metadata-vmware) de la machine virtuelle VMware.
- [Données de performances](migrate-appliance.md#collected-performance-data-hyper-v) et [métadonnées](migrate-appliance.md#collected-metadata-hyper-v) de la machine virtuelle Hyper-V.


## <a name="how-is-data-stored"></a>Comment les données sont stockées ?

Les données collectées par l’appliance Azure Migrate sont stockées à l’emplacement Azure choisi lors de la création du projet de migration. 

- Les données sont stockées de façon sécurisée dans un abonnement Microsoft et sont supprimées quand vous supprimez le projet Azure Migrate.
- Si la [visualisation des dépendances](concepts-dependency-visualization.md) est utilisée, les données collectées sont stockées aux États-Unis, dans un espace de travail Log Analytics créé dans l’abonnement Azure. Ces données sont supprimées lorsque vous supprimez l’espace de travail Log Analytics de votre abonnement.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Quelle est la quantité de données chargée dans le profilage continu ?

Le volume de données envoyées à Azure Migrate dépend de plusieurs paramètres. Pour vous donner une idée de ce volume, un projet Azure Migrate avec 10 machines (chacune disposant d’un disque et d’une carte réseau) envoie environ 50 Mo par jour. Cette valeur est approximative et change en fonction du nombre de points de données des cartes réseau et des disques. L’augmentation des données envoyées n’est pas linéaire si le nombre d’ordinateurs, de cartes réseau ou de disques augmente.

## <a name="is-data-encrypted-at-restin-transit"></a>Les données sont-elles chiffrées au repos/en transit ?

Oui, les deux.

- Les métadonnées sont envoyées de façon sécurisée au service Azure Migrate sur Internet via HTTPS.
- Les métadonnées sont stockées dans une base de données [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) et dans le [Stockage Blob Azure](../storage/common/storage-service-encryption.md) au sein d’un abonnement Microsoft. Les métadonnées sont chiffrées au repos.
- Les données pour l’analyse des dépendances sont également chiffrées en transit (HTTPS sécurisé). Elles sont stockées dans un espace de travail Log Analytics dans votre abonnement. Elles sont également chiffrées au repos.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Comment l’appliance se connecte-t-elle au serveur vCenter ?

1. L’appliance se connecte à vCenter Server (port 443) en utilisant les informations d’identification que vous avez fournies lors de sa configuration.
2. L’appliance utilise VMware PowerCLI pour interroger vCenter Server afin de collecter des métadonnées sur les machines virtuelles gérées par vCenter Server.
3. L’appliance collecte des données de configuration sur les machines virtuelles (cœurs, mémoire, disques, cartes réseau) ainsi que l’historique des performances de chaque machine virtuelle pour le mois dernier.
4. Les métadonnées collectées sont envoyées à Azure Migrate : Server Assessment (sur Internet via HTTPS) pour l’évaluation.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Peut-on connecter l’appliance à plusieurs serveurs vCenter ?

Non. Il existe un mappage un-à-un entre une appliance et le serveur vCenter Server. Pour découvrir des machines virtuelles sur plusieurs instances vCenter Server, vous devez déployer plusieurs appliances.

### <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Combien de machines virtuelles peut-on détecter avec une appliance ?

Il est possible de détecter jusqu’à 10 000 machines virtuelles VMware et 5 000 machines virtuelles Hyper-V avec une seule appliance. Si vous avez plus de machines dans votre environnement local, découvrez comment adapter l’évaluation des machines [Hyper-V](scale-hyper-v-assessment.md) et [VMware](scale-vmware-assessment.md).

### <a name="can-i-delete-an-appliance"></a>Peut-on supprimer une appliance ?

La suppression de l’appliance du projet n’est actuellement pas prise en charge.

- La seule façon de supprimer l’appliance consiste à supprimer le groupe de ressources qui contient le projet Azure Migrate associé à l’appliance.
- Toutefois, la suppression du groupe de ressources a pour effet de supprimer également les autres appliances inscrites, l’inventaire détecté, les évaluations et tous les autres composants Azure associés au projet dans le groupe de ressources.


## <a name="next-steps"></a>Étapes suivantes
Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
