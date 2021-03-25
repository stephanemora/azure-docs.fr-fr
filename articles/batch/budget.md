---
title: Analyse des coûts et définition de budgets pour Azure Batch
description: Découvrez comment obtenir une analyse des coûts, définir un budget et réduire les coûts pour les ressources de calcul et licences logicielles sous-jacentes utilisées dans le cadre de l’exécution de vos charges de travail Batch.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99091325"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Analyse des coûts et définition de budgets pour Azure Batch

Cette rubrique permet de comprendre les coûts qui peuvent être associés à Azure Batch. Elle explique également comment définir un budget pour un pool ou un compte Batch et réduire les coûts des charges de travail Batch.

## <a name="understand-costs-associated-with-batch-resources"></a>Coûts associés aux ressources Batch

Si l’utilisation d’Azure Batch est gratuite, les ressources de calcul et licences logicielles sous-jacentes utilisées pour exécuter les charges de travail Batch peuvent occasionner des coûts. Ces coûts peuvent être liés aux machines virtuelles d’un pool, au transfert de données à partir des machines virtuelles, ou aux données d’entrée ou de sortie stockées dans le cloud.

### <a name="virtual-machines"></a>Machines virtuelles

Les machines virtuelles représentent la ressource la plus importante utilisée pour le traitement Batch. Le coût d'utilisation des machines virtuelles pour Batch est calculé en fonction du type, de la quantité et de la durée d'utilisation. Les options de facturation des machines virtuelles incluent le [paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) ou la [réservation](../cost-management-billing/reservations/save-compute-costs-reservations.md) (payer à l'avance). Les deux options de paiement offrent différents avantages selon votre charge de travail de calcul, et affecteront différemment votre facture.

Chaque machine virtuelle d’un pool créée avec une [Configuration de machine virtuelle](nodes-and-pools.md#virtual-machine-configuration) est associée à un disque de système d’exploitation managé par Azure. Les disques managés par Azure entraînent un coût supplémentaire, et d’autres niveaux de performance de disque ont également des coûts différents.

### <a name="storage"></a>Stockage

Lorsque des applications sont déployées sur des nœuds Batch (machines virtuelles) à l’aide de [packages d’application](batch-application-packages.md), vous êtes facturé pour les ressources Stockage Azure que vos packages d’application consomment. Le stockage des fichiers d’entrée et de sortie, notamment les fichiers de ressources et autres données de journal, vous est également facturé.

En général, le coût des données de stockage associées à Batch est beaucoup moins élevé que celui des ressources de calcul.

### <a name="networking-resources"></a>Ressources réseau

Les pools Batch utilisent des ressources réseau, dont certaines présentent des coûts associés. Pour les pools de configuration de machine virtuelle en particulier, des équilibreurs de charge standard sont employés, pour lesquels des adresses IP statiques sont nécessaires. Les équilibreurs de charge utilisés par Batch sont visibles pour les [comptes](accounts.md#batch-accounts) configurés en mode abonnement utilisateur, mais non en mode service Batch.

Les équilibreurs de charge standard entraînent des frais pour toutes les données transmises aux et par les machines virtuelles de pools Batch. Certaines API Batch qui récupèrent les données des nœuds de pool (par exemple Get Task/Node File), les packages d’application de tâches, les fichiers de ressources/sortie et les images conteneur engendrent également des frais.

### <a name="additional-services"></a>Services supplémentaires

Selon les services que vous utilisez avec votre solution Batch, des frais supplémentaires peuvent s’appliquer. Référez-vous à la [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour déterminer le coût de chaque service supplémentaire. Parmi les services souvent utilisés avec Batch, les suivants peuvent présenter des coûts associés :

- Application Insights
- Data Factory
- Azure Monitor
- Réseau virtuel
- Machines virtuelles avec applications graphiques

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Affichage de l’analyse des coûts et création d’un budget pour un pool

Dans le portail Azure, vous pouvez créer des budgets et des alertes de dépenses pour vos pools ou comptes Batch. Les budgets et alertes sont utiles pour notifier les parties prenantes des risques de dépassement, même si un retard les alertes de dépenses et un léger dépassement de budget peuvent se produire.

> [!NOTE]
> Dans cet exemple, le pool utilise la **configuration de machine virtuelle**, qui est recommandée pour la plupart des pools et facturée selon la structure tarifaire Machines virtuelles. Les pools qui appliquent la **configuration des services cloud** sont facturés suivant la structure tarifaire Services cloud.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Affichage de l’analyse des coûts pour un pool Batch

1. Dans le portail Azure, tapez ou sélectionnez **Cost Management + Billing**.
1. Sélectionnez votre abonnement dans la section **Étendues de facturation**.
1. Sous **Gestion des coûts**, sélectionnez **Analyse des coûts**.
1. Sélectionnez **Ajouter un filtre**. Dans la première liste déroulante, sélectionnez **Ressource**.
1. Dans le deuxième menu déroulant, sélectionnez le pool Batch. Lorsque le pool est sélectionné, l’analyse des coûts du pool s’affiche, comme dans l’exemple présenté ici.
    ![Capture d’écran montrant l’analyse des coûts d’un pool dans le portail Azure.](./media/batch-budget/pool-cost-analysis.png)

L'analyse du coût qui en résulte montre le coût du pool ainsi que les ressources qui génèrent ce coût. Dans cet exemple, les machines virtuelles utilisées dans le pool sont la ressource la plus coûteuse.

### <a name="create-a-budget-for-a-batch-pool"></a>Création d’un budget pour un pool Batch

1. Sur la page **Analyse des coûts**, sélectionnez **Budget : aucun**.
1. Sélectionnez **Créer un budget >** .
1. Utilisez la fenêtre qui apparaît pour configurer un budget propre à votre pool. Pour plus d’informations, consultez [Didacticiel : Créer et gérer des budgets Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Réduction des coûts associés à Azure Batch

Selon les scénarios, il peut être nécessaire de réduire autant que possible les coûts. Envisagez d’appliquer certaines de ces stratégies pour optimiser l’efficacité de vos charges de travail et réduire les coûts potentiels.

### <a name="use-low-priority-virtual-machines"></a>Machines virtuelles basse priorité

Les [machines virtuelles de faible priorité](batch-low-pri-vms.md) réduisent le coût des charges de travail Batch en tirant parti de la capacité de calcul excédentaire disponible dans Azure. Lorsque vous spécifiez des machines virtuelles de faible priorité dans vos pools, Batch utilise ce surplus pour exécuter votre charge de travail. Vous pouvez réaliser des économies substantielles en utilisant des machines virtuelles de faible priorité plutôt que des machines virtuelles dédiées.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Sélection d’un type de disque de système d’exploitation de machine virtuelle standard

Azure offre plusieurs [types de disques de système d’exploitation de machine virtuelle](../virtual-machines/disks-types.md). La plupart des machines virtuelles ont des tailles qui prennent en charge le stockage Premium et Standard. Quand une machine virtuelle de taille « s » est sélectionnée pour un pool, Batch configure des disques de système d’exploitation SSD Premium. Quand la taille de machine virtuelle « non s » est sélectionnée, le type de disque dur standard moins cher est utilisé. Par exemple, les disques de système d’exploitation SSD Premium sont utilisés pour `Standard_D2s_v3`, et les disques de système d’exploitation de type standard sont utilisés pour `Standard_D2_v3`.

Les disques de système d’exploitation de type SSD Premium sont plus onéreux, mais offrent des performances supérieures. Des machines virtuelles dotées de disques Premium peuvent démarrer légèrement plus rapidement que des machines virtuelles équipées de disques de système d’exploitation de type HDD Standard. Avec Batch, le disque du système d’exploitation est couvent peu utilisé, car les applications et les fichiers de tâches sont situés sur le disque SSD temporaire de la machine virtuelle. De ce fait, vous pouvez souvent sélectionner la taille de machine virtuelle « non-s » pour éviter de payer le coût accru du disque SSD Premium approvisionné quand la taille de machine virtuelle spécifiée est « s ».

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Achat de réservations pour les instances de machine virtuelle

Si vous avez l’intention d’utiliser Batch sur une longue période, vous pouvez réduire le coût des machines virtuelles en utilisant des [Réservations Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) pour vos charges de travail. Le coût d’une réservation est nettement inférieur à celui d’un paiement à l'utilisation. Les instances de machines virtuelles utilisées sans réservation sont facturées à l’utilisation. Lorsque vous achetez une réservation, la remise de réservation est appliquée.

### <a name="use-automatic-scaling"></a>Mise à l’échelle automatique

La [mise à l'échelle automatique](batch-automatic-scaling.md) ajuste dynamiquement le nombre de machines virtuelles dans votre pool Batch en fonction des exigences du travail en cours. En mettant à l’échelle le pool selon la durée de vie d’un travail, la mise à l’échelle automatique garantit que les machines virtuelles effectuent un scale-up et sont utilisées uniquement quand il y a un travail à exécuter. Une fois le travail terminé ou quand il n’y a pas de travail, les machines virtuelles effectuent un scale-down automatiquement pour économiser les ressources de calcul. La mise à l'échelle vous permet de réduire le coût global de votre solution Batch en n'utilisant que les ressources nécessaires.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md)
- Apprenez-en davantage sur l’[utilisation de machines virtuelles de faible priorité avec Batch](batch-low-pri-vms.md).
