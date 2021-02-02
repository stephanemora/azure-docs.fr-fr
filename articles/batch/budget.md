---
title: Analyse des coûts et budgets
description: Découvrez comment obtenir une analyse des coûts, définir un budget et réduire les coûts pour les ressources de calcul et licences logicielles sous-jacentes utilisées dans le cadre de l’exécution de vos charges de travail Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679309"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analyse des coûts et budgets pour Azure Batch

Si l’utilisation d’Azure Batch est gratuite, les ressources de calcul et licences logicielles sous-jacentes utilisées pour exécuter les charges de travail Batch peuvent occasionner des coûts. Ces coûts peuvent être liés aux machines virtuelles d’un pool, au transfert de données à partir des machines virtuelles, ou aux données d’entrée ou de sortie stockées dans le cloud. Cette rubrique vous aidera à comprendre l’origine des coûts, la manière d’établir un budget pour un pool ou un compte batch, et la façon de réduire les coûts des charges de travail Batch.

## <a name="batch-resources"></a>Ressources Batch

Les machines virtuelles représentent la ressource la plus importante utilisée pour le traitement Batch. Le coût d'utilisation des machines virtuelles pour Batch est calculé en fonction du type, de la quantité et de la durée d'utilisation. Les options de facturation des machines virtuelles incluent le [paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou la [réservation](../cost-management-billing/reservations/save-compute-costs-reservations.md) (payer à l'avance). Les deux options de paiement offrent différents avantages selon votre charge de travail de calcul, et affecteront différemment votre facture.

Lorsque des applications sont déployées sur des nœuds Batch (machines virtuelles) à l’aide de [packages d’application](batch-application-packages.md), vous êtes facturé pour les ressources Stockage Azure que vos packages d’application consomment. Vous êtes également facturé pour le stockage de tout fichier d'entrée ou de sortie, notamment les fichiers de ressources et autres données de journal. En général, le coût des données de stockage associées à Batch est beaucoup moins élevé que celui des ressources de calcul. Chaque machine virtuelle d’un pool créée avec une [Configuration de machine virtuelle](nodes-and-pools.md#virtual-machine-configuration) est associée à un disque de système d’exploitation managé par Azure. Les disques managés par Azure entraînent un coût supplémentaire, et d’autres niveaux de performance de disque ont également des coûts différents.

Les pools Batch utilisent des ressources de mise en réseau. En particulier, pour les pools **VirtualMachineConfiguration**, des équilibreurs de charge standard sont utilisés, qui nécessitent des adresses IP statiques. Les équilibreurs de charge utilisés par Batch sont visibles pour les comptes **Abonnement utilisateur**, mais pas pour les comptes **Service Batch**. Les équilibreurs de charge standard entraînent des frais pour toutes les données transférées vers et depuis les machines virtuelles d’un pool Batch ; sélectionnez les API Batch qui récupèrent les données des nœuds de pool (par exemple Get Task/Node File), les paquets d'application de tâches, les fichiers de ressources/sorties, et les images de conteneur.

### <a name="additional-services"></a>Services supplémentaires

Les services n'incluant pas les machines virtuelles et le stockage peuvent être intégrés au coût de votre compte Batch.

D'autres services couramment utilisés avec Batch peuvent inclure :

- Application Insights
- Data Factory
- Azure Monitor
- Réseau virtuel
- Machines virtuelles avec applications graphiques

Selon les services que vous utilisez avec votre solution Batch, des frais supplémentaires peuvent s’appliquer. Référez-vous à la [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour déterminer le coût de chaque service supplémentaire.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analyse des coûts et budgets d’un pool

Dans le portail Azure, vous pouvez créer des budgets et des alertes de dépenses pour vos pools ou comptes Batch. Les budgets et alertes sont utiles pour notifier les parties prenantes des risques de dépassement, même si un retard les alertes de dépenses et un léger dépassement de budget peuvent se produire.

Dans cet exemple, nous allons examiner une analyse des coûts d'un pool Batch individuel.

1. Dans le portail Azure, tapez ou sélectionnez **Cost Management + Billing**.
1. Sélectionnez votre abonnement dans la section **Étendues de facturation**.
1. Sous **Gestion des coûts**, sélectionnez **Analyse des coûts**.
1. Sélectionnez **Ajouter un filtre**. Dans la première liste déroulante, sélectionnez **Ressource**.
1. Dans le deuxième menu déroulant, sélectionnez le pool Batch. Lorsque le pool est sélectionné, l’analyse des coûts du pool s’affiche, comme dans l’exemple présenté ici.
    ![Capture d’écran montrant l’analyse des coûts d’un pool dans le portail Azure.](./media/batch-budget/pool-cost-analysis.png)

L'analyse du coût qui en résulte montre le coût du pool ainsi que les ressources qui génèrent ce coût. Dans cet exemple, les machines virtuelles utilisées dans le pool sont la ressource la plus coûteuse.

Pour créer un budget pour le pool, sélectionnez **Budget : aucun**, puis **Créer un nouveau budget >** . Utilisez maintenant la fenêtre afin de [configurer un budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md) spécifique pour votre pool.

> [!NOTE]
> Azure Batch est basé sur la technologie d’Azure Cloud Services et des machines virtuelles Azure. Si vous sélectionnez **Configuration des services cloud**, vous êtes facturé en fonction de la structure de tarification des services cloud. Si vous sélectionnez la **Configuration de la machine virtuelle**, vous êtes facturé en fonction de la structure de tarification des machines virtuelles. L’exemple sur cette page utilise la **Configuration de machine virtuelle** recommandée pour la plupart des pools Batch.

## <a name="minimize-cost"></a>Réduire le coût

L'utilisation de plusieurs machines virtuelles et services Azure pour des périodes prolongées peut se révéler coûteuse. Envisagez d’utiliser ces stratégies pour optimiser l’efficacité de vos charges de travail et réduire vos coûts.

### <a name="low-priority-virtual-machines"></a>Machines virtuelles de faible priorité

Les [machines virtuelles de faible priorité](batch-low-pri-vms.md) réduisent le coût des charges de travail Batch en tirant parti de la capacité de calcul excédentaire disponible dans Azure. Lorsque vous spécifiez des machines virtuelles de faible priorité dans vos pools, Batch utilise ce surplus pour exécuter votre charge de travail. Vous pouvez réaliser des économies substantielles en utilisant des machines virtuelles de faible priorité plutôt que des machines virtuelles dédiées.

### <a name="virtual-machine-os-disk-type"></a>Type de disque du système d'exploitation de la machine virtuelle

Azure offre plusieurs [types de disques de système d’exploitation de machine virtuelle](../virtual-machines/disks-types.md). La plupart des machines virtuelles ont des tailles qui prennent en charge le stockage Premium et Standard. Quand une machine virtuelle de taille « s » est sélectionnée pour un pool, Batch configure des disques de système d’exploitation SSD Premium. Quand la taille de machine virtuelle « non s » est sélectionnée, le type de disque dur standard moins cher est utilisé. Par exemple, les disques de système d’exploitation SSD Premium sont utilisés pour `Standard_D2s_v3`, et les disques de système d’exploitation de type standard sont utilisés pour `Standard_D2_v3`.

Les disques de système d’exploitation de type SSD Premium sont plus onéreux, mais offrent des performances supérieures. Des machines virtuelles dotées de disques Premium peuvent démarrer légèrement plus rapidement que des machines virtuelles équipées de disques de système d’exploitation de type HDD Standard. Avec Batch, le disque du système d’exploitation est couvent peu utilisé, car les applications et les fichiers de tâches sont situés sur le disque SSD temporaire de la machine virtuelle. De ce fait, vous pouvez souvent sélectionner la taille de machine virtuelle « non-s » pour éviter de payer le coût accru du disque SSD Premium approvisionné quand la taille de machine virtuelle spécifiée est « s ».

### <a name="reserved-virtual-machine-instances"></a>Instances de machines virtuelles réservées

Si vous avez l’intention d’utiliser Batch sur une longue période, vous pouvez réduire le coût des machines virtuelles en utilisant des [Réservations Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) pour vos charges de travail. Le coût d’une réservation est nettement inférieur à celui d’un paiement à l'utilisation. Les instances de machines virtuelles utilisées sans réservation sont facturées à l’utilisation. Lorsque vous achetez une réservation, la remise de réservation est appliquée.

### <a name="automatic-scaling"></a>Mise à l’échelle automatique

La [mise à l'échelle automatique](batch-automatic-scaling.md) ajuste dynamiquement le nombre de machines virtuelles dans votre pool Batch en fonction des exigences du travail en cours. En mettant à l’échelle le pool selon la durée de vie d’un travail, la mise à l’échelle automatique garantit que les machines virtuelles effectuent un scale-up et sont utilisées uniquement quand il y a un travail à exécuter. Une fois le travail terminé ou quand il n’y a pas de travail, les machines virtuelles effectuent un scale-down automatiquement pour économiser les ressources de calcul. La mise à l'échelle vous permet de réduire le coût global de votre solution Batch en n'utilisant que les ressources nécessaires.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [outils et API Batch](batch-apis-tools.md) disponibles pour créer et surveiller des solutions Batch.  
- Apprenez-en davantage sur l’[utilisation de machines virtuelles de faible priorité avec Batch](batch-low-pri-vms.md).
