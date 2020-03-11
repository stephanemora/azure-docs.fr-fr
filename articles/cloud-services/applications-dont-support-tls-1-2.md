---
title: Résolution des problèmes provoqués par les applications qui ne prennent pas en charge TLS 1.2 | Microsoft Docs
description: Résolution des problèmes provoqués par les applications qui ne prennent pas en charge TLS 1.2
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270859"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Résolution des problèmes liés aux applications qui ne prennent pas en charge TLS 1.2
Cet article décrit comment activer les anciens protocoles TLS (TLS 1.0 et 1.1) et appliquer les suites de chiffrement héritées pour prendre en charge les protocoles supplémentaires sur les rôles web et worker du service cloud Windows Server 2019. 

Parallèlement à nos mesures visant à déprécier TLS 1.0 et TLS 1.1, nous comprenons que certains clients aient besoin de prendre en charge les anciens protocoles et suites de chiffrement dans le but de planifier correctement leur dépréciation.  Bien nous ne recommandions pas de réactiver ces valeurs héritées, les conseils suivants sont destinés aux clients qui peuvent en avoir besoin. Nous encourageons les clients à évaluer le risque de régression avant d’implémenter les changements décrits dans cet article. 

> [!NOTE]
> Les versions de la famille 6 du système d’exploitation invité imposent le protocole TLS 1.2 en désactivant les chiffrements 1.0/1.1. 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Fin de la prise en charge de TLS 1.0/TLS 1.1 et des anciennes suites de chiffrement 
Dans le cadre de son engagement à utiliser un chiffrement optimal, Microsoft a annoncé son intention de renoncer aux protocoles TLS 1.0 et 1.1 en juin 2017.   Depuis cette annonce initiale, Microsoft a communiqué sa volonté de désactiver les protocoles TLS (Transport Layer Security) 1.0 et 1.1 par défaut dans les versions prises en charge de Microsoft Edge et d’Internet Explorer 11 au cours du premier semestre 2020.  Des annonces similaires de la part d’Apple, de Google et de Mozilla confirment la tendance dans le secteur.   

## <a name="tls-configuration"></a>Configuration TLS  
L’image du serveur cloud Windows Server 2019 est configurée de telle sorte que TTLS 1.0 et TLS 1.1 sont désactivés au niveau du Registre. Cela signifie que les applications déployées sur cette version de Windows ET que la pile Windows utilisée pour la négociation TLS n’autorisent pas les communications TLS 1.0 et TLS 1.1.   

Le serveur est également fourni avec un ensemble limité de suites de chiffrement : 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>Étape 1 : Créer le script PowerShell pour activer TLS 1.0 et TLS 1.1 

Utilisez le code suivant comme exemple pour créer un script qui active les anciens protocoles et suites de chiffrement. Dans cette documentation, ce script est nommé comme ceci : **TLSsettings.ps1**. Stockez ce script sur votre Bureau local pouvoir y accéder facilement lors des étapes ultérieures. 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>Étape 2 : Créer un fichier de commandes 

Créez un fichier CMD nommé **RunTLSSettings.cmd** à l’aide de la commande ci-dessous. Stockez ce script sur votre Bureau local pouvoir y accéder facilement lors des étapes ultérieures. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Étape 3 : Ajouter la tâche de démarrage à la définition du service de rôle (csdef) 

Ajoutez l’extrait de code suivant à votre fichier de définition de service existant. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Voici un exemple qui montre les rôles worker et web. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>Étape 5 : Ajouter les scripts à votre service cloud 

1) Dans Visual Studio, cliquez avec le bouton droit sur votre WebRole
2) Sélectionnez **Ajouter**
3) Sélectionnez **Élément existant**
4) Dans l’Explorateur de fichiers, accédez à votre Bureau où vous avez stocké les fichiers **TLSsettings.ps1** et **RunTLSSettings.cmd** 
5) Sélectionnez les deux fichiers pour les ajouter à votre projet Services cloud

## <a name="step-6-enable-copy-to-output-directory"></a>Étape 6 : Activer la copie dans le répertoire de sortie

Pour vous assurer que les scripts sont chargés avec chaque mise à jour envoyée à partir de Visual Studio, le paramètre *Copier dans le répertoire de sortie* doit être défini sur *Toujours copier*

1) Sous votre WebRole, cliquez avec le bouton droit sur RunTLSSettings.cmd
2) Sélectionnez **Propriétés**
3) Dans l’onglet Propriétés, changez *Copier dans le répertoire de sortie* sur *Toujours copier*
4) Répétez les étapes pour **TLSsettings.ps1**

## <a name="step-7-publish--validate"></a>Étape 7 : Publier et valider

Maintenant que les étapes ci-dessus ont été effectuées, publiez la mise à jour sur votre service cloud existant. 

Vous pouvez utiliser [SSLLabs](https://www.ssllabs.com/) pour valider l’état TLS de vos points de terminaison. 

 
