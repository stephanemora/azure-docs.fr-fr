---
title: Activer le suivi des modifications et l’inventaire d’Azure Automation à partir d’un compte Automation
description: Cet article explique comment activer le suivi des modifications et l’inventaire à partir d’un compte Automation
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 28c61e637a37c158dcd80c0f02b748b4813945fb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826840"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Activer Change Tracking et Inventory à partir d’un compte Automation

Cet article explique comment utiliser votre compte Automation pour activer la fonctionnalité [Suivi des modifications et inventaire](change-tracking.md) pour les machines virtuelles de votre environnement. Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle existante à l’aide de la fonctionnalité Suivi des modifications et inventaire. 

> [!NOTE]
> Lors de l’activation de la fonctionnalité Suivi des modifications et inventaire, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](automation-offering-get-started.md) pour gérer les machines.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure sur https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Activer Change Tracking et Inventory

1. Accédez à votre compte Automation et sélectionnez **Inventorier** ou **Suivi des modifications** sous **Gestion de la configuration**.

2. Choisissez l’espace de travail Log Analytics et un compte Automation, puis cliquez sur **Activer** pour activer la fonctionnalité Suivi des modifications et inventaire. La configuration prend jusqu’à 15 minutes.

    ![Activer Change Tracking et Inventory](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Vérifier la configuration de l’étendue

La fonctionnalité Suivi des modifications et inventaire utilise une configuration d’étendue dans l’espace de travail pour cibler les ordinateurs devant recevoir des modifications. La configuration d’étendue est un groupe d’une ou plusieurs recherches enregistrées qui est utilisé pour limiter l’étendue de la fonctionnalité à des ordinateurs spécifiques. Pour plus d’informations, consultez [Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire](automation-scope-configurations-change-tracking.md).

## <a name="enable-azure-vms"></a>Activer les machines virtuelles Azure

1. Dans votre compte Automation, sélectionnez **Inventaire** ou **Suivi des modifications** sous **Gestion de la configuration**.

2. Cliquez sur **+ Ajouter des machines virtuelles Azure** et sélectionnez une ou plusieurs machines virtuelles dans la liste. Les machines virtuelles qui ne peuvent pas être activées sont grisées et ne peuvent pas être sélectionnées. Les machines virtuelles Azure peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation. 

3. Cliquez sur **Activer** pour ajouter les machines virtuelles sélectionnées à la recherche enregistrée de groupe d’ordinateurs pour la fonctionnalité. Pour plus d’informations, consultez [Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire](automation-scope-configurations-change-tracking.md).

    ![Activer les machines virtuelles Azure](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Activer les machines virtuelles non-Azure

Les machines qui ne sont pas dans Azure doivent être ajoutées manuellement. 

1. Dans votre compte Automation, sélectionnez **Inventaire** ou **Suivi des modifications** sous **Gestion de la configuration**.

2. Cliquez sur **Ajouter une machine virtuelle non-Azure**. Une nouvelle fenêtre de navigateur s’ouvre et présente des [instructions d’installation et de configuration de l’agent Log Analytics pour Windows](../azure-monitor/platform/log-analytics-agent.md) afin de permettre à la machine de commencer à rapporter les opérations de la fonctionnalité Suivi des modifications et inventaire. Si vous activez une machine actuellement gérée par Operations Manager, l’installation d’un nouvel agent n’est pas nécessaire et les informations de l’espace de travail sont entrées dans l’agent existant.

## <a name="enable-machines-in-the-workspace"></a>Activer des machines dans l’espace de travail

Les machines installées manuellement ou les machines déjà connectées à votre espace de travail doivent être ajoutées à Azure Automation pour que la solution Suivi des modifications et inventaire soit activée. 

1. Dans votre compte Automation, sélectionnez **Inventaire** ou **Suivi des modifications** sous **Gestion de la configuration**.

2. Sélectionnez **Gérer des machines**. Le bouton **Gérer des machines** peut être grisé si vous avez précédemment choisi l’option **Activer sur toutes les machines disponibles et futures**.

    ![Recherches enregistrées](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Pour activer la fonctionnalité Suivi des modifications et inventaire sur toutes les machines disponibles, sélectionnez **Activer sur toutes les machines disponibles** sur la page Gérer des machines. La commande permettant d’ajouter des machines individuellement est alors désactivée. Cette tâche ajoute tous les noms des machines envoyant des informations à l’espace de travail dans la requête de recherche enregistrée de groupe d’ordinateurs. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé.

4. Pour activer la fonctionnalité sur toutes les machines disponibles et les futures machines, sélectionnez **Activer sur toutes les machines disponibles et futures**. Cette option supprime les recherches enregistrées et les configurations d’étendue de l’espace de travail et ouvre la fonctionnalité pour toutes les machines Azure et non-Azure qui envoient des informations à l’espace de travail. Quand cette option est sélectionnée, le bouton **Gérer des machines** est désactivé définitivement, car il n’existe plus aucune configuration d’étendue.

5. Le cas échéant, vous pouvez rajouter les configurations d’étendue en ajoutant à nouveau les recherches enregistrées initiales. Pour plus d’informations, consultez [Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire](automation-scope-configurations-change-tracking.md).

6. Pour activer la fonctionnalité sur une ou plusieurs machines, sélectionnez **Activer sur les machines sélectionnées**, puis cliquez sur **Ajouter** en regard de chaque machine à activer pour la fonctionnalité. Cette tâche ajoute le nom des machines sélectionnées à la requête de recherche enregistrée de groupe d’ordinateurs pour la fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la fonctionnalité, consultez [Gérer Suivi des modifications et inventaire](change-tracking-file-contents.md).
* Pour plus d’informations sur les configurations d’étendue, consultez [Utiliser des configurations d’étendue pour la fonctionnalité Suivi des modifications et inventaire](automation-scope-configurations-change-tracking.md).
* Pour savoir comment utiliser la fonctionnalité pour identifier les logiciels installés dans votre environnement, consultez [Détecter les logiciels installés sur vos machines virtuelles](automation-tutorial-installed-software.md).
* Si vous ne souhaitez pas intégrer votre compte Automation avec un espace de travail Log Analytics lors de l’activation de cette fonctionnalité, consultez [Dissocier un espace de travail d’un compte Automation](automation-unlink-workspace-change-tracking.md).
* Lorsque vous avez fini de déployer des modifications sur des machines virtuelles, vous pouvez les supprimer comme décrit dans [Supprimer des machines virtuelles de Suivi des modifications et inventaire](automation-remove-vms-from-change-tracking.md).
* Pour résoudre des problèmes généraux liés à la fonctionnalité, consultez [Résoudre les problèmes rencontrés avec Change Tracking et d’inventaire](troubleshoot/change-tracking.md).