---
title: Faire pivoter les clés secrètes dans Azure Stack | Microsoft Docs
description: Apprenez à faire pivoter vos clés secrètes dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a3dfce6ce1b136e39047cfd47b336b2fb2a35af9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258679"
---
# <a name="rotate-secrets-in-azure-stack"></a>Faire pivoter les clés secrètes dans Azure Stack

*Ces instructions s’appliquent uniquement aux systèmes intégrés Azure Stack version 1803 et versions ultérieures. N’essayez pas d’effectuer la rotation des secrets sur les versions d’Azure Stack antérieures à la version 1802*

Azure Stack utilise différents secrets pour assurer une communication sécurisée entre les ressources d’infrastructure Azure Stack et les services.

- **Secrets internes**  
Tous les certificats, mots de passe, chaînes sécurisées et clés utilisés par l’infrastructure Azure Stack sans intervention de l’Opérateur Azure Stack. 

- **Secrets externes**  
Certificats de service d’infrastructure pour les services accessibles de l’extérieur fournis par l’opérateur Azure Stack. Cela inclut les certificats pour les services suivants : 
    - Portail administrateur 
    - Portail public 
    - Administrateur Azure Resource Manager 
    - Global Azure Resource Manager 
    - Administrateur Keyvault 
    - KeyVault 
    - ACS (y compris stockage objet blob, table et file d’attente) 
    - ADFS<sup>*</sup>
    - Graph<sup>*</sup>

    > <sup>*</sup> Applicable uniquement si le fournisseur d’identité de l’environnement est AD FS (services de fédération Active Directory).

> [!NOTE]
> Toutes les autres clés et chaînes sécurisées, y compris les mots de passe BMC et switch ainsi que les mots de passe de compte utilisateur et administrateur sont toujours mis à jour manuellement par l’administrateur. 

Afin de maintenir l’intégrité de l’infrastructure Azure Stack, les opérateurs doivent pouvoir effectuer régulièrement la rotation des secrets de leur infrastructure à une fréquence conforme aux exigences de sécurité de leur organisation.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Rotation des secrets avec des certificats externes obtenus auprès d’une nouvelle autorité de certification

Azure Stack prend en charge la rotation des secrets avec des certificats externes obtenus auprès d’une nouvelle autorité de certification dans les contextes suivants :

|Autorité de certification de certificats installée|Autorité de certification vers laquelle effectuer la rotation|Prise en charge|Versions d’Azure Stack prises en charge|
|-----|-----|-----|-----|-----|
|De : Autorité de certification de certificats autosignés|Vers : Autorité de certification d’entreprise|Non pris en charge||
|De : Autorité de certification de certificats autosignés|Vers : Autorité de certification de certificats autosignés|Non pris en charge||
|De : Autorité de certification de certificats autosignés|Vers : Autorité de certification publique<sup>*</sup>|Prise en charge|1803 et ultérieure|
|De : Autorité de certification d’entreprise|Vers : Autorité de certification d’entreprise|Pris en charge à condition que les clients utilisent la MÊME autorité de certification d’entreprise que celle utilisée au moment du déploiement|1803 et ultérieure|
|De : Autorité de certification d’entreprise|Vers : Autorité de certification de certificats autosignés|Non pris en charge||
|De : Autorité de certification d’entreprise|Vers : Autorité de certification publique<sup>*</sup>|Prise en charge|1803 et ultérieure|
|De : Autorité de certification publique<sup>*</sup>|Vers : Autorité de certification d’entreprise|Non pris en charge|1803 et ultérieure|
|De : Autorité de certification publique<sup>*</sup>|Vers : Autorité de certification de certificats autosignés|Non pris en charge||
|De : Autorité de certification publique<sup>*</sup>|Vers : Autorité de certification publique<sup>*</sup>|Prise en charge|1803 et ultérieure|

<sup>*</sup> Les autorités de certification publiques mentionnées ici sont celles qui font partie du programme de certification racine approuvé Windows. La liste complète est disponible dans la rubrique [Programme de certification racine approuvé Microsoft : Participants (depuis le 27 juin 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Correction des alertes

Lorsque les secrets arrivent à expiration dans moins de 30 jours, les alertes suivantes sont générées dans le portail d’administration : 

- Pending service account password expiration (Expiration imminente du mot de passe du compte de service) 
- Pending internal certificate expiration (Expiration imminente du certificat interne) 
- Pending external certificate expiration (Expiration imminente du certificat externe) 

Pour remédier à ces alertes, exécutez la rotation des secrets en suivant les instructions ci-dessous.

## <a name="pre-steps-for-secret-rotation"></a>Étapes préliminaires à la rotation des secrets

1.  Notifiez vos utilisateurs de toute opération de maintenance. Planifiez les fenêtres de maintenance normale pendant les heures creuses, autant que possible. Les opérations de maintenance peuvent affecter les opérations du portail et les charges de travail des utilisateurs.

    > [!note]  
    > Les étapes suivantes s’appliquent uniquement lorsque vous effectuez la rotation des secrets externes Azure Stack.

2.  Préparez un nouveau jeu de certificats externes de remplacement. Le nouveau jeu répond aux spécifications de certificat décrites sur la page [Exigences de certificat pour infrastructure à clé publique Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  Stockez une sauvegarde des certificats utilisés pour la rotation dans un emplacement de sauvegarde sécurisé. Si votre rotation s’exécute puis échoue, remplacez les certificats dans le partage de fichiers par les copies de sauvegarde avant d’exécuter à nouveau la rotation. Remarque : vous devez conserver des copies de sauvegarde dans l’emplacement de sauvegarde sécurisé.
3.  Créez un partage de fichiers auquel vous pouvez accéder depuis les machines virtuelles ERCS. Le partage de fichiers doit être accessible en lecture et en écriture pour l’identité **CloudAdmin**.
4.  Ouvrez une console PowerShell ISE à partir d’un ordinateur sur lequel vous avez accès au partage de fichiers. Accédez à votre partage de fichiers. 
5.  Exécutez **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** pour créer les répertoires requis pour vos certificats externes.

## <a name="rotating-external-and-internal-secrets"></a>Rotation des clés internes et externes

Pour effectuer la rotation d’un secret interne et externe :

1. Dans le répertoire **/Certificats** nouvellement créé lors des étapes préliminaires, placez le nouveau jeu de certificats externes de remplacement dans la structure de répertoires en suivant le format décrit dans la section Certificats obligatoires de la page [Exigences de certificat pour infrastructure à clé publique Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Créez une session PowerShell avec le [point de terminaison privilégié](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) à l’aide du compte **CloudAdmin** et stockez les sessions en tant que variable. Vous allez utiliser cette variable comme paramètre dans l’étape suivante.

    > [!IMPORTANT]  
    > Ne pas entrer dans la session, stockez la session en tant que variable.
    
3. Exécutez **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Passez la variable de session PowerShell de votre point de terminaison privilégié en tant que paramètre de **Session**. 
4. Exécutez **Start-SecretRotation** avec les paramètres suivants :
    - **PfxFilesPath**  
    Spécifiez le chemin d’accès réseau vers votre répertoire de certificats créé précédemment.  
    - **PathAccessCredential**  
    Un objet PSCredential pour les informations d’identification pour accéder au partage. 
    - **CertificatePassword**  
    Une chaîne sécurisée du mot de passe utilisée pour tous les fichiers de certificat pfx créés.
4. Patientez pendant la rotation de vos secrets.  
Lorsque la rotation des secrets a réussi, la console affiche **Overall action status: Success** (état global de l’action : réussite). 
5. Une fois la rotation des secrets réussie, supprimez vos certificats du partage créé lors de l’étape préliminaire et stockez-les dans leur emplacement de sauvegarde sécurisé. 

## <a name="walkthrough-of-secret-rotation"></a>Procédure pas à pas de rotation des secrets

L’exemple PowerShell suivant montre les cmdlet et paramètres à exécuter afin d’effectuer la rotation des secrets.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Rotation des secrets internes uniquement

Pour effectuer uniquement la rotation des secrets internes Azure Stack :

1. Créez une session PowerShell avec le [point de terminaison privilégié](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Dans la session de point de terminaison privilégié, exécutez **Start-SecretRotation** sans arguments.

## <a name="start-secretrotation-reference"></a>Référence Start-SecretRotation

Effectue la rotation des secrets d’un système Azure Stack. Exécutée uniquement sur le point de terminaison privilégié Azure Stack.

### <a name="syntax"></a>Syntaxe

Chemin d’accès (par défaut)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Description

La cmdlet Start-SecretRotation effectue la rotation des secrets de l’infrastructure d’un système Azure Stack. Par défaut, elle effectue la rotation de tous les secrets exposés au réseau de l’infrastructure interne, et avec une entrée d’utilisateur elle procède également à la rotation des certificats de tous les points de terminaison d’infrastructure réseau externe. Lors de la rotation de points de terminaison d’infrastructure réseau externe, Start-SecretRotation doit être exécuté via un bloc de script Invoke-Command, avec la session de point de terminaison privilégié de l’environnement Azure Stack transmise comme paramètre de session.
 
### <a name="parameters"></a>Paramètres

| Paramètre | Type | Obligatoire | Position | Default | Description |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Chaîne  | False  | named  | Aucun  | Le chemin d’accès au partage de fichiers pour le répertoire **\Certificates** contenant tous les certificats de points de terminaison réseau externe. Uniquement requis lors de la rotation de secrets internes et externes. Le répertoire de fin doit être **\Certificates**. |
| CertificatePassword | SecureString | False  | named  | Aucun  | Le mot de passe pour tous les certificats fournis dans le -PfXFilesPath. Valeur requise si PfxFilesPath est fourni lors de la rotation des secrets internes et externes. |
|

### <a name="examples"></a>Exemples
 
**Effectuer la rotation des secrets d’infrastructure interne uniquement**

```powershell  
PS C:\> Start-SecretRotation  
```

Cette commande effectue la rotation de tous les secrets de l’infrastructure exposés au réseau interne Azure Stack. Start-SecretRotation effectue la rotation de tous les secrets générés par la pile, mais comme aucun certificat n’est fourni, la rotation des certificats de points de terminaison externes ne sera pas effectuée.  

**Effectuer la rotation des secrets d’infrastructure internes et externes**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Cette commande effectue la rotation de tous les secrets de l’infrastructure exposés au réseau interne Azure Stack, ainsi que les certificats TLS utilisés pour les points de terminaison d’infrastructure réseau externe Azure Stack. Start-SecretRotation effectue la rotation de tous les secrets générés par la pile, et comme des certificats sont fournis, la rotation des certificats de points de terminaison externes sera également effectuée.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Mise à jour du mot de passe pour le contrôleur BMC (Baseboard Management Controller)

Le contrôleur BMC (Baseboard Management Controller) analyse l’état physique de vos serveurs. Les spécifications et les instructions sur la mise à jour du mot de passe du contrôleur BMC varient en fonction de votre fournisseur de matériel OEM. Il est recommandé de mettre régulièrement à jour vos mots de passe des composants Azure Stack.

1. Mettez à jour le contrôleur BMC sur les serveurs physiques de Azure Stack en suivant les instructions de votre fabricant OEM. Tous les contrôleurs BMC de votre environnement doivent avoir le même mot de passe.
2. Ouvrez un point de terminaison privilégié dans des sessions Azure Stack. Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md).
3. Lorsque votre invite PowerShell devient **[adresse IP ou nom de machine virtuelle ERCS]: PS>** ou **[azs-ercs01]: PS>**, en fonction de l’environnement, exécutez `Set-BmcPassword` en exploitant `invoke-command`. Passez la variable de session de votre point de terminaison privilégié en tant que paramètre. Par exemple : 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Vous pouvez également utiliser la version statique de PowerShell avec les mots de passe sous forme de lignes de code :
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Étapes suivantes

[Apprenez-en davantage sur la sécurité dans Azure Stack](azure-stack-security-foundations.md)
