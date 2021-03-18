---
title: États et facturation des machines virtuelles Azure
description: Vue d’ensemble des différents états dans lesquels une machine virtuelle peut entrer et des moments où un utilisateur est facturé.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 0325dcf16c8e637a58365311a4ebd37a442d6b8c
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525429"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>États et facturation des machines virtuelles Azure

Les machines virtuelles Microsoft Azure passent par différents états qui peuvent être classés en états *d’approvisionnement* et *d’alimentation*. Cet article vise à décrire ces états et en particulier à mettre en exergue la date de facturation des clients pour l’utilisation des instances. 

## <a name="get-states-using-instance-view"></a>Obtenir des états à l’aide de la vue d’instance

L’API de vue d’instance fournit des informations sur l’état d’exécution d’une machine virtuelle. Pour plus d’informations, consultez la documentation relative à l’API [Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview) (Machines virtuelles : vue d’instance).

Azure Resource Explorer présente une interface utilisateur simple permettant d’afficher l’état d’exécution des machines virtuelles : [Resource Explorer](https://resources.azure.com/).

Les états d’approvisionnement sont visibles dans la vue d’instance et les propriétés des machines virtuelles. Les états d’alimentation sont disponibles dans la vue d’instance des machines virtuelles.

Pour récupérer l’état d’alimentation de toutes les machines virtuelles de votre abonnement, utilisez l’[API Machines virtuelles - Répertorier tout](/rest/api/compute/virtualmachines/listall) avec le paramètre **statusOnly** défini sur *true*.

> [!NOTE]
> [Machines virtuelles - Lister toutes les API](/rest/api/compute/virtualmachines/listall) avec le paramètre **statusOnly** défini sur true permet de récupérer l’état d’alimentation de toutes les machines virtuelles de votre abonnement. Toutefois, dans de rares cas, l’état d’alimentation peut ne pas être disponible en raison de problèmes intermittents dans le processus de récupération. Dans de telles situations, nous vous recommandons d’effectuer une nouvelle tentative avec la même API, ou en utilisant [Azure Resource Health](../service-health/resource-health-overview.md) ou [Azure Resource Graph](..//governance/resource-graph/overview.md) pour vérifier l’état d’alimentation de vos machines virtuelles.
 
## <a name="power-states-and-billing"></a>États d’alimentation et facturation

L’état d’alimentation représente le dernier état connu de la machine virtuelle.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Image montrant un diagramme des états d’alimentation par lesquels une machine virtuelle peut passer.":::

Le tableau suivant contient une description de l’état de chaque instance et indique si elle est facturée pour utilisation ou non.

| État d’alimentation | Description | Facturation |  
|---|---|---|
| Démarrage en cours| La machine virtuelle est en cours de mise sous tension. |Pas de facturation* | 
| Exécution en cours | La machine virtuelle est entièrement opérationnelle. Il s’agit de l’état de fonctionnement standard. | Facturation | 
| En cours d’arrêt | Il s’agit d’un état transitoire entre l’exécution et l’arrêt. | Facturation| 
|Arrêté | La machine virtuelle a été arrêtée à partir du système d’exploitation invité ou à l’aide des API PowerOff. Dans cet état, la machine virtuelle loue toujours le matériel sous-jacent. Cet état est également appelé *Arrêté (alloué)* . | Facturation | 
| Libération | Il s’agit de l’état transitoire entre l’exécution et la désallocation. | Pas de facturation* | 
| Libéré | La machine virtuelle a libéré le bail sur le matériel sous-jacent et est entièrement mise hors tension. Cet état est également appelé *Arrêté (désalloué)* . | Pas de facturation* | 

&#42; Certaines ressources Azure, par exemple les [Disques](https://azure.microsoft.com/pricing/details/managed-disks) et le [Réseau](https://azure.microsoft.com/pricing/details/bandwidth/), continueront à impliquer des frais.


## <a name="provisioning-states"></a>États d’approvisionnement

Un état d’approvisionnement est l’état d’une opération de type plan de contrôle initiée par l’utilisateur sur la machine virtuelle. Ces états sont distincts de l’état d’alimentation d’une machine virtuelle.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Image montrant les états de provisionnement par lesquels une machine virtuelle peut passer.":::

| État de provisionnement | Description | État d’alimentation | Facturation | 
|---|---|---|---|
| Créer | Création d’une machine virtuelle. | Démarrage en cours | Pas de facturation* | 
| Update | Met à jour le modèle d’une machine virtuelle existante. Certains changements apportés à une machine virtuelle et ne concernant pas un modèle, comme le démarrage et le redémarrage, relèvent de l’état de mise à jour. | Exécution en cours | Facturation | 
| Supprimer | Suppression d’une machine virtuelle. | Libération | Pas de facturation* |
| Libérer | La machine virtuelle est complètement arrêtée et supprimée de l’hôte sous-jacent. La désallocation d’une machine virtuelle est considérée comme une mise à jour et affiche les états de provisionnement semblables à la mise à jour. | Libération | Pas de facturation* | 

&#42; Certaines ressources Azure, par exemple les [Disques](https://azure.microsoft.com/pricing/details/managed-disks) et le [Réseau](https://azure.microsoft.com/pricing/details/bandwidth/), continueront à impliquer des frais.

## <a name="os-provisioning-states"></a>États de provisionnement d’un système d’exploitation
Les états de provisionnement d’un système d’exploitation s’appliquent uniquement aux machines virtuelles créées avec une image de système d’exploitation. Les images spécialisées n’affichent pas ces états. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Image montrant les états de provisionnement d’un système d’exploitation par lesquels une machine virtuelle peut passer.":::

| États de provisionnement d’un système d’exploitation | Description | État d’alimentation | Facturation | 
|---|---|---|---|
| OSProvisioningInProgress | La machine virtuelle est en cours d’exécution et l’installation du système d’exploitation invité est en cours. | Exécution en cours | Facturation | 
| OSProvisioningComplete | Il s’agit d’un état à courte durée de vie. La machine virtuelle passe rapidement de cet état à l’état **Réussite**. Si les extensions sont toujours en cours d’installation, vous continuez à voir cet état jusqu’à ce que l’opération soit terminée. | Exécution en cours | Facturation | 
| Opération réussie | Les actions lancées par l’utilisateur se sont terminées. | Exécution en cours | Facturation | 
| Échec | Représente une opération ayant échoué. Pour obtenir plus d’informations et connaître les solutions possibles, reportez-vous au code d’erreur. | Exécution en cours  | Facturation | 


## <a name="next-steps"></a>Étapes suivantes
- Consultez la [Documentation Cost Management and Billing](https://docs.microsoft.com/azure/cost-management-billing/).
- Utilisez la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour planifier vos déploiements.
- Découvrez-en plus sur la supervision de votre machine virtuelle en consultant [Supervision des machines virtuelles dans Azure](../azure-monitor/insights/monitor-vm-azure.md).