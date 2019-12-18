---
title: Intégration des machines pour la gestion avec Azure Automation State Configuration
description: Comment configurer des machines pour la gestion avec Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 89b51af3beaad645dc27b599c2493be4d4bdf30f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951409"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Intégration des machines pour la gestion avec Azure Automation State Configuration

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Pourquoi gérer des machines avec Azure Automation State Configuration ?

Azure Automation State Configuration est un service de gestion de la configuration pour les nœuds DSC dans n’importe quel cloud ou centre de données local.
Il permet de faire évoluer des milliers d’ordinateurs rapidement et facilement à partir d’un emplacement central et sécurisé.
Vous pouvez facilement intégrer des machines, leur affecter des configurations déclaratives et afficher des rapports montrant la conformité de chaque machine à l’état souhaité que vous avez spécifié.
Le service Azure Automation State Configuration est au DSC ce que les runbooks Azure Automation sont aux scripts PowerShell.
En d'autres termes, de la même manière que Azure Automation vous permet de gérer des scripts PowerShell, il vous aide également à gérer des configurations DSC.
Pour en savoir plus sur les avantages de l’utilisation d’Azure Automation State Configuration, consultez [Vue d’ensemble d’Azure Automation State Configuration](automation-dsc-overview.md).

Azure Automation State Configuration peut servir à gérer une grande diversité de machines :

- Machines virtuelles Azure
- Machines virtuelles Azure (classiques)
- Instances EC2 d’Amazon Web Services (AWS)
- Machines physiques/virtuelles Windows locales ou dans un cloud autre qu’Azure/AWS
- Machines physiques/virtuelles Linux sur site, dans Azure, ou dans un cloud autre qu’Azure

En outre, si vous n’êtes pas prêt à gérer la configuration de la machine dans le cloud, Azure Automation State Configuration peut également servir de point de terminaison dédié uniquement à la génération de rapports.
Cela vous permet de définir des configurations (d’envois [push]) par l’intermédiaire de DSC et d’afficher les détails sur les rapports dans Azure Automation.

> [!NOTE]
> La gestion des machines virtuelles Azure avec State Configuration est fournie sans frais supplémentaires si l’extension DSC de la machine virtuelle installée est ultérieure à 2.70. Pour plus d’informations, reportez-vous à la [**page relative à la tarification d’Automation**](https://azure.microsoft.com/pricing/details/automation/).

Les sections suivantes décrivent la façon dont vous pouvez intégrer chaque type de machine à Azure Automation State Configuration.

## <a name="azure-virtual-machines"></a>Machines virtuelles Azure

Azure Automation State Configuration vous permet d’intégrer facilement des machines virtuelles Azure pour une gestion de configuration par le biais du portail Azure, des modèles Azure Resource Manager ou de PowerShell. En arrière-plan, et sans qu’aucun administrateur n’ait à contrôler la machine virtuelle à distance, l’extension Azure VM Desired State Configuration inscrit la machine virtuelle auprès d’Azure Automation State Configuration.
Étant donné que cette extension s’exécute de façon asynchrone, la section [**Résolution des problèmes liés à l’intégration de machines virtuelles Azure**](#troubleshooting-azure-virtual-machine-onboarding) suivante décrit la procédure à suivre pour contrôler sa progression ou résoudre les problèmes.

### <a name="azure-portal"></a>Portail Azure

Dans le [portail Azure](https://portal.azure.com/), accédez au compte Azure Automation où vous souhaitez intégrer des machines virtuelles. Dans la page Configuration d’état et sous l’onglet **Nœuds**, cliquez sur **+ Ajouter**.

Sélectionnez une machine virtuelle Azure à intégrer.

Si l’extension d’état PowerShell souhaitée n’est pas installée sur la machine virtuelle et que l’état d’alimentation est en cours d’exécution, cliquez sur **Se connecter**.

Sous **Inscription**, entrez les [valeurs du gestionnaire de configuration locale de PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) requises pour votre cas d’utilisation et, éventuellement, une configuration de nœud à attribuer à la machine virtuelle.

![intégration](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Les machines virtuelles Azure peuvent être déployées et intégrées à Azure Automation State Configuration par le biais de modèles Azure Resource Manager. Pour un exemple de modèle intégrant une machine virtuelle existante à Azure Automation State Configuration, consultez la section relative à la [gestion d’un serveur par le service Desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/).
Si vous gérez un groupe de machines virtuelles identiques, consultez l’exemple de modèle de la section relative à la [gestion de la configuration des groupes de machines virtuelles identiques par Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

Vous pouvez utiliser l’applet de commande [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) pour intégrer des machines virtuelles dans Azure à l’aide de PowerShell.
Toutefois, cela n’est actuellement implémenté que pour les machines exécutant Windows (l’applet de commande déclenche uniquement l’extension Windows).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Enregistrement de machines virtuelles dans plusieurs abonnements Azure

La meilleure façon d’enregistrer des machines virtuelles à partir d’autres abonnements Azure consiste à utiliser l’extension DSC dans un modèle de déploiement Azure Resource Manager.
Vous trouverez des exemples dans la section relative à [l’extension de Desired State Configuration avec des modèles Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Pour trouver la clé d’inscription et l’URL d’inscription à utiliser comme paramètres dans ce modèle, consultez la section [**Inscription sécurisée**](#secure-registration), ci-après.

## <a name="amazon-web-services-aws-virtual-machines"></a>Machines virtuelles Amazon Web Services (AWS)

Vous pouvez facilement intégrer des machines virtuelles Amazon Web Services pour la gestion de configuration par Azure Automation State Configuration à l’aide de la boîte à outils AWS DSC. Pour obtenir plus d’informations sur cette boîte à outils, cliquez [ici](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Machines physiques/virtuelles Windows locales ou dans un cloud autre qu’Azure/AWS

Les serveurs Windows en cours d’exécution en local ou dans d’autres environnements de cloud peuvent également être intégrés dans Azure Automation State Configuration, tant qu’ils ont [un accès sortant à Azure](automation-dsc-overview.md#network-planning) :

1. Vérifiez que la dernière version de [WMF 5](https://aka.ms/wmf5latest) est installée sur les machines que vous souhaitez intégrer à Azure Automation State Configuration.
1. Suivez les instructions de la section [**Génération de métaconfigurations DSC**](#generating-dsc-metaconfigurations) suivante pour générer un dossier contenant les métaconfigurations DSC nécessaires.
1. Appliquez à distance la métaconfiguration DSC PowerShell aux machines que vous voulez intégrer. **La machine à partir de laquelle cette commande est exécutée doit disposer de la dernière version de [WMF 5](https://aka.ms/wmf5latest)** :

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Si vous ne pouvez pas appliquer les métaconfigurations DSC PowerShell à distance, copiez le dossier des métaconfigurations de l’étape 2 sur chaque machine à intégrer. Appelez ensuite **Set-DscLocalConfigurationManager** localement sur chaque machine à intégrer.
1. À l’aide du portail Azure ou des applets de commande, vérifiez que les machines à intégrer s’affichent bien comme nœuds State Configuration inscrits dans votre compte Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Machines physiques/virtuelles Linux locales, dans Azure, ou dans un cloud autre qu’Azure

Les serveurs Linux en cours d’exécution en local ou dans d’autres environnements de cloud peuvent également être intégrés dans Azure Automation State Configuration, tant qu’ils ont [un accès sortant à Azure](automation-dsc-overview.md#network-planning) :

1. Vérifiez que la dernière version du service [Desired State Configuration de PowerShel pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) est installée sur les machines que vous souhaitez intégrer à Azure Automation State Configuration.
1. Si les [valeurs par défaut du gestionnaire de configuration locale DSC PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) correspondent à votre cas d’utilisation et que vous voulez intégrer des machines de sorte qu’elles procèdent **à la fois** à une extraction auprès d’Azure Automation State Configuration et qu’elles lui adressent des rapports :

   - Sur chaque machine Linux à intégrer à Azure Automation State Configuration, utilisez `Register.py` pour effectuer l’intégration en utilisant les valeurs par défaut du gestionnaire de configuration locale DSC PowerShell :

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Pour trouver la clé d’inscription et l’URL d’inscription de votre compte Automation, consultez la section [**Inscription sécurisée**](#secure-registration) suivante.

     Si les valeurs par défaut du gestionnaire de configuration locale DSC PowerShell **ne correspondent pas** à votre cas d’utilisation et que vous voulez intégrer des machines de sorte qu’elles n’envoient de rapports qu’à Azure Automation State Configuration, suivez les étapes 3 à 6. Sinon, passez directement à l’étape 6.

1. Suivez les instructions de la section [**Génération de métaconfigurations DSC**](#generating-dsc-metaconfigurations) suivante pour générer un dossier contenant les métaconfigurations DSC nécessaires.
1. Appliquez à distance la métaconfiguration PowerShell DSC pour les machines que vous souhaitez intégrer :

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

La machine à partir de laquelle cette commande est exécutée doit disposer de la dernière version de [WMF 5](https://aka.ms/wmf5latest) .

1. Si vous ne pouvez pas appliquer à distance les métaconfigurations PowerShell DSC, copiez la métaconfiguration correspondant à cet ordinateur à partir du dossier de l’étape 5 sur l’ordinateur Linux. Appelez ensuite `SetDscLocalConfigurationManager.py` localement sur chaque machine Linux à intégrer à Azure Automation State Configuration :

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. À l’aide du portail Azure ou des applets de commande, vérifiez que les machines à intégrer s’affichent bien en tant que nœuds DSC enregistrés dans votre compte Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Génération de métaconfigurations DSC

Pour intégrer de manière générique n’importe quelle machine à Azure Automation State Configuration, une [métaconfiguration DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) peut être générée, qui demande à l’agent DSC d’effectuer une extraction auprès d’Azure Automation State Configuration et/ou de lui adresser un rapport. Les métaconfigurations DSC pour Azure Automation State Configuration peuvent être générées à l’aide d’une configuration PowerShell DSC ou des applets de commande PowerShell Azure Automation.

> [!NOTE]
> Les métaconfigurations DSC contiennent les clés secrètes nécessaires à l’intégration d’une machine à un compte Automation à des fins de gestion. Veillez à protéger convenablement les métaconfigurations DSC que vous créez ou supprimez-les après utilisation.

### <a name="using-a-dsc-configuration"></a>Utilisation d’une configuration DSC

1. Ouvrez VSCode (ou votre éditeur favori) en tant qu’administrateur sur une machine de votre environnement local. Cette machine doit disposer de la dernière version de [WMF 5](https://aka.ms/wmf5latest) .
1. Copiez le script suivant localement. Ce script contient une configuration DSC PowerShell pour créer des métaconfigurations, ainsi qu’une commande pour lancer la création de métaconfigurations.

> [!NOTE]
> Les noms de configuration de nœud State Configuration respectent la casse dans le portail. S’ils ne correspondent pas, le nœud ne s’affiche pas sous l’onglet **Nœuds**.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Renseignez la clé d’inscription et l’URL de votre compte Automation, ainsi que les noms des machines à intégrer. Tous les autres paramètres sont facultatifs. Pour trouver la clé d’inscription et l’URL d’inscription de votre compte Automation, consultez la section [**Inscription sécurisée**](#secure-registration) suivante.
1. Si vous voulez que les machines adressent les informations d’état DSC à Azure Automation State Configuration sans toutefois extraire la configuration ou des modules PowerShell, affectez au paramètre **ReportOnly** la valeur true.
1. Exécutez le script. Vous devez à présent avoir un dossier appelé **DscMetaConfigs** dans votre répertoire de travail contenant les métaconfigurations DSC PowerShell pour les machines à intégrer (en tant qu’administrateur) :

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Utilisation des applets de commande Azure Automation

Si les valeurs par défaut du gestionnaire de configuration locale DSC PowerShell correspondent à votre cas d’utilisation et que vous voulez intégrer des machines de sorte qu’elles procèdent à la fois à une extraction auprès d’Azure Automation State Configuration et qu’elles lui adressent des rapports, les applets de commande Azure Automation constituent une méthode qui simplifie la génération des métaconfigurations nécessaires :

1. Ouvrez la console PowerShell ou VSCode en tant qu’administrateur sur une machine de votre environnement local.
2. Connectez-vous à Azure Resource Manager en utilisant `Connect-AzAccount`.
3. Téléchargez les métaconfigurations DSC PowerShell pour les machines à intégrer du compte Automation vers l’emplacement où vous voulez intégrer des nœuds :

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Vous devez à présent avoir un dossier appelé ***DscMetaConfigs***contenant les métaconfigurations DSC PowerShell pour les machines à intégrer (en tant qu’administrateur) :

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Enregistrement sécurisé

Les machines peuvent être intégrées en toute sécurité à un compte Azure Automation par le biais du protocole d’inscription WMF 5 DSC, qui permet à un nœud DSC de s’authentifier sur un serveur Pull ou Reporting PowerShell DSC (notamment Azure Automation State Configuration). Le nœud s’enregistre sur le serveur au niveau d’une **URL d’inscription** et s’authentifie à l’aide d’une **clé d’inscription**. Pendant l’enregistrement, le nœud DSC et le serveur Pull / Reporting DSC négocient un certificat unique pour ce nœud qui devra être utilisé pour l’authentification au serveur après l’enregistrement. Ce processus empêche les nœuds intégrés d’emprunter l’identité d’un autre nœud, par exemple si un nœud est compromis et agit à des fins malveillantes. Après l’enregistrement, la clé d’enregistrement n’est plus utilisée pour l’authentification et est supprimée du nœud.

Pour obtenir les informations requises pour le protocole d’inscription State Configuration, accédez à **Clés** sous **Paramètres du compte** dans le portail Azure. Ouvrez ce panneau en cliquant sur l’icône de clé dans le panneau **Bases** du compte Automation.

![Clés et URL d’Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- L’URL d’enregistrement correspond à la valeur du champ URL dans le panneau Gérer les clés.
- La clé d’enregistrement correspond à la clé d’accès primaire ou à la clé d’accès secondaire dans le panneau Gérer les clés. Vous pouvez utiliser l’une de ces deux clés.

Pour renforcer la sécurité, les clés d’accès primaire et secondaire d’un compte Automation peuvent être régénérées à tout moment (dans la page **Gérer les clés**) pour éviter les enregistrements ultérieurs de nœuds à l’aide de clés déjà utilisées.

## <a name="certificate-expiration-and-re-registration"></a>Expiration du certificat et nouvelle inscription

Après avoir inscrit une machine en tant que nœud DSC dans Azure Automation State Configuration, vous êtes susceptible de réinscrire le nœud à l’avenir  pour de multiples raisons :

- Pour les versions de Windows Server antérieures à Windows Server 2019, chaque nœud négocie automatiquement un certificat unique d’authentification qui expire après un an. À ce stade, le protocole d’inscription DSC PowerShell ne peut pas renouveler automatiquement les certificats lorsqu’ils sont sur le point d’expirer. Vous devez donc réinscrire les nœuds après un an. Avant la réinscription, assurez-vous que chaque nœud exécute Windows Management Framework 5.0 RTM. Si le certificat d’authentification d’un nœud expire et que ce dernier n’est pas réinscrit, le nœud ne peut pas communiquer avec Azure Automation et indique « Aucune réponse ». La réinscription effectuée dans un délai de 90 jours ou moins à partir de la date d’expiration du certificat, ou à tout moment après cette date, entraîne la génération et l’utilisation d’un nouveau certificat.  Une solution à ce problème est incluse dans Windows Server 2019 et ultérieur.
- Pour modifier des [valeurs du gestionnaire de configuration local PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) qui ont été définies lors de l’inscription initiale du nœud, telles que ConfigurationMode. Actuellement, ces valeurs de l’agent DSC peuvent être modifiées uniquement par le biais d’une réinscription. La seule exception concerne la configuration du nœud assignée au nœud, qui peut être modifiée directement dans Azure Automation DSC.

L’inscription peut être renouvelée selon la procédure initiale, en utilisant l’une des méthodes d’intégration décrites dans ce document. Il est inutile de désinscrire un nœud dans Azure Automation State Configuration avant de le réinscrire.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Résolution des problèmes liés à l’intégration de machines virtuelles Azure

Azure Automation State Configuration vous permet d’intégrer facilement des machines virtuelles Microsoft Azure à des fins de gestion de la configuration. En arrière-plan, l’extension Azure VM Desired State Configuration est utilisée pour enregistrer la machine virtuelle auprès d’Azure Automation State Configuration. Étant donné que cette extension s’exécute de façon asynchrone, il peut être très important d’en suivre la progression et de résoudre ses éventuels problèmes d’exécution.

> [!NOTE]
> Quelle que soit la méthode choisie pour intégrer une machine virtuelle Microsoft Azure sur Azure Automation State Configuration, l’enregistrement du nœud dans Azure Automation peut prendre jusqu’à une heure si l’extension Azure VM Desired State Configuration est utilisée. Cela est dû à l’installation de Windows Management Framework 5.0 sur la machine virtuelle par l’extension Azure VM DSC, nécessaire à l’intégration de la machine virtuelle dans Azure Automation State Configuration.

Pour résoudre les problèmes ou afficher l’état de l’extension Azure VM Desired State Configuration, rendez-vous dans le portail Azure, accédez à la machine virtuelle en cours d’intégration, puis cliquez sur **Extensions** sous **Paramètres**. Cliquez ensuite sur **DSC** ou **DSCForLinux** selon votre système d’exploitation. Pour plus de détails, vous pouvez cliquer sur **Afficher l’état détaillé**.

Pour plus d’informations sur la résolution des problèmes, consultez [Résolution des problèmes à l’aide d’Azure Automation Desired State Configuration (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)
- Pour savoir comment compiler des configurations DSC pour les affecter à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation State Configuration](automation-dsc-compile.md)
- Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Applets de commande d’Azure Automation State Configuration](/powershell/module/az.automation#automation)
- Pour obtenir des informations sur les prix, consultez [Tarification d’Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Pour voir un exemple d’utilisation d’Azure Automation State Configuration dans un pipeline de déploiement continu, consultez [Déploiement continu à l’aide d’Azure Automation State Configuration et de Chocolatey](automation-dsc-cd-chocolatey.md)
