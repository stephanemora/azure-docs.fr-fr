---
title: Activer Azure Automation Update Management à partir d’un compte Automation
description: Cet article explique comment activer Update Management à partir d’un compte Automation.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f739134cd066f4dcc7fdf3da16c6db99a54d6265
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204937"
---
# <a name="enable-update-management-from-an-automation-account"></a>Activer Update Management à partir d’un compte Automation

Cet article explique comment utiliser votre compte Automation pour activer la fonctionnalité [Update Management](automation-update-management.md) pour les machines virtuelles de votre environnement. Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle existante à l’aide d’Update Management. 

> [!NOTE]
> Lors de l’activation d’Update Management, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](automation-offering-get-started.md) pour gérer les machines.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure sur https://portal.azure.com.

## <a name="enable-update-management"></a>Activer Update Management

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer Update Management. Le configuration prend jusqu’à 15 minutes.

    ![Activer Update Management](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Activer les machines virtuelles Azure

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Cliquez sur **+ Ajouter des machines virtuelles Azure** et sélectionnez une ou plusieurs machines virtuelles dans la liste. Les machines virtuelles qui ne peuvent pas être activées sont grisées et ne peuvent pas être sélectionnées. Les machines virtuelles Azure peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation. 

3. Cliquez sur **Activer** pour ajouter les machines virtuelles sélectionnées à la recherche enregistrée de groupe d’ordinateurs pour la fonctionnalité.

    ![Activer les machines virtuelles Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Activer les machines virtuelles non-Azure

Les machines qui ne sont pas dans Azure doivent être ajoutées manuellement. 

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Cliquez sur **Ajouter une machine virtuelle non-Azure**. Une nouvelle fenêtre de navigateur s’ouvre et présente des [instructions d’installation et de configuration de l’agent Log Analytics pour Windows](../azure-monitor/platform/log-analytics-agent.md) afin de permettre à la machine de commencer à envoyer des informations à Update Management. Si vous activez une machine actuellement gérée par Operations Manager, l’installation d’un nouvel agent n’est pas nécessaire et les informations de l’espace de travail sont entrées dans l’agent existant.

## <a name="enable-machines-in-the-workspace"></a>Activer des machines dans l’espace de travail

Les machines installées manuellement ou celles déjà connectées à votre espace de travail doivent être ajoutées à Azure Automation pour qu’Update Management soit activée. 

1. Dans votre compte Automation, sélectionnez **Update Management** sous **Gestion des mises à jour**.

2. Sélectionnez **Gérer des machines**. Le bouton **Gérer des machines** peut être grisé si vous avez précédemment choisi l’option **Activer sur toutes les machines disponibles et futures**.

    ![Recherches enregistrées](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Pour activer Update Management sur toutes les machines disponibles, sélectionnez **Activer sur toutes les machines disponibles** sur la page Gérer des machines. La commande permettant d’ajouter des machines individuellement est alors désactivée. Cette tâche ajoute tous les noms des machines envoyant des informations à l’espace de travail dans la requête de recherche enregistrée de groupe d’ordinateurs. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé.

5. Pour activer la fonctionnalité sur toutes les machines disponibles et les futures machines, sélectionnez **Activer sur toutes les machines disponibles et futures**. Cette option supprime les recherches enregistrées et les configurations d’étendue de l’espace de travail et ouvre la fonctionnalité pour toutes les machines Azure et non-Azure qui envoient des informations à l’espace de travail. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé définitivement, car il n’existe plus aucune configuration d’étendue.

6. Le cas échéant, vous pouvez rajouter les configurations d’étendue en ajoutant à nouveau les recherches enregistrées initiales. Pour plus d’informations, consultez [Limiter l’étendue du déploiement d’Update Management](automation-scope-configurations-update-management.md).

7. Pour activer la fonctionnalité sur une ou plusieurs machines, sélectionnez **Activer sur les machines sélectionnées**, puis cliquez sur **Ajouter** à côté de chaque machine à activer pour la fonctionnalité. Cette tâche ajoute le nom des machines sélectionnées à la requête de recherche enregistrée de groupe d’ordinateurs pour la fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](troubleshoot/update-management.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Windows, consultez [Résoudre les problèmes de l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Linux, consultez [Résoudre les problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).
