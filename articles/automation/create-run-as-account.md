---
title: Créer un compte d'identification Azure Automation
description: Cet article décrit comment créer un compte d’identification avec PowerShell ou à partir du portail Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: d6c8e96c325cfa6422ceb4efc55ef0c5cc149ae3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056771"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Créer un compte d’identification Azure Automation

Les comptes d’identification d’Azure Automation assurent l’authentification pour la gestion des ressources sur le modèle de déploiement Azure Resource Manager ou Azure Classic à l’aide de runbooks Automation et d’autres fonctionnalités Automation. Cet article explique comment créer un compte d’identification standard ou Classic à partir du portail Azure ou d’Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Créer un compte dans le portail Azure

Effectuez les étapes suivantes pour mettre à jour votre compte Azure Automation dans le portail Azure. Les comptes d’identification standard ou Classic sont créés séparément. Si vous n’avez pas besoin de gérer des ressources classiques, vous pouvez simplement créer le compte d’identification Azure.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

2. Recherchez et sélectionnez **Comptes Automation**.

3. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.

4. Dans le volet gauche, sélectionnez **Comptes d’identification** dans la section **Paramètres de compte**.

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Sélection de l’option Compte d’identification":::

5. Selon le compte dont vous avez besoin, choisissez le volet **+ Compte d’identification Azure** ou le volet **+ Compte d’identification Azure Classic**. Après avoir passé en revue les informations générales, cliquez sur **Créer**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Sélection de l’option permettant de créer un compte d’identification":::

6. Pour suivre la progression de la création du compte d’identification, accédez à l’onglet **Notifications** du menu. Une bannière s’affiche également indiquant que le compte est en cours de création. L’exécution de ce processus peut prendre plusieurs minutes.

## <a name="create-account-using-powershell"></a>Créer un compte à l’aide de PowerShell

La liste suivante indique la configuration nécessaire pour créer un compte d’identification dans PowerShell à l’aide d’un script fourni. Ces conditions requises s’appliquent aux deux types de comptes d’identification.

* Windows 10 ou Windows Server 2016 avec les modules Azure Resource Manager 3.4.1 et ultérieur. Le script PowerShell ne prend pas en charge les versions antérieures de Windows.
* Azure PowerShell 6.2.4 ou version ultérieure. Pour plus d’informations, consultez [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/install-az-ps).
* Un compte Automation, qui est référencé comme valeur pour les paramètres `AutomationAccountName` et `ApplicationDisplayName`.
* Les autorisations équivalentes à celles qui sont listées dans [Autorisations nécessaires pour configurer des comptes d’identification](automation-security-overview.md#permissions).

Pour obtenir les valeurs de `AutomationAccountName`, `SubscriptionId` et `ResourceGroupName`, qui sont des paramètres obligatoires pour le script PowerShell, procédez comme suit.

1. Connectez-vous au portail Azure.

1. Recherchez et sélectionnez **Comptes Automation**.

1. Dans la page Comptes Automation, sélectionnez votre compte Automation dans la liste.

1. Dans le volet gauche, sélectionnez **Propriétés**.

1. Notez les valeurs **Nom**, **ID d’abonnement** et **Groupe de ressources** sur la page **Propriétés**.

   ![Page des propriétés du compte Automation](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script PowerShell pour créer un compte d’identification

Le script PowerShell comprend la prise en charge de plusieurs configurations.

* Créez un compte d’identification à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et/ou un compte d’identification Classic à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et/ou un compte d’identification Classic à l’aide d’un certificat émis par votre autorité de certification d’entreprise.
* Créez un compte d’identification standard et/ou un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government.

1. Téléchargez et enregistrez le script dans un dossier local à l’aide de la commande suivante.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Lancez PowerShell avec des droits d’utilisateur élevés et accédez au dossier contenant le script.

3. Exécutez l’une des commandes suivantes pour créer un compte d’identification ou un compte d’identification Classic en fonction de vos besoins.

    * Créez un compte d’identification à l’aide d’un certificat auto-signé.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat d’entreprise.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Si vous avez créé un compte d’identification Classic avec un certificat public d’entreprise (fichier .cer), utilisez ce certificat. Le script le crée et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil utilisateur `%USERPROFILE%\AppData\Local\Temp` que vous avez utilisé pour exécuter la session PowerShell. Consultez [Chargement d’un certificat d’API de gestion dans le portail Azure](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Connectez-vous avec un compte membre du rôle Administrateurs des abonnements. Si vous créez un compte d’identification Classic, votre compte doit être coadministrateur de l’abonnement.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création graphique, consultez [Créer des runbooks graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
* Pour commencer à utiliser des runbooks PowerShell, consultez [Tutoriel : Créer un runbook PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Pour commencer à utiliser un runbook Python 3, consultez le [Tutoriel : Créer un runbook Python 3](learn/automation-tutorial-runbook-textual-python-3.md).