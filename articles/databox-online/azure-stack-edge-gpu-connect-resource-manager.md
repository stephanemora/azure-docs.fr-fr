---
title: Se connecter à Azure Resource Manager sur votre appareil GPU Azure Stack Edge
description: Décrit comment se connecter à l’infrastructure Azure Resource Manager s’exécutant sur votre Azure Stack Edge Pro avec GPU à l’aide d’Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ac064d53f4d85d8f517ef7bba67e88abe625f16
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515669"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-device"></a>Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager fournit une couche de gestion qui vous permet de créer, mettre à jour et supprimer des ressources dans votre abonnement Azure. L’appareil Azure Stack Edge prend en charge les mêmes API Azure Resource Manager pour créer, mettre à jour et supprimer des machines virtuelles dans un abonnement local. Cette prise en charge vous permet de gérer l’appareil d’une manière cohérente avec le cloud. 

Cet article explique comment se connecter aux API locales sur votre appareil Azure Stack Edge via Azure Resource Manager à l’aide d’Azure PowerShell.


## <a name="endpoints-on-azure-stack-edge-device"></a>Point de terminaison sur l’appareil Azure Stack Edge

Le tableau suivant récapitule les différents points de terminaison exposés sur votre appareil, les protocoles pris en charge et les ports pour accéder à ces points de terminaison. Tout au long de l’article, vous trouverez des références à ces points de terminaison.

| # | Point de terminaison | Protocoles pris en charge | Port utilisé | Utilisé pour |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Pour se connecter à Azure Resource Manager pour l’automatisation |
| 2. | Service d’émission de jeton de sécurité | https | 443 | Pour s’authentifier via des jetons d’accès et d’actualisation |
| 3. | Blob* | https | 443 | Pour se connecter au service Stockage Blob via REST |

\* *La connexion au point de terminaison de stockage blob n’est pas requise pour se connecter à Azure Resource Manager.*
 
## <a name="connecting-to-azure-resource-manager-workflow"></a>Connexion au flux de travail Azure Resource Manager

Le processus de connexion aux API locales de l’appareil à l’aide d’Azure Resource Manager nécessite les étapes suivantes :

| N° de l’étape | Vous accomplirez cette tâche... | .. à cet emplacement. |
| --- | --- | --- |
| 1. | [Configurer votre appareil Azure Stack Edge](#step-1-configure-azure-stack-edge-device) | Interface utilisateur web locale |
| 2. | [Créer et installer des certificats](#step-2-create-and-install-certificates) | Client Windows/interface utilisateur web locale |
| 3. | [Examiner les conditions préalables et configurer en conséquence](#step-3-install-powershell-on-the-client) | Client Windows |
| 4. | [Configurer Azure PowerShell sur le client](#step-4-set-up-azure-powershell-on-the-client) | Client Windows |
| 5. | [Modifier le fichier hôte pour la résolution de nom de point de terminaison](#step-5-modify-host-file-for-endpoint-name-resolution) | Client Windows ou serveur DNS |
| 6. | [Vérifier que le nom du point de terminaison est résolu](#step-6-verify-endpoint-name-resolution-on-the-client) | Client Windows |
| 7. | [Utiliser des cmdlets Azure PowerShell pour vérifier la connexion à Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Client Windows |

Les sections suivantes décrivent chacune des étapes ci-dessus de connexion à Azure Resource Manager.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, assurez-vous que le client utilisé pour se connecter à l’appareil via Azure Resource Manager utilise le protocole TLS 1.2. Pour plus d’informations, accédez à [Configurer le protocole TLS 1.2 sur le client Windows accédant à l’appareil Azure Stack Edge](azure-stack-edge-gpu-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-device"></a>Étape 1 : Configurer l’appareil Azure Stack Edge 

Suivez les étapes suivantes dans l’interface utilisateur web locale de votre appareil Azure Stack Edge.

1. Définissez les paramètres réseau de votre appareil Azure Stack Edge. 

    ![Page « Paramètres réseau » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Prenez note de l’adresse IP de l’appareil. Vous l’utiliserez ultérieurement.

2. Configurez le nom de l’appareil et le domaine DNS à partir de la page **Appareil**. Notez le nom de l’appareil et le domaine DNS, car vous les utiliserez ultérieurement.

    ![Page « Appareil » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Le nom de l’appareil et le domaine DNS seront utilisés pour former les points de terminaison exposés.
    > Utilisez les points de terminaison Azure Resource Manager et Blob de la page **Appareil** de l’interface utilisateur web locale.


## <a name="step-2-create-and-install-certificates"></a>Étape 2 : Créer et installer les certificats

Les certificats garantissent que votre communication est approuvée. Sur votre appareil Azure Stack Edge, l’appliance auto-signée, le blob et les certificats Azure Resource Manager sont générés automatiquement. Si vous le souhaitez, vous pouvez également insérer votre propre blob signé ainsi que vos certificats Azure Resource Manager.

Lorsque vous apportez votre propre certificat signé, vous avez également besoin de la chaîne de signature correspondante du certificat. Pour la chaîne de signature, Azure Resource Manager et les certificats de blob sur l’appareil, vous aurez besoin des certificats correspondants sur l’ordinateur client pour vous authentifier et communiquer avec l’appareil.

Pour vous connecter à Azure Resource Manager, vous devez créer ou obtenir des certificats de chaîne de signature et de point de terminaison, importer ces certificats sur votre client Windows, puis télécharger ces certificats sur l’appareil.

### <a name="create-certificates"></a>Créer des certificats

À des fins de développement ou de test uniquement, vous pouvez utiliser Windows PowerShell pour créer des certificats sur votre système local. Lors de la création des certificats pour le client, suivez les instructions suivantes :

1. Vous commencer par créer un certificat racine pour la chaîne de signature. Pour plus d’informations, consultez les étapes à suivre pour [Créer des certificats de chaîne de signature](azure-stack-edge-gpu-create-certificates-powershell.md#create-signing-chain-certificate).

2. Vous pouvez ensuite créer les certificats de point de terminaison pour Azure Resource Manager et le blob (facultatif). Vous pouvez accéder à ces points de terminaison à partir de la page **Appareil** dans l’interface utilisateur web locale. Consultez les étapes à suivre pour [Créer des certificats de point de terminaison](azure-stack-edge-gpu-create-certificates-powershell.md#create-signed-endpoint-certificates).

3. Pour tous ces certificats, assurez-vous que le nom de l’objet et l’autre nom de l’objet sont conformes aux recommandations suivantes :

    |Type |Nom de l’objet (SN)  |Autre nom de l’objet (SAN)  |Exemple de nom de sujet |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Stockage blob*|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificat unique à plusieurs SAN pour les deux points de terminaison|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

\* Le stockage blob n’est pas requis pour se connecter à Azure Resource Manager. Il est répertorié au cas où vous créez des comptes de stockage locaux sur votre appareil.

Pour plus d’informations sur les certificats, consultez la procédure [Charger des certificats sur votre appareil et importer des certificats sur les clients qui accèdent à votre appareil](azure-stack-edge-gpu-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Charger des certificats sur l’appareil

Les certificats que vous avez créés à l’étape précédente se trouvent dans le magasin personnel sur votre client. Ces certificats doivent être exportés sur votre client dans des fichiers de format appropriés qui peuvent ensuite être chargés sur votre appareil.

1. Le certificat racine doit être exporté sous la forme d’un fichier de format DER avec l’extension *.cer*. Pour obtenir des instructions détaillées, consultez [Exporter des certificats au format .cer](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format).

2. Les certificats de point de terminaison doivent être exportés en tant que fichiers *.pfx* avec des clés privées. Pour obtenir des instructions détaillées, consultez [Exporter des certificats en tant que fichiers .pfx avec des clés privées](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key).

3. Les certificats racines et de point de terminaison sont ensuite chargés sur l’appareil à l’aide de l’option **+ Ajouter un certificat** dans la page **Certificats** de l’interface utilisateur web locale. Pour charger les certificats, suivez les étapes décrites dans la section [Charger des certificats](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importer des certificats sur le client exécutant Azure PowerShell

Le client Windows dans lequel vous allez appeler les API Azure Resource Manager doit établir la confiance avec l’appareil. À cette fin, les certificats que vous avez créés à l’étape précédente doivent être importés sur votre client Windows dans le magasin de certificats approprié.

1. Le certificat racine que vous avez exporté au format DER avec l’extension *.cer* doit maintenant être importé dans les Autorités de certification racine approuvées sur votre système client. Pour obtenir des instructions détaillées, consultez [Importer des certificats dans le magasin Autorités de certification racine approuvées](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

2. Les certificats de point de terminaison que vous avez exportés dans un fichier de format *.pfx* doivent être exportés dans un fichier de format *.cer*. Ce fichier *.cer* est ensuite importé dans le magasin de certificats **Personnel** sur votre système. Pour obtenir des instructions détaillées, consultez [Importer des certificats dans le magasin personnel](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Étape 3 : Installer PowerShell sur le client 

### <a name="az"></a>[Az](#tab/Az)

Votre client Windows doit remplir les conditions préalables suivantes :


1. Exécuter PowerShell Version 5.0. Vous devez disposer de PowerShell version 5.0. Pour vérifier la version de PowerShell installée sur votre système, exécutez la cmdlet suivante :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Comparez la version **Majeure** et assurez-vous qu’il s’agit de la version 5.0 ou d’une version ultérieure.

    Si votre version est obsolète, consultez [Mise à niveau des instances Windows PowerShell existantes](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    Si vous n\'avez pas PowerShell 5.0, suivez les instructions d’[Installation de PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    Voici un exemple de sortie obtenue.

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      19041  906
    ```
    
1. Vous pouvez accéder à la PowerShell Gallery.

    Démarrez PowerShell en tant qu'administrateur. Vérifiez que la version PowerShellGet est antérieure à 2.2.3. Vérifiez également que la `PSGallery` est inscrite en tant que référentiel.

    ```powershell
    Install-Module PowerShellGet –MinimumVersion 2.2.3
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Voici un exemple de sortie obtenue.
    
    ```output
    PS C:\windows\system32> Install-Module PowerShellGet –MinimumVersion 2.2.3
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```    

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Votre client Windows doit remplir les conditions préalables suivantes :


1. Exécuter PowerShell Version 5.0. Vous devez disposer de PowerShell version 5.0. PowerShell Core n’est pas pris en charge. Pour vérifier la version de PowerShell installée sur votre système, exécutez la cmdlet suivante :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Comparez la version **Majeure** et assurez-vous qu’il s’agit de la version 5.0 ou d’une version ultérieure.

    Si votre version est obsolète, consultez [Mise à niveau des instances Windows PowerShell existantes](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell).

    Si vous n\'avez pas PowerShell 5.0, suivez les instructions d’[Installation de PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    Voici un exemple de sortie obtenue.

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Vous pouvez accéder à la PowerShell Gallery.

    Démarrez PowerShell en tant qu'administrateur. Vérifiez que la `PSGallery` est inscrite en tant que référentiel.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Voici un exemple de sortie obtenue.
    
    ```output
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
---    
Si votre référentiel n’est pas approuvé ou si vous avez besoin d’informations supplémentaires, consultez [Vérifier l’accessibilité de PowerShell Gallery](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Étape 4 : Configurer Azure PowerShell sur le client 

### <a name="az"></a>[Az](#tab/Az)

Vous allez installer sur votre client des modules Azure PowerShell qui fonctionneront avec votre appareil.

1. Exécutez PowerShell ISE en tant qu’administrateur. Vous devez accéder à la PowerShell Gallery. 


1. Vérifiez tout d’abord qu’il n’existe aucune version de modules `AzureRM` et `Az` sur votre client. Pour le savoir, exécutez les commandes suivantes :

    ```powershell
    # Check existing versions of AzureRM modules
    Get-InstalledModule -Name AzureRM -AllVersions

    # Check existing versions of Az modules
    Get-InstalledModule -Name Az -AllVersions
    ```

    S’il existe des versions, utilisez le cmdlet `Uninstall-Module` pour les désinstaller. Pour plus d'informations, consultez la rubrique 
    - [Désinstaller des modules AzureRM](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-az-module).
    - [Désinstaller des modules Az](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-azurerm-module).

1. Pour installer les modules Azure PowerShell requis à partir de la PowerShell Gallery, exécutez la commande suivante :

    - Si votre client utilise PowerShell Core version 7.0 et versions ultérieures :

        ```powershell
        # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet.
        
        Install-Module -Name Az.BootStrapper
        
        # Install and import the API Version Profile into the current PowerShell session.
        
        Use-AzProfile -Profile 2020-09-01-hybrid -Force
        
        # Confirm the installation of PowerShell
        Get-Module -Name "Az*" -ListAvailable
        ```
    
    - Si votre client utilise PowerShell 5.1 et versions ultérieures :
        
        ```powershell
        #Install the Az module version 1.10.0
        
        Install-Module –Name Az –RequiredVersion 1.10.0    
        ```

3.  Assurez-vous que le module Az version 1.10.0 s’exécute à la fin de l’installation. 
   

    Si vous avez utilisé PowerShell Core 7.0 et versions ultérieures, l’exemple de sortie ci-dessous indique que les modules Az version 1.10.0 ont été installés avec succès.
    
    ```output
   
    PS C:\windows\system32> Install-Module -Name Az.BootStrapper
    PS C:\windows\system32> Use-AzProfile -Profile 2020-09-01-hybrid -Force
    Loading Profile 2020-09-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Az*" -ListAvailable
    ```

    Si vous avez utilisé PowerShell 5.1 et versions ultérieures, l’exemple de sortie ci-dessous indique que les modules Az version 1.10.0 ont été installés avec succès.
     
    ```powershell
    PS C:\WINDOWS\system32> Get-InstalledModule -Name Az -AllVersions
    Version              Name                                Repository           Description
    -------              ----                                ----------           ------
    1.10.0               Az                                  PSGallery            Mic...  
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Vous allez installer sur votre client des modules Azure PowerShell qui fonctionneront avec votre appareil.

1. Exécutez PowerShell ISE en tant qu’administrateur. Vous devez accéder à la PowerShell Gallery. 


2. Pour installer les modules Azure PowerShell requis à partir de la PowerShell Gallery, exécutez la commande suivante :

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Assurez-vous que le module Azure-RM version 2.5.0 s’exécute à la fin de l’installation. 
    Si vous disposez d’une version existante du module Azure-RM qui ne correspond pas à la version requise, désinstallez-la à l’aide de la commande suivante :

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Vous devez à présent réinstaller la version requise.
   

    Un exemple de sortie est illustré ci-dessous, indiquant que les modules AzureRM version 2.5.0 ont été installés avec succès.
    
    ```powershell
    PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
    PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Loading Profile 2019-03-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
     
        Directory: C:\Program Files\WindowsPowerShell\Modules
     
    ModuleType Version    Name                          ExportedCommands
    ---------- -------    ----                          ----------------
    Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
    Script     2.5.0      AzureRM
    Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
    Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
    Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
    Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
    Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
    Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
    Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
    Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
    Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
    Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
    Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
    Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...
    
     
        Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
     
    ModuleType Version    Name                            ExportedCommands
    ---------- -------    ----                           ----------------
    Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
    ```
---   

## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Étape 5 : Modifier le fichier hosts pour la résolution de noms de point de terminaison 

Vous allez maintenant ajouter l’adresse IP de l’appareil à :

- Au fichier hosts sur le client, ou
- configuration du serveur DNS.

> [!IMPORTANT]
> Nous vous recommandons de modifier la configuration du serveur DNS pour la résolution des noms de point de terminaison.

Sur le client Windows que vous utilisez pour vous connecter à l’appareil, procédez comme suit :

1. Démarrez le **Bloc-notes** en tant qu’administrateur, puis ouvrez le fichier **hosts** qui se trouve dans C:\Windows\System32\Drivers\etc.

    ![Fichier hosts dans l’Explorateur Windows](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Ajoutez les entrées suivantes à votre fichier **hosts**, en les remplaçant par les valeurs appropriées pour votre appareil : 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > L’entrée dans le fichier hosts doit correspondre exactement à celle fournie pour se connecter à Azure Resource Manager à une étape ultérieure. Assurez-vous que l’entrée Domaine DNS ici est entièrement en minuscules. Pour obtenir les valeurs de `<appliance name>` et `<DNS domain>`, accédez à la page **Appareil** dans l’interface utilisateur locale de votre appareil.

    Vous avez enregistré l’adresse IP de l’appareil à partir de l’interface utilisateur web locale lors d’une étape précédente.

    L’entrée `login.<appliance name>.<DNS domain>` est le point de terminaison du service d’émission de jeton de sécurité (STS). STS est responsable de la création, de la validation, du renouvellement et de l’annulation des jetons de sécurité. Le service de jeton de sécurité est utilisé pour créer le jeton d’accès et le jeton d’actualisation utilisés pour la communication continue entre l’appareil et le client.

    Le point de terminaison du stockage blob est facultatif lors de la connexion à Azure Resource Manager. Ce point de terminaison est nécessaire pour transférer des données vers Azure via des comptes de stockage.

3. Pour référence, utilisez l’image suivante. Enregistrez le fichier **hosts**.

    ![Fichier hosts dans le Bloc-notes](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Étape 6 : Vérifier la résolution de noms de point de terminaison sur le client

Vérifiez si le nom de point de terminaison est résolu sur le client que vous utilisez pour vous connecter à l’appareil.

1. Vous pouvez vous servir de l’utilitaire de ligne de commande `ping.exe` pour vérifier que le nom du point de terminaison est résolu. À partir d’une adresse IP donnée, la commande `ping` renvoie le nom d’hôte TCP/IP de l’ordinateur que vous suivez\'.

    Ajoutez le commutateur `-a` à la ligne de commande, comme dans l’exemple ci-dessous. Si le nom d’hôte peut être retourné, la commande le retourne également cette information potentiellement précieuse dans la réponse.

    ![Ping dans l’invite de commandes](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Étape 7 : Définir l’environnement Azure Resource Manager

### <a name="az"></a>[Az](#tab/Az)

Définissez l’environnement Azure Resource Manager et vérifiez que la communication de votre appareil avec le client via Azure Resource Manager fonctionne correctement. Pour effectuer cette vérification, procédez comme suit :


1. Utilisez la cmdlet `Add-AzEnvironment` pour vérifier que la communication via Azure Resource Manager fonctionne correctement et que les appels d’API transitent par le port dédié pour Azure Resource Manager (443).

    La cmdlet `Add-AzEnvironment` ajoute des points de terminaison et des métadonnées pour permettre aux cmdlets d’Azure Resource Manager de se connecter à une nouvelle instance d’Azure Resource Manager. 


    > [!IMPORTANT]
    > L’URL de point de terminaison d’Azure Resource Manager que vous fournissez dans la cmdlet suivante respecte la casse. Assurez-vous que l’URL de point de terminaison est entièrement en minuscules et qu’elle correspond à ce que vous avez fourni dans le fichier hosts. Si la casse ne correspond pas, une erreur s’affiche.

    ```powershell
    Add-AzEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Voici un exemple de sortie obtenue :
    
    ```output
    PS C:\WINDOWS\system32> Add-AzEnvironment -Name AzASE -ARMEndpoint "https://management.myasegpu.wdshcsso.com/"
    
    Name  Resource Manager Url                      ActiveDirectory Authority
    ----  --------------------                      -------------------------
    AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...
    ```

2. Pour l’environnement, définissez Azure Stack Edge, et pour le port à utiliser pour les appels à Azure Resource Manager, définissez le port 443. Vous définissez l’environnement de deux manières :

    - Définissez l’environnement. Tapez la commande suivante :

        ```powershell
        Set-AzEnvironment -Name <Environment Name>
        ```
        
        Voici un exemple de sortie :

        ```output
        PS C:\WINDOWS\system32> Set-AzEnvironment -Name AzASE

        Name  Resource Manager Url                      ActiveDirectory Authority
        ----  --------------------                      -------------------------
        AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...     
        ```
        Pour plus d’informations, accédez à [Set-AzEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).    

    - Définissez l’environnement inclus pour chaque cmdlet que vous exécutez. Cela permet de s’assurer que tous les appels d’API transitent par l’environnement approprié. Par défaut, les appels transitent par le cloud public Azure, mais vous souhaitez qu’ils transitent par l’environnement que vous avez défini pour l’appareil Azure Stack Edge.

    - Pour plus d’informations sur la manière de changer d’environnement, consultez [Changer d’environnement Az](#switch-environments).

2. Appelez les API de l’appareil local pour authentifier les connexions à Azure Resource Manager. 

    1. Ces informations d’identification sont destinées à un compte d’ordinateur local et sont uniquement utilisées pour l’accès d’API.

    2. Vous pouvez vous connecter via `login-AzAccount` ou la commande `Connect-AzAccount`. 

        1. Pour vous connecter, entrez la commande suivante. 
        
            ```powershell
            $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            ```

            Utilisez l’ID de locataire c0257de7-538f-415c-993a-1b87a031879d comme dans cette instance. Il est codé en dur.
            Utilisez le nom d’utilisateur et le mot de passe suivants.

            - **Nom d’utilisateur** - *EdgeArmUser*

            - **Mot de passe** - [Définissez le mot de passe pour Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md), puis utiliser ce mot de passe pour vous connecter. 
       


            Voici un exemple de sortie pour `Connect-AzAccount` :

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzASE
            
            PS C:\windows\system32>
            ```                   
        
            Une autre façon de se connecter consiste à utiliser la cmdlet `login-AzAccount`. 
            
            `login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Voici un exemple de sortie : 
         
            ```output
            PS C:\WINDOWS\system32> login-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account               SubscriptionName              TenantId
            -------               ----------------              --------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a...            
            
            PS C:\WINDOWS\system32>
            ```
3. Pour vérifier que la connexion à l’appareil fonctionne, utilisez la commande `Get-AzResource`. Cette commande doit retourner toutes les ressources qui existent localement sur l’appareil.

    Voici un exemple de sortie :

    ```output
    PS C:\WINDOWS\system32> Get-AzResource
    
    Name              : aseimagestorageaccount
    ResourceGroupName : ase-image-resourcegroup
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ase-image-resourcegroup/providers/Microsoft.Storage/storageac
                        counts/aseimagestorageaccount
    Tags              :
    
    Name              : myaselinuxvmimage1
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Compute/images
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Compute/images/myaselinuxvmimage1
    Tags              :
    
    Name              : ASEVNET
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Network/virtualNetworks
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    Tags              :
    
    PS C:\WINDOWS\system32>  
    ```

   

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Définissez l’environnement Azure Resource Manager et vérifiez que la communication de votre appareil avec le client via Azure Resource Manager fonctionne correctement. Pour effectuer cette vérification, procédez comme suit :


1. Utilisez la cmdlet `Add-AzureRmEnvironment` pour vérifier que la communication via Azure Resource Manager fonctionne correctement et que les appels d’API transitent par le port dédié pour Azure Resource Manager (443).

    La cmdlet `Add-AzureRmEnvironment` ajoute des points de terminaison et des métadonnées pour permettre aux cmdlets d’Azure Resource Manager de se connecter à une nouvelle instance d’Azure Resource Manager. 


    > [!IMPORTANT]
    > L’URL de point de terminaison d’Azure Resource Manager que vous fournissez dans la cmdlet suivante respecte la casse. Assurez-vous que l’URL de point de terminaison est entièrement en minuscules et qu’elle correspond à ce que vous avez fourni dans le fichier hosts. Si la casse ne correspond pas, une erreur s’affiche.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Voici un exemple de sortie obtenue :
    
    ```output
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Pour l’environnement, définissez Azure Stack Edge, et pour le port à utiliser pour les appels à Azure Resource Manager, définissez le port 443. Vous définissez l’environnement de deux manières :

    - Définissez l’environnement. Tapez la commande suivante :

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Pour plus d’informations, accédez à [Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).

    - Définissez l’environnement inclus pour chaque cmdlet que vous exécutez. Cela permet de s’assurer que tous les appels d’API transitent par l’environnement approprié. Par défaut, les appels transitent par le cloud public Azure, mais vous souhaitez qu’ils transitent par l’environnement que vous avez défini pour l’appareil Azure Stack Edge.

    - Pour plus d’informations sur la manière de changer d’environnement AzureRM, consultez [Changer d’environnement](#switch-environments).

2. Appelez les API de l’appareil local pour authentifier les connexions à Azure Resource Manager. 

    1. Ces informations d’identification sont destinées à un compte d’ordinateur local et sont uniquement utilisées pour l’accès d’API.

    2. Vous pouvez vous connecter via `login-AzureRMAccount` ou la commande `Connect-AzureRMAccount`. 

        1. Pour vous connecter, entrez la commande suivante. L’ID de locataire dans cette instance est codé en dur - c0257de7-538f-415c-993a-1b87a031879d. Utilisez le nom d’utilisateur et le mot de passe suivants.

            - **Nom d’utilisateur** - *EdgeArmUser*

            - **Mot de passe** - [Définissez le mot de passe pour Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md), puis utiliser ce mot de passe pour vous connecter. 

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Une autre façon de se connecter consiste à utiliser la cmdlet `login-AzureRmAccount`. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Voici un exemple de sortie de la commande. 
         
            ```output
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```
---

Si vous rencontrez des problèmes avec vos connexions Azure Resource Manager, consultez [Résolution des problèmes d’Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md) pour obtenir de l’aide. 

> [!IMPORTANT]
> La connexion à Azure Resource Manager expire toutes les 1,5 heures ou si votre appareil Azure Stack Edge redémarre. Dans ce cas, toute cmdlet que vous exécutez retourne un message d’erreur indiquant que vous n’êtes plus connecté à Azure. Vous devez vous reconnecter.

## <a name="switch-environments"></a>Changer d’environnement

Vous devrez peut-être basculer entre deux environnements.

### <a name="az"></a>[Az](#tab/Az)

Exécutez la commande `Disconnect-AzAccount` pour basculer vers un autre `AzEnvironment`. Si vous utilisez `Set-AzEnvironment` et `Login-AzAccount` sans `Disconnect-AzAccount`, l’environnement ne change pas.  

Les exemples suivants montrent comment basculer entre deux environnements, `AzASE1` et `AzASE2`.

Tout d’abord, répertoriez tous les environnements existants sur votre client.


```output
PS C:\WINDOWS\system32> Get-AzEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Ensuite, accédez à l’environnement auquel vous êtes actuellement connecté via votre Azure Resource Manager.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Vous devez maintenant vous déconnecter de l’environnement actuel avant de basculer vers l’autre environnement.


```output
PS C:\WINDOWS\system32> Disconnect-AzAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Connectez-vous à l’autre environnement. Voici un exemple de sortie obtenue.

```output
PS C:\WINDOWS\system32> Login-AzAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Exécutez cette cmdlet pour vérifier l’environnement auquel vous êtes connecté.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Vous avez maintenant basculé vers l’environnement voulu.

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Exécutez la commande `Disconnect-AzureRmAccount` pour basculer vers un autre `AzureRmEnvironment`. Si vous utilisez `Set-AzureRmEnvironment` et `Login-AzureRmAccount` sans `Disconnect-AzureRmAccount`, l’environnement ne change pas.  

Les exemples suivants montrent comment basculer entre deux environnements, `AzDBE1` et `AzDBE2`.

Tout d’abord, répertoriez tous les environnements existants sur votre client.


```output
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Ensuite, accédez à l’environnement auquel vous êtes actuellement connecté via votre Azure Resource Manager.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Vous devez maintenant vous déconnecter de l’environnement actuel avant de basculer vers l’autre environnement.


```output
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Connectez-vous à l’autre environnement. Voici un exemple de sortie obtenue.

```output
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Exécutez cette cmdlet pour vérifier l’environnement auquel vous êtes connecté.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
---

Vous avez maintenant basculé vers l’environnement voulu.

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes d’Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md).
- [Déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
