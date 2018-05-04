---
title: Ajouter des métriques étendues pour les machines virtuelles Azure | Microsoft Docs
description: Cet article vous aide à activer et à configurer les métriques de diagnostics étendues pour vos machines virtuelles Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4b00baba44a4724ce8f6a45a80692f7f566a35ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Ajouter des métriques étendues pour les machines virtuelles Azure

Cost Management utilise les données de métriques Azure issues de vos machines virtuelles Azure pour vous montrer des informations détaillées sur leurs ressources. Les données de métriques, également appelées compteurs de performances, permettent à Cost Management de générer des rapports. Toutefois, Cost Management ne collecte pas automatiquement toutes les données de métriques Azure à partir des machines virtuelles invitées ; vous devez activer la collecte de métriques. Cet article vous aide à activer et à configurer des métriques de diagnostics supplémentaires pour vos machines virtuelles Azure.

Une fois que vous avez activé la collecte de métriques, vous pouvez :

- Savoir quand vos machines virtuelles atteignent leurs limites en termes de mémoire, disque et UC.
- Détecter les anomalies et les tendances d’utilisation.
- Contrôler les coûts en effectuant un redimensionnement en fonction de l’utilisation.
- Obtenir des recommandations d’optimisation du dimensionnement à moindre frais provenant de Cost Management.

Par exemple, vous pouvez souhaiter surveiller les pourcentages d’utilisation de l’UC et de la mémoire de vos machines virtuelles Azure. Les métriques des machines virtuelles Azure correspondent à _[Hôte] Pourcentage d’UC_ et _[Invité] Pourcentage de mémoire_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Vérifier que les métriques sont activées sur les machines virtuelles

1. Connectez-vous au portail Azure sur http://portal.azure.com.
2. Sous **Machines virtuelles**, sélectionnez une machine virtuelle, puis sous **Surveillance**, sélectionnez **Métriques**. Une liste de métriques disponibles s’affiche.
3. Sélectionnez certaines métriques ; un graphe affiche alors les données correspondantes.  
    ![Exemple de métrique : pourcentage d’UC (hôte)](./media/azure-vm-extended-metrics/metric01.png)

Dans l’exemple précédent, un ensemble limité de métriques standard est disponible pour vos hôtes, mais aucune métrique de mémoire n’est proposée. Les métriques de mémoire font partie des métriques étendues. Vous devez effectuer quelques étapes supplémentaires pour activer les métriques étendues. Les informations suivantes vous expliquent comment les activer.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Activer les métriques étendues dans le portail Azure

Les métriques standard sont des métriques d’hôte. La métrique _[Hôte] Pourcentage d’UC_ en est un exemple. Il existe également des métriques de base pour les machines virtuelles invitées ; elles sont également appelées « métriques étendues ». Parmi les métriques étendues, citons _[Invité] Pourcentage de mémoire_ et _[Invité] Mémoire disponible_.

L’activation des métriques étendues est simple. Pour chaque machine virtuelle, activez la surveillance d’invités. Quand vous activez la surveillance d’invités, l’agent de diagnostics Azure est installé sur la machine virtuelle. Le processus suivant est le même pour les machines virtuelles classiques et normales et le même pour les machines virtuelles Windows et Linux.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Activer la surveillance d’invités sur les machines virtuelles existantes

1. Dans **Machines virtuelles**, affichez la liste de vos machines virtuelles, puis sélectionnez une machine virtuelle.
2. Sous **Surveillance**, sélectionnez **Métriques**.
3. Cliquez sur **Paramètres de diagnostic**.
4. Dans la page Paramètres de diagnostic, cliquez sur **Activer la surveillance d’invités**. Les machines virtuelles Linux nécessitent un compte de stockage existant. Si vous ne choisissez pas un compte de stockage pour une machine virtuelle Windows, un compte de stockage est créé pour vous.  
    ![Activer la surveillance d’invités](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Après quelques minutes, l’agent de diagnostics Azure est installé sur la machine virtuelle. Actualisez la page afin de mettre à jour la liste des métriques disponibles avec les métriques d’invité.  
    ![Métriques étendues](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Activer la surveillance d’invités sur des machines virtuelles nouvelles

Quand vous créez des machines virtuelles, vérifiez que vous sélectionnez **Diagnostics du système d’exploitation invité**. Les machines virtuelles Linux nécessitent un compte de stockage existant. Si vous ne choisissez pas un compte de stockage pour une machine virtuelle Windows, un compte de stockage est créé pour vous.

![Activer les diagnostics du système d’exploitation invité](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Informations d’identification Resource Manager

Après avoir activé les métriques étendues, vérifiez que Cost Management a accès à vos [informations d’identification Resource Manager](activate-subs-accounts.md). Cost Management a besoin de vos informations d’identification pour collecter et afficher les données de performances de vos machines virtuelles. Elles lui permettent également de créer des recommandations d’optimisation des coûts. Cost Management a besoin d’au moins trois jours de données de performances d’une instance pour déterminer si une diminution des coûts peut être recommandée.

## <a name="enable-vm-metrics-with-a-script"></a>Activer les métriques de machine virtuelle avec un script

Vous pouvez activer les métriques de machine virtuelle avec des scripts Azure PowerShell. Si le nombre de machines virtuelles sur lesquelles vous souhaitez activer les métriques est élevé, vous pouvez utiliser un script pour automatiser le processus. Vous trouverez des exemples de scripts sur GitHub à la page [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics) (Azure - Activer les diagnostics).

## <a name="view-azure-performance-metrics"></a>Afficher les métriques de performances Azure

Pour afficher les métriques de performances sur vos instances Azure dans le portail Cloudyn, accédez à **Ressources** > **Compute** > **Explorateur d’instances**. Dans la liste des instances de machine virtuelle, développez une instance, puis développez une ressource pour afficher les détails.

![Explorateur d’instances](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Étapes suivantes

- Si vous n’avez pas déjà activé l’accès de l’API Azure Resource Manager pour vos comptes, consultez [Activer des abonnements et comptes Azure](activate-subs-accounts.md).
