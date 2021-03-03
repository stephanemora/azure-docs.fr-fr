---
title: Modèles de tarification et de facturation
description: Vue d’ensemble du fonctionnement des modèles de tarification et de facturation dans Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 103855748c4b5d998dfc81eeb4044f5f53dae9e5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372006"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modèles de tarification et de facturation pour Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous aide à créer et à exécuter des flux de travail d’intégration automatisés et pouvant être mis à l’échelle dans le cloud. Cet article décrit le fonctionnement des modèles de facturation et de tarification pour le service Logic Apps et les ressources associées. Pour connaître les tarifs spécifiques, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps). Pour savoir comment prévoir, gérer et surveiller les coûts, consultez [Prévoir et gérer les coûts d’Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Tarification multilocataire

Un modèle de tarification de la consommation par paiement à l’utilisation s’applique aux applications logiques qui s’exécutent dans le service Logic Apps multilocataire, « mondial » et public. Toutes les exécutions réussies et infructueuses sont comptabilisées et facturées.

Par exemple, la demande d’un déclencheur d’interrogation est toujours comptabilisée comme une exécution, même si ce déclencheur est ignoré, et aucune instance de workflow d’application logique n’est créée.

| Éléments | Description |
|-------|-------------|
| Déclencheurs et actions [intégrés](../connectors/apis-list.md#built-in) | Exécutés en mode natif dans le service Logic Apps et sont comptabilisés selon le tarif [**Actions**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Par exemple, le déclencheur HTTP et le déclencheur Requête sont des déclencheurs intégrés, tandis que l’action HTTP et l’action Réponse sont des actions intégrées. Les opérations de données, les opérations de traitement par lots, les opérations de variables et les [actions de contrôle de workflow](../connectors/apis-list.md#control-workflow), telles que les boucles, les conditions, les basculements, les branches parallèles, etc., sont également des actions intégrées. |
| Déclencheurs et actions de [connecteur standard](../connectors/apis-list.md#managed-connectors) <p><p>Déclencheurs et actions de [connecteur personnalisé](../connectors/apis-list.md#custom) | Comptabilisés selon le [tarif du connecteur standard](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Déclencheurs et actions de [connecteur entreprise](../connectors/apis-list.md#managed-connectors) | Comptabilisés selon le [tarif du connecteur entreprise](https://azure.microsoft.com/pricing/details/logic-apps/). Toutefois, durant la préversion publique, les connecteurs entreprise sont comptabilisés selon le [tarif du connecteur *standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Actions dans des [boucles](logic-apps-control-flow-loops.md) | Chaque action qui s’exécute dans une boucle est comptabilisée pour chaque cycle de boucle qui s’exécute. <p><p>Par exemple, supposons que vous ayez une boucle « for each » qui comprend des actions qui traitent une liste. Le service Logic Apps comptabilise chaque action qui s’exécute dans cette boucle en multipliant le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoute l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action. |
| Nouvelles tentatives | Pour gérer les exceptions et les erreurs les plus simples, vous pouvez configurer une [stratégie de nouvelles tentatives](logic-apps-exception-handling.md#retry-policies) sur les déclencheurs et les actions qui sont pris en charge. Ces nouvelles tentatives, ainsi que la demande initiale, sont facturées à des tarifs qui varient selon que le déclencheur ou l’action est de type intégré, Standard ou Entreprise. Par exemple, une action qui s’exécute avec deux nouvelles tentatives est facturée pour trois exécutions d’action. |
| [Conservation des données et consommation du stockage](#data-retention) | Comptabilisée selon le tarif de conservation des données, que vous pouvez trouver sur la [page de tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), sous le tableau **Détails des prix**. |
|||

Pour plus d’informations, consultez les rubriques suivantes :

* [Afficher les métriques pour les exécutions et la consommation du stockage](plan-manage-costs.md#monitor-billing-metrics)
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

* Lorsqu’un événement ou un message répond aux critères de déclencheurs, plusieurs déclencheurs essaient immédiatement de lire tous les autres événements ou messages en attente qui répondent aux critères. Ce comportement signifie que le déclencheur se lance selon le nombre d’événements ou de messages en attente qualifiés pour le démarrage de workflows, même si vous sélectionnez un intervalle d’interrogation plus étendue. Les déclencheurs qui adoptent ce comportement incluent Azure Service Bus et Azure Event Hub.

  Par exemple, imaginons que vous configuriez un déclencheur qui vérifie un point de terminaison par jour. Lorsqu’il vérifie le point de terminaison et trouve 15 événements qui répondent aux critères, il se lance et exécute le workflow correspondant 15 fois. Le service Logic Apps mesure toutes les actions réalisées par ces 15 workflows, dont les requêtes du déclencheur.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Tarification d’un ISE

Un modèle de tarification fixe s’applique aux applications logiques qui s’exécutent dans un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un ISE est facturé selon la [tarification d’un environnement de service d’intégration](https://azure.microsoft.com/pricing/details/logic-apps), qui dépend du [niveau ISE ou *niveau tarifaire*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) que vous créez. Cette tarification diffère de la tarification multilocataire, car vous payez pour la capacité de réserve et les ressources dédiées, que vous les utilisiez ou non.

| Référence (SKU) de l’environnement de service d’intégration | Description |
|---------|-------------|
| **Premium** | L’unité de base a une [capacité fixe](logic-apps-limits-and-config.md#integration-service-environment-ise) et est [facturée à un tarif horaire pour le niveau tarifaire Premium](https://azure.microsoft.com/pricing/details/logic-apps). Si vous avez besoin d’un débit plus élevé, vous pouvez [ajouter des unités d’échelle supplémentaires](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) pendant ou après la création de votre ISE. Chaque unité d’échelle est facturée à un [tarif horaire qui correspond à peu près à la moitié du tarif de l’unité de base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Développeur** | L’unité de base a une [capacité fixe](logic-apps-limits-and-config.md#integration-service-environment-ise) et est [facturée à un tarif horaire pour le niveau tarifaire Développeur](https://azure.microsoft.com/pricing/details/logic-apps). Toutefois, cette référence SKU n’a pas de contrat de niveau de service (SLA), de fonctionnalité de scale-up ou de redondance pendant le recyclage, ce qui signifie que vous pouvez rencontrer des retards ou des temps d’arrêt. Les mises à jour du back-end peuvent interrompre le service par intermittence. <p><p>**Important !** Veillez à utiliser cette référence SKU uniquement à des fins d’exploration, d’expérimentation, de développement ou de test, et non pour la production ou les tests de performances. <p><p>Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Inclus sans frais supplémentaires

| Éléments | Description |
|-------|-------------|
| Déclencheurs et actions [intégrés](../connectors/apis-list.md#built-in) | Affichent l’étiquette **Core** et s’exécutent dans le même ISE que vos applications logiques. |
| [Connecteurs standard](../connectors/apis-list.md#managed-connectors) <p><p>[Connecteurs d’entreprise](../connectors/apis-list.md#enterprise-connectors) | – Les connecteurs managés qui comportent l’étiquette **ISE** sont spécialement conçus pour fonctionner sans la passerelle de données locale et s’exécutent dans le même ISE que vos applications logiques. La tarification d’un ISE comprend autant de connexions entreprise que vous le souhaitez. <p><p>– Les connecteurs qui ne portent pas l’étiquette ISE s’exécutent dans le service Logic Apps multilocataire. Toutefois, la tarification d’un ISE comprend ces exécutions pour les applications logiques qui s’exécutent dans un ISE. |
| Actions dans des [boucles](logic-apps-control-flow-loops.md) | La tarification d’un ISE comprend chaque action qui s’exécute dans une boucle pour chaque cycle de boucle qui s’exécute. <p><p>Par exemple, supposons que vous ayez une boucle « for each » qui comprend des actions qui traitent une liste. Pour connaître le nombre total d’exécutions d’action, multipliez le nombre d’éléments dans la liste par le nombre d’actions dans la boucle, puis ajoutez l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action. |
| Nouvelles tentatives | Pour gérer les exceptions et les erreurs les plus simples, vous pouvez configurer une [stratégie de nouvelles tentatives](logic-apps-exception-handling.md#retry-policies) sur les déclencheurs et les actions qui sont pris en charge. La tarification d’un ISE comprend les nouvelles tentatives, ainsi que la demande initiale. |
| [Conservation des données et consommation du stockage](#data-retention) | Les applications logiques dans un ISE n’entraînent pas de coûts de rétention ni de stockage. |
| [Comptes d’intégration](#integration-accounts) | Comprend l’utilisation d’un seul niveau de compte d’intégration, basé sur le niveau tarifaire ISE, sans coût supplémentaire. |
|||

Pour plus d’informations sur les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Comptes d’intégration

Un [compte d’intégration](../logic-apps/logic-apps-pricing.md#integration-accounts) est une ressource distincte que vous créez et liez à des applications logiques afin que vous puissiez explorer, générer et tester des solutions d’intégration B2B qui utilisent [EDI](logic-apps-enterprise-integration-b2b.md) et les capacités de [traitement XML](logic-apps-enterprise-integration-xml.md). Azure Logic Apps propose les niveaux de service ou niveaux de compte d’intégration suivants :

| Niveau | Description |
|------|-------------|
| **De base** | Pour les scénarios où vous souhaitez utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante. <p><p>Pris en charge par le contrat SLA Logic Apps. |
| **Standard** | Pour les scénarios où vous avez des relations B2B plus complexes et un nombre croissant d’entités que vous devez gérer. <p><p>Pris en charge par le contrat SLA Logic Apps. |
| **Gratuit** | Pour les scénarios exploratoires, et non pour les scénarios de production. Ce niveau de service est limité quant à la disponibilité, au débit et à l’utilisation des régions. Par exemple, le niveau Gratuit est disponible uniquement pour les régions publiques dans Azure, par exemple, USA Ouest et Asie Sud-Est, mais pas pour [Azure China 21Vianet](/azure/china/overview-operations) ni [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Remarque** : Non pris en charge par le contrat SLA Logic Apps. |
|||

Pour plus d’informations sur les limites des comptes d’intégration, consultez [Limites et configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), par exemple :

* [Limites sur les comptes d’intégration par abonnement Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limites sur différents artefacts par compte d’intégration](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Les artefacts incluent les partenaires commerciaux, les contrats, les mappages, les schémas, les assemblys, les certificats et les configurations de lots, etc.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Comptes d’intégration pour les applications logiques basées sur la consommation

Les comptes d’intégration sont facturés selon le [prix fixe d’un compte d’intégration](https://azure.microsoft.com/pricing/details/logic-apps/) basé sur le niveau de compte que vous utilisez.

### <a name="ise-based-logic-apps"></a>Applications logiques basées sur un ISE

Sans coût supplémentaire, votre ISE comprend un seul compte d’intégration basé sur votre niveau tarifaire ISE. Pour un coût supplémentaire, vous pouvez créer davantage de comptes d’intégration pour votre ISE pour utiliser [jusqu’à la limite totale d’ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). En savoir plus sur le [modèle de tarification ISE](#fixed-pricing) plus haut dans cette rubrique.

| Référence (SKU) de l’environnement de service d’intégration | Compte d’intégration inclus | Coût supplémentaire |
|---------|------------------------------|-----------------|
| **Premium** | Un seul compte d’intégration [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Jusqu’à 19 comptes Standard supplémentaires. Aucun compte Gratuit ou De base n’est autorisé. |
| **Développeur** | Un seul compte d’intégration [Gratuit](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Jusqu’à 19 comptes Standard supplémentaires si vous disposez déjà d’un compte Gratuit, ou 20 comptes Standard au total si vous n’avez pas de compte Gratuit. Aucun compte De base n’est autorisé. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Conservation des données et consommation du stockage

Toutes les entrées et sorties de l’historique des exécutions de votre application logique sont stockées et comptabilisées en fonction de la [durée d’exécution et de la période de rétention de l’historique](logic-apps-limits-and-config.md#run-duration-retention-limits) de l’application.

* Pour les applications logiques du service Logic Apps multilocataire, la consommation de stockage est facturée à un prix fixe, que vous pouvez trouver sur la [page de tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps), sous le tableau **Détails des prix**.

* Pour les applications logiques dans des ISE, la consommation de stockage n’entraîne pas de coûts de rétention.

Pour surveiller l’utilisation de la consommation de stockage, consultez [Afficher les métriques pour les exécutions et la consommation du stockage](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Passerelle de données locale

Une [passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) est une ressource distincte que vous créez afin que vos applications logiques puissent accéder aux données locales en utilisant des connecteurs pris en charge par la passerelle. Les opérations des connecteurs qui s’exécutent via la passerelle entraînent des frais, mais la passerelle elle-même n’est pas facturée.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Applications logiques désactivées

Les applications logiques désactivées ne sont pas facturées, car elles ne peuvent pas créer d’instances lorsqu’elles sont désactivées. Une fois que vous avez désactivé une application logique, l’arrêt complet des instances en cours d’exécution peut prendre un certain de temps.

## <a name="next-steps"></a>Étapes suivantes

* [Prévoir et gérer les coûts d’Azure Logic Apps](plan-manage-costs.md)
