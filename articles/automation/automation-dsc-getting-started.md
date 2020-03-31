---
title: Prise en main d’Azure Automation State Configuration
description: Explication et exemples de tâches les plus courantes dans Azure Automation Stae Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fce9a769dd6d88c9926913d22716666284938c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74850956"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Prise en main d’Azure Automation State Configuration

Cette rubrique explique comment effectuer les tâches les plus courantes avec Azure Automation State Configuration, comme la création, l’importation et la compilation de configurations, l’intégration des ordinateurs à gérer et l’affichage des rapports. Pour obtenir une vue d’ensemble d’Azure Automation State Configuration, consultez [Vue d’ensemble d’Azure Automation State Configuration](automation-dsc-overview.md). Pour accéder à une documentation de Desired State Configuration (DSC), consultez l’article [Vue d’ensemble de la fonctionnalité Desired State Configuration de Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Cet article fournit des instructions détaillées sur l’utilisation d’Azure Automation State Configuration. Si vous souhaitez obtenir un exemple d’environnement préconfiguré sans suivre les étapes décrites dans cet article, vous pouvez utiliser le modèle Resource Manager suivant : [Azure Automation Managed Node](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ce modèle configure un environnement Azure Automation State Configuration complet, comprenant une machine virtuelle Azure gérée par Azure Automation State Configuration.

## <a name="prerequisites"></a>Prérequis

Pour exécuter les exemples de cet article, vous devez disposer des éléments suivants :

- Un compte Azure Automation. Pour obtenir des instructions sur la création d’un compte d’identification Azure Automation, consultez [Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md).
- Une machine virtuelle Azure Resource Manager (non classique) exécutant un [système d’exploitation pris en charge](automation-dsc-overview.md#operating-system-requirements). Pour obtenir des instructions sur la création d’une machine virtuelle, consultez [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Création d’une configuration DSC

Vous créez une [configuration DSC](/powershell/scripting/dsc/configurations/configurations) simple qui garantit la présence ou l’absence de la fonctionnalité Windows **Serveur web** (IIS), selon le mode d’attribution des nœuds.

1. Démarrez [VSCode](https://code.visualstudio.com/docs) (ou n’importe quel éditeur de texte).
1. Saisissez le texte suivant :

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Enregistrez le fichier sous le nom `TestConfig.ps1`.

Cette configuration appelle une ressource dans chaque bloc de nœuds, la [ressource WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource), qui garantit la présence ou l’absence de la fonctionnalité **Serveur web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importation d’une configuration dans Azure Automation

Ensuite, vous importez la configuration dans le compte Automation.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Sur la page **Compte Automation**, sélectionnez **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Sur la page **State Configuration (DSC)** , cliquez sur l’onglet **Configurations**, puis cliquez sur **+ Ajouter**.
1. Sur la page **Importer la configuration**, recherchez le fichier `TestConfig.ps1` sur votre ordinateur.

   ![Capture d’écran du panneau **Importer la configuration**](./media/automation-dsc-getting-started/AddConfig.png)

1. Cliquez sur **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Affichage d’une configuration dans Azure Automation

Après avoir importé une configuration, vous pouvez l’afficher dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Sur la page **Compte Automation**, sélectionnez **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Sur la page **State Configuration (DSC)** , cliquez sur l’onglet **Configurations**, puis sur **TestConfig** (il s’agit du nom de la configuration que vous avez importée dans la procédure précédente).
1. Sur la page **Configuration de TestConfig**, cliquez sur **Afficher la source de configuration**.

   ![Capture d’écran du panneau Configuration de TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Dans la page **Source de configuration de TestConfig** qui s’affiche, vous accédez au code PowerShell de la configuration.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilation d’une configuration dans Azure Automation

Avant de pouvoir appliquer un état souhaité à un nœud, vous devez compiler une configuration DSC définissant cet état dans une ou plusieurs configurations de nœuds (documents MOF) et la placer sur le serveur Pull Automation DSC. Pour obtenir une description plus détaillée de la compilation des configurations dans Azure Automation State Configuration, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md).
Pour plus d’informations sur la compilation des configurations, consultez [Configurations DSC](/powershell/scripting/dsc/configurations/configurations).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Sur la page **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Sur la page **State Configuration (DSC)** , cliquez sur l’onglet **Configurations**, puis sur **TestConfig** (le nom de la configuration que vous avez importée précédemment).
1. Sur la page **Configuration de TestConfig**, cliquez sur **Compiler**, puis sur **Oui**. Une tâche de compilation démarre.

   ![Capture d’écran de la page Configuration de TestConfig mettant en évidence le bouton de compilation](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Lorsque vous compilez une configuration dans Azure Automation, tous les fichiers MOF de configuration de nœuds créés sont automatiquement déployés sur le serveur Pull.

## <a name="viewing-a-compilation-job"></a>Affichage d’une tâche de compilation

Après avoir démarré une compilation, vous pouvez l’afficher sur la mosaïque **Travaux de compilation** de la page **Configuration**. La mosaïque **Tâches de compilation** affiche les tâches en cours d’exécution, terminées et en échec. Quand vous ouvrez la page d’une tâche de compilation, vous obtenez des informations sur cette tâche, notamment les erreurs ou les avertissements rencontrés, les paramètres d’entrée utilisés dans la configuration et les journaux d’activité de compilation.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Sur la page **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Sur la page **State Configuration (DSC)** , cliquez sur l’onglet **Configurations**, puis sur **TestConfig** (le nom de la configuration que vous avez importée précédemment).
1. Sous **Tâches de compilation**, sélectionnez la tâche de compilation que vous souhaitez afficher. Une page **Tâche de compilation** s’ouvre en indiquant la date de démarrage de la tâche de compilation.

   ![Capture d’écran de la page Tâche de compilation](./media/automation-dsc-getting-started/CompilationJob.png)

1. Cliquez sur n’importe quelle mosaïque de la page **Tâche de compilation** pour afficher des détails supplémentaires sur la tâche.

## <a name="viewing-node-configurations"></a>Affichage des configurations de nœuds

La réussite d’une tâche de compilation a pour effet de créer une ou plusieurs configurations de nœud. Une configuration de nœud est un document MOF déployé sur le serveur Pull et prêt à être extrait et appliqué par un ou plusieurs nœuds. Vous pouvez afficher les configurations de nœuds de votre compte Automation dans la page **State Configuration (DSC)** . Une configuration de nœud possède un nom au format *NomConfiguration*.*NomNœud*.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Dans le panneau **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Sur la page **State Configuration (DSC)** , cliquez sur l’onglet **Configurations compilées**.

   ![Capture d’écran de l’onglet Configurations compilées](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Intégration d’une machine virtuelle Azure pour la gérer avec Azure Automation State Configuration

Azure Automation State Configuration vous permet de gérer vos machines virtuelles Azure (par le biais des modèles de déploiement classique et Resource Manager), vos machines virtuelles locales, vos machines Linux, vos machines virtuelles AWS et vos ordinateurs physiques en local. Dans cet article, vous allez n’intégrer que des machines virtuelles Azure Resource Manager. Pour plus d’informations sur l’intégration d’autres types de machines, consultez [Intégration de machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Pour intégrer une machine virtuelle Azure Resource Manager pour la gérer via Azure Automation State Configuration, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Dans le panneau **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** et sous l’onglet **Nœuds**, cliquez sur **+ Ajouter**.

   ![Capture d’écran de la page Nœuds DSC mettant en évidence le bouton Ajouter des machines virtuelles Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Sur la page **Machines virtuelles**, sélectionnez votre machine virtuelle.
1. Sur la page **Machine virtuelle**, cliquez sur **+ Connecter**.

   > [!IMPORTANT]
   > Il doit s’agir d’une machine virtuelle Azure Resource Manager exécutant un [système d’exploitation pris en charge](automation-dsc-overview.md#operating-system-requirements).

2. Sur la page **Inscription**, sélectionnez le nom de la configuration de nœud que vous souhaitez appliquer à la machine virtuelle dans la zone **Nom de la configuration de nœuds**. Vous n’êtes pas obligé de fournir un nom à ce stade. Vous pouvez modifier la configuration de nœud assignée une fois l’intégration du nœud effectuée.
   Cochez la case **Redémarrer le nœud si nécessaire**, puis cliquez sur **OK**.

   ![Capture d’écran du panneau Inscription](./media/automation-dsc-getting-started/RegisterVM.png)

   La configuration de nœud spécifiée s’applique à la machine virtuelle selon les intervalles spécifiés par la **Fréquence du mode de configuration**. La machine virtuelle recherche les mises à jour de la configuration du nœud selon les intervalles spécifiés par la **Fréquence d’actualisation**. Pour plus d’informations sur la façon dont ces valeurs sont utilisées, consultez [Configuration du Gestionnaire de configuration local](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure lance le processus d’intégration de la machine virtuelle. Une fois ce processus terminé, la machine virtuelle s’affiche sous l’onglet **Nœuds** de la page **Configuration d’état (DSC)** dans le compte Automation.

## <a name="viewing-the-list-of-managed-nodes"></a>Affichage de la liste des nœuds gérés

Vous pouvez utiliser l’onglet **Nœuds** de la page **Configuration d’état (DSC)** pour afficher la liste de toutes les machines qui ont été intégrées afin d’être gérées dans votre compte Automation.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Dans le panneau **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** , cliquez sur l’onglet **Nœuds**.

## <a name="viewing-reports-for-managed-nodes"></a>Affichage des rapports de nœuds gérés

Chaque fois qu’Azure Automation State Configuration effectue une vérification de cohérence sur un nœud géré, le nœud envoie un rapport d’état sur le serveur Pull. Vous pouvez consulter ces rapports sur la page correspondant à ce nœud.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Dans le panneau **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** , cliquez sur l’onglet **Nœuds**. Ici, vous pouvez voir la vue d’ensemble de l’état de la configuration et les détails de chaque nœud.

   ![Capture d’écran de la page des nœuds](./media/automation-dsc-getting-started/NodesTab.png)

1. Sur l’onglet **Nœuds**, cliquez sur l’enregistrement de nœud pour ouvrir les rapports. Cliquez sur le rapport pour lequel vous souhaitez afficher des détails supplémentaires.

   ![Capture d’écran du panneau Rapport](./media/automation-dsc-getting-started/NodeReport.png)

Le panneau d’un rapport vous permet d’accéder aux informations d’état suivantes pour vous permettre d’effectuer la vérification de cohérence correspondante :

- L’état du rapport : si le nœud est « Conforme », si la configuration a « Échoué » ou si le nœud est « Non conforme » (lorsque le nœud est en mode **ApplyandMonitor** et que l’ordinateur ne se trouve pas à l’état souhaité).
- L’heure de début de la vérification de cohérence.
- Le temps total d’exécution de la vérification de cohérence.
- Le type de vérification de cohérence.
- Toute erreur rencontrée, avec code d’erreur et message d’erreur.
- Toutes les ressources DSC utilisées dans la configuration, ainsi que l’état de chaque ressource (si le nœud est à l’état souhaité pour cette ressource) : vous pouvez cliquer sur chaque ressource pour obtenir des informations plus détaillées sur cette ressource.
- Le nom, l’adresse IP et le mode de configuration du nœud.

Vous pouvez également cliquer sur **Afficher le rapport brut** pour afficher les données que le nœud envoie effectivement au serveur.
Pour plus d’informations sur l’utilisation de ces données, consultez la page [Utilisation d’un serveur de rapports DSC](/powershell/scripting/dsc/pull-server/reportserver).

Vous risquez de devoir attendre un certain temps après l’intégration d’un nœud pour pouvoir accéder au premier rapport. Vous devrez peut-être attendre jusqu’à 30 minutes pour obtenir le premier rapport après avoir effectué l’intégration.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Réaffectation d’un nœud à une configuration de nœud différent

Vous pouvez attribuer un nœud à une configuration de nœud différente de celle qui lui a été initialement affectée.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Dans le panneau **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** , cliquez sur l’onglet **Nœuds**.
1. Sous l’onglet **Nœuds**, cliquez sur le nom du nœud que vous souhaitez réaffecter.
1. Sur la page de ce nœud, cliquez sur **Attribuer une configuration de nœuds**.

    ![Capture d’écran de la page de détails des nœuds mettant en évidence le bouton Attribuer une configuration de nœuds](./media/automation-dsc-getting-started/AssignNode.png)

1. Sur la page **Affecter une configuration de nœuds**, sélectionnez la configuration de nœuds à laquelle vous souhaitez affecter le nœud, puis cliquez sur **OK**.

    ![Capture d’écran de la page Attribuer une configuration de nœuds](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Annulation de l’inscription d’un nœud

Si vous ne souhaitez plus qu’un nœud soit géré par Azure Automation DSC, vous pouvez annuler son inscription.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
1. Dans le panneau **Compte Automation**, cliquez sur **State Configuration (DSC)** sous **Gestion de la configuration**.
1. Dans la page **State Configuration (DSC)** , cliquez sur l’onglet **Nœuds**.
1. Sous l’onglet **Nœuds**, cliquez sur le nom du nœud dont vous voulez annuler l’inscription.
1. Sur la page de ce nœud, cliquez sur **Annuler l’inscription**.

    ![Capture d’écran de la page de détails des nœuds mettant en évidence le bouton Annuler l’inscription](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Articles connexes

- [Vue d’ensemble d’Azure Automation State Configuration](automation-dsc-overview.md)
- [Intégration des machines pour la gestion avec Azure Automation State Configuration](automation-dsc-onboarding.md)
- [Vue d’ensemble de la configuration d’état souhaité Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Applets de commande d’Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
