---
title: Analyse des coûts et définition de budgets pour Azure Batch
description: Découvrez comment obtenir une analyse des coûts, définir un budget et réduire les coûts pour les ressources de calcul et licences logicielles sous-jacentes utilisées dans le cadre de l’exécution de vos charges de travail Batch.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 0e4c0b6235537d1e5c36e7adf997516d0f966d76
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532544"
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

## <a name="view-cost-analysis-and-create-budgets"></a>Afficher l’analyse des coûts et créer des budgets

[Azure Cost Management](/cost-management-billing/cost-management-billing-overview.md) vous permet de planifier, d’analyser et de réduire vos dépenses en vue d’optimiser votre investissement dans le cloud. Les coûts d’utilisation de tous les services Azure sont disponibles, notamment Azure Batch. Vous pouvez afficher et filtrer les coûts de Batch à afficher et filtrer, prévoir les coûts futurs et définir des limites de dépense avec des alertes lorsque ces limites sont atteintes.

Dans le portail Azure, vous pouvez créer des budgets et des alertes de dépenses pour vos pools ou comptes Batch. Les budgets et alertes sont utiles pour notifier les parties prenantes des risques de dépassement, même si un retard les alertes de dépenses et un léger dépassement de budget peuvent se produire.

La capture d’écran suivante montre un exemple de vue d'**analyse des coûts** pour un abonnement, filtrée de manière à n’afficher que les coûts cumulés associés à tous les comptes Batch. Les graphiques inférieurs montrent comment le coût total de la période sélectionnée peut être catégorisé par service consommé, emplacement et compteur. Bien qu’il s’agisse d’un exemple et qu’il ne soit pas destiné à refléter les coûts que vous pouvez voir pour vos abonnements, il est normal que le coût le plus élevé s’applique aux machines virtuelles allouées aux nœuds de pool Batch.

:::image type="content" source="media/batch-budget/subscription-cost-analysis.png" alt-text="Capture d’écran montrant l’analyse des coûts dans le portail Azure pour tous les comptes Batch d’un abonnement.":::

Un niveau supplémentaire de détail d’analyse des coûts peut être obtenu avec un filtre **Ressource**. Pour les comptes Batch, ces valeurs sont le nom du compte Batch et le nom du pool. Cela vous permet d’afficher les coûts d’un pool spécifique, de plusieurs pools, ou d’un ou plusieurs comptes.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Affichage de l’analyse des coûts pour un pool Batch

#### <a name="batch-service-pool-allocation-mode"></a>Mode d’allocation de pool du service Batch

Pour les comptes Batch créés avec le mode d’allocation de pool du service Batch :

1. Dans le portail Azure, tapez ou sélectionnez **Cost Management + Billing**.
1. Sélectionnez votre abonnement dans la section **Étendues de facturation**.
1. Sous **Gestion des coûts**, sélectionnez **Analyse des coûts**.
1. Sélectionnez **Ajouter un filtre**. Dans la première liste déroulante, sélectionnez **Ressource**.
1. Dans le deuxième menu déroulant, sélectionnez le pool Batch. Une fois le pool sélectionné, vous voyez l’analyse des coûts pour votre pool. La capture d'écran ci-dessous montre un exemple de données.
   :::image type="content" source="media/batch-budget/pool-cost-analysis.png" alt-text="Capture d’écran montrant l’analyse des coûts d’un pool Batch dans le portail Azure.":::

L'analyse du coût qui en résulte montre le coût du pool ainsi que les ressources qui génèrent ce coût. Dans cet exemple, les machines virtuelles utilisées dans le pool sont la ressource la plus coûteuse.

> [!NOTE]
> Dans cet exemple, le pool utilise la **configuration de la machine virtuelle**, qui est [recommandée pour la plupart des pools](batch-pool-cloud-service-to-virtual-machine-configuration.md) et facturée selon la structure tarifaire des machines virtuelles. Les pools qui appliquent la **configuration des services cloud** sont facturés suivant la structure tarifaire Services cloud.

Des [balises](../azure-resource-manager/management/tag-resources.md) peuvent être associées à des comptes Batch, ce qui permet d’utiliser des balises pour un filtrage plus avancé des coûts. Par exemple, les balises peuvent être utilisées pour associer des informations de projet, d’utilisateur ou de groupe à un compte Batch. Les balises ne peuvent pas être associées actuellement à des pools Batch.

#### <a name="user-subscription-pool-allocation-mode"></a>Mode d’allocation de pool de l'abonnement utilisateur

Pour les comptes Batch créés avec le mode d’allocation de pool de l’abonnement utilisateur :

1. Dans le portail Azure, tapez ou sélectionnez **Cost Management + Billing**.
1. Sélectionnez votre abonnement dans la section **Étendues de facturation**.
1. Sous **Gestion des coûts**, sélectionnez **Analyse des coûts**.
1. Sélectionnez **Ajouter un filtre**. Dans la première liste déroulante, sélectionnez **Balise**.
1. Dans la deuxième liste déroulante, sélectionnez **poolname**.
1. Dans la troisième liste déroulante, sélectionnez le pool Batch. Une fois le pool sélectionné, vous voyez l’analyse des coûts pour votre pool. La capture d'écran ci-dessous montre un exemple de données.
   :::image type="content" source="media/batch-budget/user-subscription-pool.png" alt-text="Capture d’écran montrant l’analyse des coûts d’un pool Batch d’abonnement utilisateur dans le portail Azure.":::

Notez que si vous souhaitez afficher les données de coût de tous les pools dans un compte Batch d’abonnement utilisateur, vous pouvez sélectionner **batchaccountname** dans la deuxième liste déroulante et le nom de votre compte Batch dans la troisième liste déroulante. 

> [!NOTE]
> Les pools créés par les comptes Batch de l’abonnement utilisateur ne s’affichent pas sous le filtre **Ressource**, bien que leur utilisation s’affiche toujours lors du filtrage des « machines virtuelles » sous le nom du service.

### <a name="create-a-budget-for-a-batch-pool"></a>Création d’un budget pour un pool Batch

Des budgets peuvent être créés et des alertes de coût sont émises lorsque différents pourcentages d’un budget sont atteints, par exemple 60 %, 80 % et 100 %. Les budgets peuvent spécifier un ou plusieurs filtres, ce qui vous permet de surveiller et d’alerter les coûts des comptes Batch à différentes granularités.

1. Sur la page **Analyse des coûts**, sélectionnez **Budget : aucun**.
1. Sélectionnez **Créer un budget >** .
1. Utilisez la fenêtre qui apparaît pour configurer un budget propre à votre pool. Pour plus d’informations, consultez [Didacticiel : Créer et gérer des budgets Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Réduction des coûts associés à Azure Batch

Selon les scénarios, il peut être nécessaire de réduire autant que possible les coûts. Envisagez d’appliquer certaines de ces stratégies pour optimiser l’efficacité de vos charges de travail et réduire les coûts potentiels.

### <a name="reduce-pool-node-use"></a>Réduire l’utilisation des nœuds de pool

Les coûts les plus importants associés à l’utilisation de Batch proviennent généralement des machines virtuelles allouées aux nœuds de pool. Pour les pools de configuration de machine virtuelle, les disques managés associés utilisés pour les disques de système d’exploitation de machine virtuelle peuvent également contribuer aux coûts de manière significative.

Évaluez votre application Batch pour déterminer si les nœuds de pool sont bien utilisés par des tâches de travail ou si les nœuds de pool sont inactifs pendant une durée supérieure à celle prévue. Il peut être possible de réduire le nombre de nœuds de pool qui sont alloués, de réduire le taux de scale-up des nœuds de pool ou d’augmenter le taux de scale-down pour augmenter l’utilisation.

En plus de la surveillance personnalisée, les [métriques Batch](batch-diagnostics.md#view-batch-metrics) peuvent aider à identifier les nœuds qui sont alloués, mais dans un état inactif. Vous pouvez sélectionner une métrique pour la plupart des états de nœud de pool à afficher en utilisant les métriques de surveillance de Batch dans le portail Azure. Le nombre de nœuds inactifs et le nombre de nœuds en cours d’exécution peuvent être consultés pour indiquer, par exemple si les nœuds de pool sont bien utilisés.

### <a name="ensure-pool-nodes-are-able-to-run-tasks"></a>Vérifier que les nœuds de pool sont en mesure d’exécuter des tâches

Les nœuds alloués répertoriés pour un pool entraînent normalement des coûts, mais il est possible que les nœuds de pool soient dans un état où il est impossible d’exécuter des tâches, par exemple « inutilisable » ou « starttaskfailed ». Les API ou les métriques Batch peuvent être utilisées pour surveiller et détecter cette catégorie de machine virtuelle. La raison de ces états peut ensuite être déterminée et des mesures correctives peuvent être prises pour réduire ou éliminer ces nœuds non sains.

### <a name="use-the-right-pool-node-vm-size"></a>Utiliser la taille de machine virtuelle de nœud du pool appropriée

Assurez-vous que la taille de machine virtuelle appropriée est utilisée, afin que les machines virtuelles soient bien utilisées lors de l’exécution de tâches, tout en fournissant les performances nécessaires à l’exécution de vos tâches de travail dans le délai imparti. Les machines virtuelles de nœuds de pool peuvent être sous-utilisées dans certaines situations, par exemple dans le cas d’une utilisation faible du processeur. Il est possible de réaliser des économies en choisissant une taille de machine virtuelle à un prix inférieur.

Pour déterminer l’utilisation des machines virtuelles, vous pouvez vous connecter à un nœud lors de l’exécution de tâches pour afficher les données de performances ou utiliser des [fonctionnalités de surveillance](monitoring-overview.md), telles qu’Application Insights, pour obtenir des données de performances à partir des nœuds de pool.

### <a name="use-pool-slots-to-reduce-node-requirements"></a>Utiliser des emplacements de pool pour réduire les conditions de nœud

Plusieurs emplacements de tâches peuvent être spécifiés pour un pool afin que le nombre correspondant de tâches puisse être exécuté en parallèle sur chaque nœud. Les emplacements de tâches de pool peuvent être utilisés pour réduire le nombre de nœuds utilisés dans un pool en choisissant des tailles de machines virtuelles plus grandes et en exécutant plusieurs tâches en parallèle sur le nœud pour vérifier que le nœud est bien utilisé. Si les nœuds sont sous-utilisés, les emplacements peuvent être utilisés pour augmenter l’utilisation. Par exemple, pour une application de tâche à thread unique, un emplacement par cœur peut être configuré. Il est également possible d’avoir plus d’emplacements que de cœurs. Cela s’applique si l’application bloque de manière significative l’attente des appels à des services externes à retourner, par exemple.

Définir [`taskSchedulingPolicy`](/rest/api/batchservice/pool/add#taskschedulingpolicy) sur `pack` permet de s’assurer que les machines virtuelles sont utilisées autant que possible, avec une mise à l’échelle plus facile pour supprimer des nœuds n’exécutant aucune tâche.

### <a name="use-low-priority-virtual-machines"></a>Machines virtuelles basse priorité

Les [machines virtuelles de faible priorité](batch-low-pri-vms.md) réduisent le coût des charges de travail Batch en tirant parti de la capacité de calcul excédentaire disponible dans Azure. Lorsque vous spécifiez des machines virtuelles de faible priorité dans vos pools, Batch utilise ce surplus pour exécuter votre charge de travail. Vous pouvez réaliser des économies substantielles en utilisant des machines virtuelles de faible priorité plutôt que des machines virtuelles dédiées. Gardez à l’esprit que les machines virtuelles de faible priorité ne conviennent pas à toutes les charges de travail, car il se peut qu’elles ne soient pas disponibles pour l’allocation, ou qu’elles puissent être réquisitionnées.

### <a name="use-ephemeral-os-disks"></a>Utiliser des disques de système d’exploitation éphémères

Par défaut, les nœuds de pool utilisent des disques managés, ce qui entraîne des coûts. Les pools de configuration de machines virtuelles de certaines machines virtuelles peuvent utiliser des [disques de système d’exploitation éphémères](create-pool-ephemeral-os-disk.md), qui créent le disque de système d’exploitation sur le cache de la machine virtuelle ou sur un SSD temporaire, afin d’éviter les coûts supplémentaires associés aux disques managés.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Achat de réservations pour les instances de machine virtuelle

Si vous avez l’intention d’utiliser Batch sur une longue période, vous pouvez réduire le coût des machines virtuelles en utilisant des [Réservations Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) pour vos charges de travail. Le coût d’une réservation est nettement inférieur à celui d’un paiement à l'utilisation. Les instances de machines virtuelles utilisées sans réservation sont facturées à l’utilisation. Lorsque vous achetez une réservation, la remise de réservation est appliquée. Avec l’engagement dans des plans d’une ou trois ans pour les [instances de machine virtuelle](../virtual-machines/prepay-reserved-vm-instances.md), d’importantes remises sont appliquées à l’utilisation des machines virtuelles, notamment les [machines virtuelles consommées via des pools Batch](../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).

Il est important de noter que la remise de réservation est « utilisée ou perdue ».  Si aucune ressource correspondante n’est utilisée pendant une heure, vous perdez la quantité de réservation pour cette heure. Les heures réservées inutilisées ne peuvent pas être reportées et sont donc perdues si elles ne sont pas utilisées. Les charges de travail Batch font souvent évoluer le nombre de machines virtuelles allouées en fonction de la charge et ont une charge variable, notamment pendant les périodes où il n’y a pas de charge. Il est par conséquent nécessaire de déterminer la quantité de réservation, étant donné que les heures réservées sont perdues si les machines virtuelles Batch connaissent un scale-down sous la quantité de réservation.

### <a name="use-automatic-scaling"></a>Mise à l’échelle automatique

La [mise à l'échelle automatique](batch-automatic-scaling.md) ajuste dynamiquement le nombre de machines virtuelles dans votre pool Batch en fonction des exigences du travail en cours. En mettant à l’échelle le pool selon la durée de vie d’un travail, la mise à l’échelle automatique garantit que les machines virtuelles effectuent un scale-up et sont utilisées uniquement quand il y a un travail à exécuter. Une fois le travail terminé ou quand il n’y a pas de travail, les machines virtuelles effectuent un scale-down automatiquement pour économiser les ressources de calcul. La mise à l'échelle vous permet de réduire le coût global de votre solution Batch en n'utilisant que les ressources nécessaires.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md).
- Apprenez-en davantage sur l’[utilisation de machines virtuelles de faible priorité avec Batch](batch-low-pri-vms.md).
