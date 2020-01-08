---
title: Composer des configurations DSC dans Azure Automation State Configuration (DSC) au moyen de ressources composites
description: Découvrez comment composer des configurations à l’aide de ressources composites dans Azure Automation State Configuration (DSC).
keywords: powershell dsc, desired state configuration, configuration d’état souhaité, powershell dsc azure, ressources composites
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 2c6d9aa13c8c320dc46f6634f3a60e0c1730c710
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418731"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Composer des configurations DSC dans Azure Automation State Configuration (DSC) au moyen de ressources composites

Lorsqu’une ressource doit être gérée avec plusieurs configurations DSC, la meilleure solution consiste à utiliser des [ressources composites](/powershell/scripting/dsc/resources/authoringresourcecomposite). Une ressource composite est une configuration de paramètres imbriqués utilisée comme ressource DSC au sein d’une autre configuration. Cela permet la création de configurations complexes tout en autorisant la création de ressources composites sous-jacentes (configurations paramétrables) et leur gestion à titre individuel.

Azure Automation permet [d’importer et de compiler des ressources composites](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
Une fois les ressources composites importées dans votre compte Automation, vous pouvez utiliser l’expérience **Composer la configuration** sur la page **State Configuration (DSC)** .

## <a name="composing-a-configuration-from-composite-resources"></a>Composer une configuration à partir de ressources composites

Avant de pouvoir attribuer une configuration effectuée à partir de ressources composites dans le portail Microsoft Azure, vous devez la composer. Vous intervenez en utilisant **Composer la configuration** sur la page **State Configuration (DSC)** , à partir des onglets **Configurations** ou **Configurations compilées**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Sur la page **Compte Automation**, sélectionnez **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Sur la page **State configuration (DSC)** , cliquez sur l’onglet **Configurations** ou **Configurations compilées**, puis sur **Composer la configuration** dans le menu en haut de la page.
1. Au moment de l’étape relative aux **fonctions de base**, indiquez le nom de la nouvelle configuration (obligatoire), cliquez n’importe où sur la ligne de chaque ressource composite que vous souhaitez inclure dans la nouvelle configuration, puis cliquez sur **Suivant** ou sur l’étape **Code source**. Pour les étapes suivantes, nous avons sélectionné les ressources composites **PSExecutionPolicy** et **RenameAndDomainJoin**.
   ![Capture d’écran de l’étape des fonctions de base sur la page Composer la configuration](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. L’étape **Code source** montre à quoi ressemble la configuration composée des ressources composites sélectionnées. Vous pouvez voir la fusion de tous les paramètres et la façon dont ils sont transmis à la ressource composite. Après avoir examiné le nouveau code source, cliquez sur **Suivant** ou sur l’étape **Paramètres**.
   ![Capture d’écran de l’étape du code source sur la page Composer la configuration](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Au niveau de l’étape **Paramètres**, le paramètre des ressources composites est exposé afin qu’elles puissent être fournies. Si un paramètre possède une description, celle-ci s’affiche à côté du champ de paramètre. Si un champ est un paramètre de type **PSCredential**, la liste déroulante servant à la configuration fournit une liste d’objets **Informations d’identification** dans le compte Automation en cours. Une option **+ Add a credential** (+ Ajouter une information d’identification) est également disponible. Une fois que tous les paramètres requis ont été fournis, cliquez sur **Save and compile** (Enregistrer et compiler).
   ![Capture d’écran de l’étape des paramètres sur la page Composer la configuration](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Une fois la nouvelle configuration enregistrée, elle est envoyée en compilation. Il est possible d’afficher l’état de la tâche de compilation comme celui de n’importe quelle autre configuration importée. Pour plus d’informations, voir [Affichage d’une tâche de compilation](automation-dsc-getting-started.md#viewing-a-compilation-job).

Si la compilation aboutit, la nouvelle configuration s’affiche dans l’onglet **Configurations compilées**. Une fois qu’elle apparaît dans cet onglet, elle peut être affectée à un nœud géré à l’aide de la procédure décrite dans [Réaffectation d’un nœud à une configuration de nœud différente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment intégrer des nœuds, consultez [Intégration de machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md)
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
