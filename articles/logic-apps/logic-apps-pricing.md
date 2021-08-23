---
title: Modèles de tarification et de facturation
description: Vue d’ensemble du fonctionnement des modèles de tarification et de facturation dans Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 3b627abdf27907a5c0739e6dd7920932a3035ee7
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111981694"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modèles de tarification et de facturation pour Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous aide à créer et à exécuter des flux de travail d’intégration automatisés et pouvant être mis à l’échelle dans le cloud. Cet article décrit le fonctionnement des modèles de facturation et de tarification pour Azure Logic Apps et les ressources associées. Pour connaître les tarifs spécifiques, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps). Pour savoir comment prévoir, gérer et surveiller les coûts, consultez [Prévoir et gérer les coûts d’Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-multi-tenant"></a>Tarification de la consommation (mutualisée)

Un modèle tarifaire de la consommation par paiement à l’utilisation s’applique aux applications logiques qui s’exécutent dans l’environnement Azure Logic Apps multilocataire, « mondial » et public. Vous pouvez créer ces applications logiques en utilisant différentes solutions, par exemple :

* Type de ressource Logic App (Consommation) dans le portail Azure
* Extension Azure Logic Apps (Consommation) dans Visual Studio Code
* Extension Outils Azure Logic Apps dans Visual Studio
* Modèle Azure Resource Manager (modèle ARM) à l’aide du type de ressource `Microsoft.Logic`
* Azure CLI pour Azure Logic Apps à l’aide des commandes `az logic`
* Azure PowerShell pour Azure Logic Apps à l’aide du module `Az.LogicApp`
* API REST pour Azure Logic Apps
* [Tâches d’automatisation](create-automation-tasks-azure-resources.md) dans le portail Azure

Le comptage et la facturation sont basés sur les exécutions de déclencheur et d’action dans un workflow d’application logique. Ces exécutions sont mesurées et facturées, que le workflow s’exécute normalement ou qu’il soit même instancié. Par exemple, supposons que votre tâche d’automatisation utilise un déclencheur d’interrogation qui effectue régulièrement un appel sortant à un point de terminaison. Cette requête sortante est mesurée et facturée en tant qu’exécution, que le déclencheur soit activé ou non, ce qui détermine si une instance de workflow est créée ou non.

| Éléments | Description |
|-------|-------------|
| Déclencheurs et actions [intégrés](../connectors/built-in.md) | Exécutés en mode natif dans le service Logic Apps et sont comptabilisés selon le tarif [**Actions**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Par exemple, le déclencheur HTTP et le déclencheur Requête sont des déclencheurs intégrés, tandis que l’action HTTP et l’action Réponse sont des actions intégrées. Les opérations de données, les opérations de traitement par lots, les opérations de variables et les [actions de contrôle de workflow](../connectors/built-in.md), telles que les boucles, les conditions, les basculements, les branches parallèles, etc., sont également des actions intégrées. <p><p>**Remarque** : En prime, le plan de consommation comprend tous les mois plusieurs milliers d’exécutions intégrées gratuites. |
| Déclencheurs et actions de [connecteur standard](../connectors/managed.md) <p><p>Déclencheurs et actions de [connecteur personnalisé](../connectors/apis-list.md#custom-apis-and-connectors) | Comptabilisés selon le [tarif du connecteur standard](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Déclencheurs et actions de [connecteur entreprise](../connectors/managed.md) | Comptabilisés selon le [tarif du connecteur entreprise](https://azure.microsoft.com/pricing/details/logic-apps/). Toutefois, durant la préversion du connecteur, les connecteurs entreprise sont comptabilisés selon le [tarif du connecteur *standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Actions dans des [boucles](logic-apps-control-flow-loops.md) | Chaque action qui s’exécute dans une boucle est comptabilisée pour chaque cycle de boucle qui s’exécute. <p><p>Par exemple, supposons que vous ayez une boucle « for each » qui comprend des actions qui traitent une liste. Le service Logic Apps comptabilise chaque action qui s’exécute dans cette boucle en multipliant le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoute l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action. |
| Nouvelles tentatives | Pour gérer les exceptions et les erreurs les plus simples, vous pouvez configurer une [stratégie de nouvelles tentatives](logic-apps-exception-handling.md#retry-policies) sur les déclencheurs et les actions qui sont pris en charge. Ces nouvelles tentatives, ainsi que la demande initiale, sont facturées à des tarifs qui varient selon que le déclencheur ou l’action est de type intégré, Standard ou Entreprise. Par exemple, une action qui s’exécute avec deux nouvelles tentatives est facturée pour trois exécutions d’action. |
| [Conservation des données et utilisation du stockage](#data-retention) | Comptabilisée selon le tarif de conservation des données, que vous pouvez trouver sur la [page de tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), sous le tableau **Détails des prix**. |
|||

Pour plus d’informations, consultez la documentation suivante :

* [Afficher les métriques pour les exécutions et l’utilisation du stockage](plan-manage-costs.md#monitor-billing-metrics)
* [Limites dans Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Non comptabilisés

* Déclencheurs ignorés en raison de conditions non satisfaites
* Actions qui n’ont pas été exécutées, car l’application logique s’est arrêtée avant la fin
* [Applications logiques désactivées](#disabled-apps)

### <a name="other-related-resources"></a>Autres ressources associées

Logic Apps fonctionne avec d’autres ressources associées, telles que des comptes d’intégration, des passerelles de données locales et des environnements de service d’intégration (ISE). Pour en savoir plus sur la tarification de ces ressources, consultez les sections suivantes plus loin dans cette rubrique :

* [On-premises data gateway (Passerelle de données locale)](#data-gateway)
* [Modèle de tarification d’un compte d’intégration](#integration-accounts)
* [Modèle de tarification d’un ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Conseils pour estimer les coûts de consommation

Pour vous aider à estimer plus précisément les coûts de consommation, consultez les conseils suivants :

* Ne basez pas vos calculs juste sur l’intervalle d’interrogation, mais prenez plutôt en compte le nombre possible de messages ou d’événements qui pourraient arriver chaque jour.

* Lorsqu’un événement ou un message répond aux critères de déclencheurs, plusieurs déclencheurs essaient immédiatement de lire les autres événements ou messages en attente qui répondent aux critères. Ce comportement signifie que le déclencheur se lance selon le nombre d’événements ou de messages en attente qualifiés pour le démarrage de workflows, même si vous sélectionnez un intervalle d’interrogation plus étendue. Les déclencheurs qui adoptent ce comportement incluent Azure Service Bus et Azure Event Hub.

  Par exemple, imaginons que vous configuriez un déclencheur qui vérifie un point de terminaison par jour. Lorsqu’il vérifie le point de terminaison et trouve 15 événements qui répondent aux critères, il se lance et exécute le workflow correspondant 15 fois. Le service Logic Apps mesure toutes les actions réalisées par ces 15 workflows, dont les requêtes du déclencheur.

<a name="standard-pricing"></a>

## <a name="standard-pricing-single-tenant"></a>Tarification standard (monolocataire)

Un modèle tarifaire basé sur le niveau tarifaire et le plan d’hébergement s’applique aux applications logiques qui s’exécutent dans l’environnement Azure Logic Apps monolocataire. Ce tarif s’applique au type de ressource **Logic Apps (Standard)** dans le portail Azure, ou aux applications logiques sur lesquelles vous travaillez avec l’extension **Azure Logic Apps (Standard)** pour Visual Studio Code. Lorsque vous créez ou déployez une telle application logique, vous devez choisir un plan d’hébergement et un niveau tarifaire qui déterminent les tarifs à pratiquer pour le comptage et la facturation lors de l’exécution de vos workflows.

> [!NOTE]
> Pour les nouvelles applications logiques que vous créez avec le type de ressource **Logic App (Standard)** , vous devez utiliser le plan d’hébergement **Workflow standard**. Le plan App Service et App Service Environment ne sont pas disponibles pour les nouvelles applications logiques.

<a name="hosting-plans"></a>

### <a name="pricing-tiers-and-billing-rates"></a>Niveaux tarifaires et tarifs de facturation

Dans un plan d’hébergement, chaque niveau tarifaire comprend une quantité spécifique de ressources en calcul, en mémoire et en stockage. Pour les tarifs horaires par ressource et par région, consultez la [page des tarifs Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Pour mieux comprendre comment fonctionnent les tarifs, cet exemple fournit des estimations à titre indicatif pour la *région USA Est 2*.

* Dans la [page des tarifs Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), sélectionnez la région **USA Est 2** pour voir les tarifs à l’heure, ou consultez le tableau suivant :

  | Ressource | Horaire (USA Est 2) |
  |----------|------------------------|
  | **Processeur virtuel** | 0,192 USD |
  | **Mémoire** | 0,0137 USD par Go |
  |||

* Basé sur les informations ci-dessus, le tableau suivant indique le tarif mensuel estimé pour chaque niveau tarifaire et les ressources comprises dans chacun d’eux :

  | Niveau tarifaire | Mensuel USD (USA Est 2) | Processeur virtuel | Mémoire (Go) | Stockage (Go) |
  |--------------|-------------------------|--------------------|-------------|--------------|
  | **WS1** | 175,20 USD | 1 | 3,5 | 250 |
  | **WS2** | 350,40 USD | 2 | 7 | 250 |
  | **WS3** | 700,80 USD | 4 | 14 | 250 |
  ||||||

* Basé sur les informations ci-dessus, ce tableau liste chaque ressource et le tarif mensuel estimé si vous choisissez le niveau tarifaire **WS1** :

  | Ressource | Montant | Mensuel USD (USA Est 2) |
  |----------|--------|-------------------------|
  | **Processeur virtuel** | 1 | 140,16 USD |
  | **Mémoire** | 3,5 Go | 35,04 USD |
  ||||

<a name="storage-transactions"></a>

### <a name="storage-transactions"></a>Transactions de stockage

Azure Logic Apps utilise [Stockage Azure](../storage/index.yml) pour toutes les opérations de stockage. Avec Azure Logic Apps multilocataire, toute utilisation de stockage et tout coût sont attachés à l’application logique. Avec Azure Logic Apps monolocataire, vous pouvez utiliser votre propre [compte de stockage](../azure-functions/storage-considerations.md#storage-account-requirements) Azure. Cette possibilité vous offre davantage de contrôle et de flexibilité sur vos données Logic Apps.

Quand des workflows *avec état* exécutent leurs opérations, le runtime Azure Logic Apps effectue les transactions de stockage. Par exemple, des files d’attente sont utilisées pour la planification, tandis que des tables et blobs sont utilisés pour stocker les états de flux de travail. Les coûts de stockage changent en fonction du contenu de votre workflow. Les différents déclencheurs, actions et charges utiles entraînent des opérations et des besoins de stockage différents. Les transactions de stockage suivent le [modèle de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Les coûts de stockage sont indiqués séparément dans votre facture Azure.

### <a name="tips-for-estimating-storage-needs-and-costs"></a>Conseils pour l’estimation des besoins et des coûts de stockage

Pour vous faire une idée du nombre d’opérations de stockage qu’un workflow pourrait exécuter et de leur coût, essayez d’utiliser la [calculatrice de stockage Logic Apps](https://logicapps.azure.com/calculator). Vous pouvez sélectionner un exemple de workflow ou utiliser une définition de workflow existante. Le premier calcul estime le nombre d’opérations de stockage dans votre workflow. Vous pouvez ensuite utiliser ces numéros pour estimer les coûts possibles à l’aide de la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

Pour plus d’informations, consultez la documentation suivante :

* [Estimer les besoins et coûts de stockage des workflows dans des applications Azure Logic Apps monolocataires](estimate-storage-costs.md)
* [Détails de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/)

<a name="fixed-pricing"></a>

## <a name="ise-pricing-dedicated"></a>Tarification ISE (dédiée)

Un modèle de tarification fixe s’applique aux applications logiques qui s’exécutent dans l’[*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) dédié. Un ISE est facturé selon la [tarification d’un environnement de service d’intégration](https://azure.microsoft.com/pricing/details/logic-apps), qui dépend du [niveau ISE ou *niveau tarifaire*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) que vous créez. Cette tarification diffère de la tarification multilocataire, car vous payez pour la capacité de réserve et les ressources dédiées, que vous les utilisiez ou non.

| Référence (SKU) de l’environnement de service d’intégration | Description |
|---------|-------------|
| **Premium** | L’unité de base a une [capacité fixe](logic-apps-limits-and-config.md#integration-service-environment-ise) et est [facturée à un tarif horaire pour le niveau tarifaire Premium](https://azure.microsoft.com/pricing/details/logic-apps). Si vous avez besoin d’un débit plus élevé, vous pouvez [ajouter des unités d’échelle supplémentaires](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) pendant ou après la création de votre ISE. Chaque unité d’échelle est facturée à un [tarif horaire qui correspond à peu près à la moitié du tarif de l’unité de base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Développeur** | L’unité de base a une [capacité fixe](logic-apps-limits-and-config.md#integration-service-environment-ise) et est [facturée à un tarif horaire pour le niveau tarifaire Développeur](https://azure.microsoft.com/pricing/details/logic-apps). Toutefois, cette référence SKU n’a pas de contrat de niveau de service (SLA), de fonctionnalité de scale-up ou de redondance pendant le recyclage, ce qui signifie que vous pouvez rencontrer des retards ou des temps d’arrêt. Les mises à jour du back-end peuvent interrompre le service par intermittence. <p><p>**Important !** Veillez à utiliser cette référence SKU uniquement à des fins d’exploration, d’expérimentation, de développement ou de test, et non pour la production ou les tests de performances. <p><p>Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Inclus sans frais supplémentaires

| Éléments | Description |
|-------|-------------|
| Déclencheurs et actions [intégrés](../connectors/built-in.md) | Affichent l’étiquette **Core** et s’exécutent dans le même ISE que vos applications logiques. |
| [Connecteurs standard](../connectors/managed.md) <p><p>[Connecteurs d’entreprise](../connectors/managed.md#enterprise-connectors) | – Les connecteurs managés qui comportent l’étiquette **ISE** sont spécialement conçus pour fonctionner sans la passerelle de données locale et s’exécutent dans le même ISE que vos applications logiques. La tarification d’un ISE comprend autant de connexions entreprise que vous le souhaitez. <p><p>– Les connecteurs qui ne portent pas l’étiquette ISE s’exécutent dans le service Azure Logic Apps monolocataire. Toutefois, la tarification d’un ISE comprend ces exécutions pour les applications logiques qui s’exécutent dans un ISE. |
| Actions dans des [boucles](logic-apps-control-flow-loops.md) | La tarification d’un ISE comprend chaque action qui s’exécute dans une boucle pour chaque cycle de boucle qui s’exécute. <p><p>Par exemple, supposons que vous ayez une boucle « for each » qui comprend des actions qui traitent une liste. Pour connaître le nombre total d’exécutions d’action, multipliez le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoutez l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action. |
| Nouvelles tentatives | Pour gérer les exceptions et les erreurs les plus simples, vous pouvez configurer une [stratégie de nouvelles tentatives](logic-apps-exception-handling.md#retry-policies) sur les déclencheurs et les actions qui sont pris en charge. La tarification d’un ISE comprend les nouvelles tentatives, ainsi que la demande initiale. |
| [Conservation des données et utilisation du stockage](#data-retention) | Dans un ISE, les applications logiques n’entraînent pas de frais de conservation des données ni d’utilisation de stockage. |
| [Comptes d’intégration](#integration-accounts) | Comprend l’utilisation d’un seul niveau de compte d’intégration, basé sur le niveau tarifaire ISE, sans coût supplémentaire. |
|||

Pour plus d’informations sur les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Comptes d’intégration

Un [compte d’intégration](../logic-apps/logic-apps-pricing.md#integration-accounts) est une ressource distincte que vous créez et liez à une application logique, afin de pouvoir explorer, générer et tester des solutions d’intégration B2B qui utilisent [EDI](logic-apps-enterprise-integration-b2b.md) et les capacités de [traitement XML](logic-apps-enterprise-integration-xml.md).

Azure Logic Apps offre ces niveaux de service ou niveaux de compte d’intégration qui [varient en fonction du tarif](https://azure.microsoft.com/pricing/details/logic-apps/) et du [modèle de facturation](logic-apps-pricing.md#integration-accounts), selon que votre application logique s’exécute dans Azure Logic Apps (multilocataire ou monolocataire) ou dans un ISE.

| Niveau | Description |
|------|-------------|
| **De base** | Pour les scénarios où vous souhaitez utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante. <p><p>Pris en charge par le contrat SLA Logic Apps. |
| **Standard** | Pour les scénarios où vous avez des relations B2B plus complexes et un nombre croissant d’entités que vous devez gérer. <p><p>Pris en charge par le contrat SLA Logic Apps. |
| **Gratuit** | Pour les scénarios exploratoires, et non pour les scénarios de production. Ce niveau de service est limité quant à la disponibilité, au débit et à l’utilisation des régions. Par exemple, le niveau Gratuit est disponible uniquement pour les régions publiques dans Azure, par exemple, USA Ouest et Asie Sud-Est, mais pas pour [Azure China 21Vianet](/azure/china/overview-operations) ni [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Remarque** : Non pris en charge par le contrat SLA Logic Apps. |
|||

Pour plus d’informations sur les limites des comptes d’intégration, consultez [Limites et configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), par exemple :

* [Limites sur les comptes d’intégration par abonnement Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limites sur différents artefacts par compte d’intégration](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Les artefacts incluent les partenaires commerciaux, les contrats, les mappages, les schémas, les assemblys, les certificats et les configurations de lots, etc.

### <a name="multi-tenant-or-single-tenant-based-logic-apps"></a>Applications logiques basées sur une architecture multilocataire ou monolocataire

Les comptes d’intégration sont facturés selon le [prix fixe d’un compte d’intégration](https://azure.microsoft.com/pricing/details/logic-apps/) basé sur le niveau de compte que vous utilisez.

### <a name="ise-based-logic-apps"></a>Applications logiques basées sur un ISE

Sans coût supplémentaire, votre ISE comprend un seul compte d’intégration basé sur votre niveau tarifaire ISE. Pour un coût supplémentaire, vous pouvez créer davantage de comptes d’intégration pour votre ISE pour utiliser [jusqu’à la limite totale d’ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). En savoir plus sur le [modèle de tarification ISE](#fixed-pricing) plus haut dans cette rubrique.

| Référence (SKU) de l’environnement de service d’intégration | Compte d’intégration inclus | Coût supplémentaire |
|---------|------------------------------|-----------------|
| **Premium** | Un seul compte d’intégration [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Jusqu’à 19 comptes Standard supplémentaires. Aucun compte Gratuit ou De base n’est autorisé. |
| **Développeur** | Un seul compte d’intégration [Gratuit](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Jusqu’à 19 comptes Standard supplémentaires si vous disposez déjà d’un compte Gratuit, ou 20 comptes Standard au total si vous n’avez pas de compte Gratuit. Aucun compte De base n’est autorisé. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-usage"></a>Conservation des données et utilisation du stockage

Azure Logic Apps utilise [Stockage Azure](../storage/index.yml) pour toutes les opérations de stockage. Les entrées et sorties de l’historique des exécutions de votre workflow sont stockées et contrôlées en fonction de la [limite de conservation de l’historique des exécutions](logic-apps-limits-and-config.md#run-duration-retention-limits) de votre application logique. Pour superviser l’utilisation du stockage, consultez [Afficher les métriques pour les exécutions et l’utilisation du stockage](plan-manage-costs.md#monitor-billing-metrics).

| Environnement | Notes |
|-------------|-------|
| **Multi-locataire** | L’utilisation du stockage et la conservation des données sont facturées selon un tarif fixe, vous le trouvez à la [page des tarifs Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps), sous le tableau **Détails des prix**. |
| **Monolocataire** | L’utilisation du stockage et la conservation des données sont facturées au moyen du [modèle tarifaire du stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Les coûts de stockage sont indiqués séparément dans votre facture Azure. Pour plus d’informations, consultez [Transactions de stockage (monolocataire)](#storage-transactions). |
| **ISE** | L’utilisation du stockage et la conservation des données ne sont pas facturées. |
|||

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Passerelle de données locale

Une [passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) est une ressource distincte que vous créez pour que les workflows de vos applications logiques puissent accéder aux données locales en utilisant des connecteurs spécifiques pris en charge par la passerelle. Les opérations des connecteurs qui s’exécutent via la passerelle entraînent des frais, mais la passerelle elle-même n’est pas facturée.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps-or-workflows"></a>Applications logiques ou workflows désactivés

Les applications logiques (multilocataires) ou les workflows (monolocataires) n’entraînent pas de frais, car ils ne peuvent pas créer de nouvelles instances tant qu’ils sont désactivés.

## <a name="next-steps"></a>Étapes suivantes

* [Prévoir et gérer les coûts d’Azure Logic Apps](plan-manage-costs.md)