---
title: Planifier la gestion des coûts pour App Service
description: Découvrez comment planifier et gérer les coûts pour Azure App Service avec l’analyse du coût dans le portail Azure.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: ada4c1991a57c8252247c9617e097dc82cb3b4a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593982"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planifier et gérer les coûts pour Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Cet article explique comment planifier et gérer les coûts pour Azure App Service. Vous devez commencer par utiliser la calculatrice de prix Azure pour planifier les coûts d’Azure App Service avant d’ajouter des ressources pour le service aux coûts estimés. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. Une fois que vous avez commencé à utiliser des ressources App Service, utilisez les fonctionnalités de [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour définir des budgets et superviser les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Azure App Service ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts d’App Service. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés pour votre abonnement Azure, y compris les services tiers.

## <a name="relevant-costs-for-app-service"></a>Coûts appropriés pour App Service

App Service s’exécute sur une infrastructure Azure qui occasionne des coûts. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais. Vous devez gérer ce coût lorsque vous apportez des modifications aux ressources déployées.

### <a name="costs-that-accrue-with-azure-app-service"></a>Coûts occasionnés avec Azure App Service

Selon la fonctionnalité que vous utilisez dans App Service, vous pouvez créer les ressources occasionnant des coûts suivantes :

- **Plan App Service**  Requis pour héberger une application App Service.
- **Niveau isolé**  Un [réseau virtuel](../virtual-network/index.yml) est requis pour un environnement App Service.
- **Sauvegarde**  Un [Compte de stockage](../storage/index.yml) est requis pour effectuer des sauvegardes.
- **Journaux de diagnostic**  Vous pouvez sélectionner [Compte de stockage](../storage/index.yml) comme option de journalisation ou opérer une intégration avec [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).
- **Certificats App Service**  Les certificats que vous achetez dans Azure doivent être conservés dans [Azure Key Vault](../key-vault/index.yml).

Les autres ressources coûteuses pour App Service sont (voir [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/) pour plus d’informations).

- [Domaines App Service](manage-custom-dns-buy-domain.md)  Votre abonnement est facturé annuellement pour l’inscription du domaine si vous activez le renouvellement automatique.
- [Certificats App Service](configure-ssl-certificate.md#import-an-app-service-certificate)  Frais uniques au moment de l’achat. Si vous avez plusieurs sous-domaines à sécuriser, vous pouvez réduire les coûts en achetant un certificat générique au lieu de plusieurs certificats standard.
- [Liaisons de certificat basées sur IP](configure-ssl-bindings.md#create-binding)  La liaison est configurée sur un certificat au niveau de l’application. Des coûts sont exposés pour chaque liaison. Pour les niveaux **Standard** et supérieurs, la première liaison basée sur IP n’est pas facturée.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’additionner après la suppression des ressources

Lorsque vous supprimez toutes les applications d’un plan App Service, le plan continue d’occasionner des frais en fonction du niveau tarifaire configuré et du nombre d’instances. Pour éviter les frais indésirables, supprimez le plan ou mettez-le à l’échelle vers le niveau **Gratuit**.

Après suppression de ressources Azure App Service, il se peut que des ressources de services Azure connexes continuent d’exister. Les coûts continueront de s’accumuler jusqu’à ce que vous supprimiez ces ressources. Par exemple :

- réseau virtuel que vous avez créé pour un plan de App Service de niveau **Isolé** ;
- comptes de stockage que vous avez créés pour stocker des sauvegardes ou journaux de diagnostic ;
- Key Vault que vous avez créé pour stocker des certificats App Service ;
- espaces de noms Log Analytics que vous avez créés pour expédier les journaux de diagnostic ;
- [réservations d’instance ou d’horodatage](#azure-reservations) pour App Service qui n’ont pas encore expiré.

### <a name="using-monetary-credit-with-azure-app-service"></a>Utilisation de Crédit monétaire avec Azure App Service

Vous pouvez payer les frais Azure App Service avec votre crédit Paiement anticipé Azure (anciennement appelé « engagement financier »). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour régler des frais liés à des produits et services tiers, y compris ceux de la Place de marché Azure.

## <a name="estimate-costs"></a>Estimer les coûts

Un moyen simple d’estimer et d’optimiser le coût de votre App Service à l’avance consiste à utiliser la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

Pour utiliser la calculatrice de prix, cliquez sur **App Service** sous l’onglet **Produits**. Ensuite, faites défiler vers le bas pour utiliser la calculatrice. La capture d’écran suivante est un exemple qui ne reflète pas la tarification actuelle.

![Exemple d’estimation de coûts dans la calculatrice de prix Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

Lorsque vous créez une application App Service ou un plan App Service, vous pouvez voir les coûts estimés.

Pour créer une application et voir le prix estimé :

1. Dans la page Créer, faites défiler jusqu’à **Plan App Service**, puis cliquez sur **Créer**.
1. Tapez un nom, puis cliquez sur **OK**.
1. En regard de **SKU et taille**, cliquez sur **Modifier la taille**.
1. Consultez le prix estimé indiqué dans le résumé. La capture d’écran suivante est un exemple qui ne reflète pas la tarification actuelle.

    ![Vérifiez le coût estimé pour chaque niveau tarifaire dans le portail](media/overview-manage-costs/pricing-estimates.png)

Si votre abonnement Azure a une limite de dépense, Azure vous empêche de dépenser au-delà que le montant de votre crédit. À mesure que vous créez et utilisez des ressources Azure, vos crédits sont utilisés. Quand vous atteignez votre limite de crédit, les ressources que vous avez déployées sont désactivées pour le reste de cette période de facturation. Vous ne pouvez pas changer votre limite de crédit, mais vous pouvez la supprimer. Pour plus d’informations sur les limites de dépense, consultez [Limite de dépense Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Optimiser les coûts

À un niveau de base, les applications App Service sont facturées par le plan App Service qui les héberge. Les coûts associés à votre déploiement App Service dépendent de quelques facteurs principaux :

- **Niveau tarifaire**  Également appelé référence (SKU) du plan App Service. Les niveaux supérieurs fournissent davantage de cœurs de processeur, de mémoire, de stockage ou de fonctionnalités, ou de combinaisons de ceux-ci.
- **Nombre d’instances**  Les niveaux dédiés (de base et supérieurs) peuvent faire l’objet d’une montée en puissance parallèle, et chaque instance concernée occasionne des coûts.
- **Frais de timbre**  Au niveau Isolé, un coût forfaitaire est imputé sur votre environnement App Service, quel que soit le nombre d’applications ou d’instances de travail hébergées.

Un plan de App Service peut héberger plusieurs applications. En fonction de votre déploiement, vous pouvez économiser des coûts en hébergeant davantage d’applications sur un seul plan App Service (c’est-à-dire en hébergeant vos applications sur moins de plans App Service).

Pour plus d’informations, consultez [Présentation des plans d’Azure App Service](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Pour les charges de travail autres que de production

Pour tester App Service ou votre solution en exposant des coûts minimaux ou minimes, vous pouvez commencer par utiliser les deux niveaux de tarification d’entrée de gamme, **Gratuit** et **Partagé**, qui sont hébergés sur des instances partagées. Pour tester votre application sur des instances dédiées offrant de meilleures performances, vous pouvez effectuer une mise à niveau vers le niveau **De base**, qui prend en charge les applications Windows et Linux. 

> [!NOTE]
> **Tarification d’Azure Dev/Test**  Pour tester les charges de travail de préproduction nécessitant des niveaux supérieurs (tous les niveaux, à l’exception du niveau **Isolé**), les abonnés Visual Studio peuvent également tirer parti de la [Tarification d’Azure Dev/Test](https://azure.microsoft.com/pricing/dev-test/) qui offre des remises significatives.
>
> Les niveaux **Gratuit** et **Partagé**, ainsi que les remises liées à la Tarification d’Azure Dev/Test, ne sont pas couverts par un contrat SLA adossé à une garantie financière.

### <a name="production-workloads"></a>Charges de travail de production

Les charges de travail de production sont fournies avec la recommandation du niveau de tarification dédié **Standard** ou supérieur. Si le prix augmente pour les niveaux supérieurs, il vous offre également davantage de mémoire et de stockage, ainsi qu’un matériel plus performant, de sorte que vous disposez d’une plus grande densité d’application par instance de calcul. Cela se traduit par une diminution du nombre d’instances pour le même nombre d’applications et, par conséquent, des coûts réduits. En fait, le niveau **Premium v3** (le niveau non **Isolé** le plus élevé) est la méthode la plus économique pour servir votre application à grande échelle. En plus des économies, vous pouvez bénéficier de remises conséquentes sur les [réservations Premium v3](#azure-reservations).

> [!NOTE]
> Le niveau **Premium v3** prend en charge les conteneurs Windows et Linux. 

Une fois que vous avez choisi le niveau tarifaire souhaité, vous devez réduire au minimum les instances inactives. Dans un déploiement avec montée en puissance parallèle, vous risquez de gaspiller de l’argent pour des instances de calcul sous-utilisées. Vous devez [configurer une mise à l’échelle automatique](../azure-monitor/autoscale/autoscale-get-started.md), disponible aux niveaux **Standard** et supérieurs. En créant des planifications de montée en puissance parallèle, ainsi que des règles de montée en puissance parallèle basées sur des métriques, vous payez uniquement pour les instances dont vous avez vraiment besoin à un moment donné.

### <a name="azure-reservations"></a>Réservations Azure

Si vous envisagez d’utiliser un nombre minimal connu d’instances de calcul pendant au moins un an, vous devriez profiter du niveau **Premium v3** et réduire drastiquement le coût d’instance en réservant ces instances par incréments de 1 ou 3 ans. Les économies mensuelles peuvent atteindre jusqu’à 55 % par instance. Deux types de réservations sont possibles :

- **Windows (ou plateforme non spécifiée)**  Peuvent s’appliquer aux instances tant Windows que Linux dans votre abonnement.
- **Spécifique de Linux**  S’applique uniquement aux instances Linux dans votre abonnement.

La tarification d’instance réservée s’applique aux instances applicables dans votre abonnement, jusqu’au nombre d’instances que vous réservez. Les instances réservées sont une question de facturation. Elles ne sont pas liées à des instances de calcul spécifiques. Si vous exécutez moins d’instances que vous n’en réservez à un moment donné de la période de réservation, vous payez toujours pour les instances réservées. Si vous exécutez plus d’instances que vous n’en réservez à un moment donné de la période de réservation, vous payez le coût cumulé normal pour les instances supplémentaires.

Le niveau **Isolé** (environnement App Service) prend également en charge les réservations de 1 et 3 ans à tarif réduit. Pour plus d’informations, consultez [Comment les remises de réservation s’appliquent à Azure App Service](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources Azure avec App Service, vous exposez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient en fonction d’intervalles de temps (secondes, minutes, heures et jours). Dès que l’utilisation d’App Service démarre, des coûts sont occasionnés, que vous pouvez voir les coûts dans [Analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous effectuez une analyse du coût, vous voyez les coûts d’App Service dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.
    
Pour consulter les coûts d’App Service dans l’analyse du coût :

1. Connectez-vous au portail Azure.
2. Ouvrez l’étendue dans le portail Azure et sélectionnez **Analyse des coûts** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. Sélectionnez **Étendue** pour passer à une autre étendue dans l’analyse des coûts.
3. Par défaut, le coût des services est affiché dans le premier graphique en anneau. Dans le graphique, sélectionnez la zone intitulée App Service.

Les coûts mensuels réels s’affichent lors de l’ouverture initiale de l’analyse des coûts. Voici un exemple qui montre tous les coûts d’utilisation mensuelle.

![Exemple montrant les coûts cumulés pour un abonnement](media/overview-manage-costs/all-costs.png)

Pour limiter les coûts à un seul service, tel App Service, sélectionnez **Ajouter un filtre**, puis **Nom du service**. Ensuite, sélectionnez **App Service**.

Voici un exemple montrant les coûts d’App Service uniquement.

![Exemple montrant les coûts cumulés pour Nom du service](media/overview-manage-costs/service-specific-costs.png)

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et les coûts d’App Service par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.

## <a name="create-budgets"></a>Créer des budgets

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre disponibles lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer une analyse supplémentaire des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail comment fonctionnent les tarifs avec Stockage Azure. Consultez la [tarification App Service](https://azure.microsoft.com/pricing/details/app-service/).
- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->