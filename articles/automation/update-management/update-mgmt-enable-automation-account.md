---
title: Activer Azure Automation Update Management à partir d’un compte Automation
description: Cet article explique comment activer Update Management à partir d’un compte Automation.
services: automation
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 787338be06c2e30aabb6421a42e7cb3aaabf8a2a
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669507"
---
# <a name="enable-update-management-from-an-automation-account"></a>Activer Update Management à partir d’un compte Automation

Cet article explique comment utiliser votre compte Automation pour activer la fonctionnalité [Update Management](update-mgmt-overview.md) pour les machines virtuelles de votre environnement, notamment les machines et serveurs inscrits auprès de [serveurs avec Azure Arc](../../azure-arc/servers/overview.md) (préversion). Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle Azure existante en utilisant Update Management.

> [!NOTE]
> Lors de l’activation d’Update Management, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](../index.yml) pour gérer les machines.
* Une [machine virtuelle Azure](../../virtual-machines/windows/quick-create-portal.md), ou une machine virtuelle ou un serveur inscrit avec des serveurs dotés d’Azure Arc (préversion). Les machines virtuelles ou les serveurs non Azure doivent disposer de l’[agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pour Windows ou Linux et de l’envoi de rapports à l’espace de travail lié au compte Automation sur lequel Update Management est activé. L’agent peut être installé sur des serveurs avec Azure Arc en déployant l’[extension de machine virtuelle Azure Log Analytics](../../azure-arc/servers/manage-vm-extensions.md) avec Azure Arc.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Activer Update Management

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Choisissez l’espace de travail Log Analytics et un compte Automation, puis sélectionnez **Activer** pour activer Update Management. Le configuration prend jusqu’à 15 minutes.

    ![Activer Update Management](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Activer les machines virtuelles Azure

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Sélectionnez **+ Ajouter des machines virtuelles Azure** et sélectionnez une ou plusieurs machines virtuelles dans la liste. Les machines virtuelles qui ne peuvent pas être activées sont grisées et ne peuvent pas être sélectionnées. Les machines virtuelles Azure peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation.

3. Sélectionnez **Activer** pour ajouter les machines virtuelles sélectionnées à la recherche enregistrée de groupe d’ordinateurs pour la fonctionnalité.

    ![Activer les machines virtuelles Azure](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Activer les machines virtuelles non-Azure

Les machines qui ne sont pas dans Azure doivent être ajoutées manuellement.

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Sélectionnez **Ajouter une machine virtuelle non-Azure**. Une nouvelle fenêtre de navigateur s’ouvre et présente des [instructions d'installation et de configuration de l'agent Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md) afin de permettre à la machine de commencer à envoyer des informations à Update Management. Si vous activez une machine actuellement gérée par Operations Manager, l’installation d’un nouvel agent n’est pas nécessaire. Les informations de l’espace de travail sont entrées dans l’agent existant.

## <a name="enable-machines-in-the-workspace"></a>Activer des machines dans l’espace de travail

Les machines installées manuellement ou celles déjà connectées à votre espace de travail doivent être ajoutées à Azure Automation pour qu’Update Management soit activée.

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Sélectionnez **Gérer des machines**. Le bouton **Gérer des machines** peut être grisé si vous avez précédemment choisi l’option **Activer sur toutes les machines disponibles et futures**.

    ![Recherches enregistrées](media/update-mgmt-enable-automation-account/managemachines.png)

3. Pour activer Update Management sur toutes les machines disponibles associées à l’espace de travail, sélectionnez **Activer sur toutes les machines disponibles** sur la page Gérer des machines. La commande permettant d’ajouter des machines individuellement est alors désactivée. Cette tâche ajoute tous les noms des machines envoyant des informations à l’espace de travail dans la requête de recherche enregistrée de groupe d’ordinateurs `MicrosoftDefaultComputerGroup`. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé.

4. Pour activer la fonctionnalité sur toutes les machines disponibles et les futures machines, sélectionnez **Activer sur toutes les machines disponibles et futures**. Cette option supprime la recherche enregistrée et la configuration d’étendue de l’espace de travail et permet à la fonctionnalité d’inclure toutes les machines Azure et non Azure qui, actuellement ou à l’avenir, envoient leurs rapports à l’espace de travail. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé définitivement, car plus aucune configuration d’étendue n’est disponible.

    > [!NOTE]
    > Comme cette option supprime les recherches enregistrées et les configurations d’étendue dans Log Analytics, il est important de supprimer tous les verrous de suppression sur l’espace de travail Log Analytics avant de sélectionner cette option. Si vous ne le faites pas, l’option ne parviendra pas à supprimer les configurations et vous devrez les supprimer manuellement.

5. Le cas échéant, vous pouvez rajouter les configurations d’étendue en ajoutant à nouveau la requête de recherche enregistrée initiale. Pour plus d’informations, consultez [Limiter l’étendue du déploiement d’Update Management](update-mgmt-scope-configuration.md).

6. Pour activer la fonctionnalité sur une ou plusieurs machines, sélectionnez **Activer sur les machines sélectionnées**, puis sélectionnez **Ajouter** en regard de chaque machine. Cette tâche ajoute le nom des machines sélectionnées à la requête de recherche enregistrée de groupe d’ordinateurs pour la fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles](update-mgmt-manage-updates-for-vm.md).

* Lorsque vous n’avez plus besoin de gérer de machines virtuelles ou de serveurs avec Update Management, consultez [Supprimer des machines virtuelles d’Update Management](update-mgmt-remove-vms.md).

* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](../troubleshoot/update-management.md).
