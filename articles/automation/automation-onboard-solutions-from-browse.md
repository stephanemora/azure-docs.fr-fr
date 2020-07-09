---
title: Activer Azure Automation Update Management à partir du portail Azure
description: Cet article explique comment activer Update Management à partir du portail Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 0af0848f057b5c225471e9ac13f14db2468d838c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186093"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Activer Update Management à partir du portail Azure

Cet article explique comment activer la fonctionnalité [Update Management](automation-update-management.md) pour les machines virtuelles en parcourant le portail Azure. Pour activer des machines virtuelles Azure à grande échelle, vous devez activer une machine virtuelle existante à l’aide d’Update Management. 

Le nombre de groupes de ressources que vous pouvez utiliser pour la gestion de vos machines virtuelles est restreint par les [limites de déploiement Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). Les déploiements Resource Manager (à ne pas confondre avec les déploiements de mises à jour) sont limités à cinq groupes de ressources par déploiement. Deux de ces groupes de ressources sont réservés à la configuration de l’espace de travail Log Analytics, du compte Automation et des ressources associées. Vous disposez ainsi de trois groupes de ressources à sélectionner pour la gestion par Update Management. Cette limite s’applique uniquement à la configuration simultanée, et non au nombre de groupes de ressources qui peuvent être gérés par une fonctionnalité Automation.

> [!NOTE]
> Lors de l’activation d’Update Management, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de région pour un compte Automation et l’espace de travail Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Compte Automation](./index.yml) pour gérer les machines.
* Une [machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure sur https://portal.azure.com.

## <a name="enable-update-management"></a>Activer Update Management

1. Dans le portail Azure, accédez à **Machines virtuelles**.

2. Utilisez les cases à cocher pour choisir les machines virtuelles à ajouter à Update Management. Vous pouvez ajouter des machines pour trois différents groupes de ressources à la fois. Les machines virtuelles Azure peuvent exister dans n’importe quelle région, quel que soit l’emplacement de votre compte Automation.

    ![Liste des machines virtuelles](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > Utilisez les contrôles de filtre pour sélectionner des machines virtuelles provenant de différents abonnements, emplacements et groupes de ressources. Vous pouvez cliquer sur la case à cocher supérieure pour sélectionner toutes les machines virtuelles dans une liste.

    ![Activer Update Management](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Cliquez sur **Services** et sélectionnez **Update Management** pour la fonctionnalité Update Management. 

4. La liste des machines virtuelles est filtrée pour afficher uniquement celles qui se trouvent dans le même abonnement et le même emplacement. Si vos machines virtuelles se trouvent dans plus de trois groupes de ressources, les trois premiers groupes de ressources sont sélectionnés.

5. Un espace de travail Log Analytics et un compte Automation existants sont sélectionnés par défaut. Si vous souhaitez utiliser un espace de travail Log Analytics et un compte Automation différents, cliquez sur **PERSONNALISER** pour les sélectionner sur la page Configuration personnalisée. Lorsque vous choisissez un espace de travail Log Analytics, une vérification est effectuée pour déterminer si ce dernier est lié à un compte Automation. Si un compte Automation lié est trouvé, l’écran suivant s’affiche. Une fois que vous avez terminé, cliquez sur **OK**.

    ![Sélectionner un espace de travail et un compte](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Si l’espace de travail sélectionné n’est pas lié à un compte Automation, l’écran suivant s’affiche. Sélectionnez un compte Automation, puis cliquez sur **OK** lorsque vous avez terminé.

    ![Aucun espace de travail](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Désactivez la case à cocher en regard des machines virtuelles que vous ne souhaitez pas activer. Les machines virtuelles qui ne peuvent pas être activées sont déjà désélectionnées.

8. Cliquez sur **Activer** pour activer la fonctionnalité que vous avez sélectionnée. La configuration prend jusqu’à 15 minutes.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](troubleshoot/update-management.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Windows, consultez [Résoudre les problèmes de l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Linux, consultez [Résoudre les problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).
