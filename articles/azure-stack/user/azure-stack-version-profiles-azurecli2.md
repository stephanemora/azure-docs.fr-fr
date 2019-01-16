---
title: Se connecter à Azure Stack avec l’interface CLI | Microsoft Docs
description: Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 2ab696436a8cf139eff92edc3b8ff2c27b40a7aa
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018379"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Utiliser des profils de version des API avec Azure CLI dans Azure Stack

Vous pouvez suivre les étapes de cet article pour configurer l’interface de ligne de commande (CLI) Azure pour gérer les ressources du Kit de développement Azure Stack à partir des plateformes clientes Linux, Mac et Windows.

## <a name="install-cli"></a>Installer l’interface CLI

Connectez-vous à votre station de travail de développement et installez l’interface CLI. Azure Stack nécessite la version 2.0 ou ultérieure d’Azure CLI. Vous pouvez l’installer en suivant la procédure décrite dans l’article [Installer Azure CLI](/cli/azure/install-azure-cli). Pour vérifier que l’installation a réussi, ouvrez un terminal ou une fenêtre d’invite de commandes, puis exécutez la commande suivante :

```azurecli
az --version
```

Vous devriez voir la version d’Azure CLI et d’autres bibliothèques dépendantes installées sur votre ordinateur.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

1. Récupérez le certificat racine d’autorité de certification Azure Stack auprès de [votre opérateur Azure Stack](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) et approuvez-le. Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant.

1. Trouvez l’emplacement du certificat sur votre machine. L’emplacement peut varier en fonction de l’endroit où vous avez installé Python. [pip](https://pip.pypa.io) et le module [certifi](https://pypi.org/project/certifi/) doivent être installés. Vous pouvez utiliser la commande Python suivante depuis l’invite de commandes bash :

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    Notez l’emplacement du certificat, par exemple, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Votre chemin d’accès spécifique dépend de votre système d’exploitation et de la version de Python que vous avez installée.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Définissez le chemin d’accès pour un ordinateur de développement à l’intérieur du cloud

Si vous exécutez l’interface CLI sur une machine Linux créée dans l’environnement Azure Stack, exécutez la commande bash suivante avec le chemin d’accès à votre certificat.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Définissez le chemin d’accès pour un ordinateur de développement à l’extérieur du cloud

Si vous exécutez l’interface CLI sur une machine située à l’extérieur de l’environnement Azure Stack :  

1. Configurez la [connectivité VPN à Azure Stack](azure-stack-connect-azure-stack.md).
1. Copiez le certificat PEM que vous avez reçu de l’opérateur Azure Stack, et notez l’emplacement du fichier (PATH_TO_PEM_FILE).
1. Exécutez les commandes dans les sections suivantes, selon le système d’exploitation de votre station de travail de développement.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a> Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting required information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Obtenir le point de terminaison des alias de machines virtuelles

Avant de pouvoir créer des machines virtuelles à l’aide de CLI, vous devez contacter l’opérateur Azure Stack et obtenir l’URI du point de terminaison des alias de machines virtuelles. Par exemple, Azure utilise l’URI suivante : `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`. L’administrateur de cloud doit configurer un point de terminaison similaire pour Azure Stack avec les images disponibles dans la Place de marché Azure Stack. Vous devez passer l’URI du point de terminaison au paramètre `endpoint-vm-image-alias-doc` à la commande `az cloud register`, comme indiqué dans la section suivante. 
  
## <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

Suivez les étapes ci-dessous pour vous connecter à Azure Stack :

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`.
   
    a. Pour inscrire l’environnement *administrateur de cloud*, utilisez :

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    b. Pour inscrire l’environnement *utilisateur*, utilisez :

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. Pour inscrire l’*utilisateur* dans un environnement mutualisé, utilisez :

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    d. Pour inscrire l’utilisateur dans un environnement AD FS, utilisez :

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
      ```
1. Définissez l’environnement actif avec les commandes suivantes.
   
    a. Pour l’environnement *administrateur de cloud*, utilisez :

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

    b. Pour l’environnement *utilisateur*, utilisez :

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

    ```azurecli
    az cloud update \
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Si vous exécutez une version d’Azure Stack antérieure à la build 1808, vous devez utiliser le profil de version API **2017-03-09-profile** plutôt que le profil de version API **2018-03-01-hybrid**.

1. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Environnements Azure AD
      * Connectez-vous en tant qu’*utilisateur* : Vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login`, ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte :

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Si l’authentification multifacteur est activée sur votre compte d’utilisateur, vous pouvez utiliser la commande `az login command` sans fournir le paramètre `-u`. L’exécution de cette commande vous donne une URL et un code à utiliser pour vous authentifier.
   
      * Connectez-vous en tant que *principal de service* : avant de vous connecter, [créez un principal de service avec le portail Azure](azure-stack-create-service-principals.md) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * Environnements AD FS

        * Connectez-vous en tant qu’utilisateur à l’aide d’un navigateur web :  
              ```azurecli  
              az login
              ```
        * Connectez-vous en tant qu’utilisateur à l’aide d’un navigateur web avec un code d’appareil :  
              ```azurecli  
              az login --use-device-code
              ```
        > [!Note]  
        >Elle vous donne une URL et un code qui vous permettent de vous authentifier.

        * Connectez-vous en tant que principal de service :
        
          1. Préparez le fichier .pem à utiliser pour la connexion du principal de service.

            * Sur l’ordinateur client où le principal a été créé, exportez le certificat du principal de service au format pfx avec la clé privée située sous `cert:\CurrentUser\My`. Le certificat porte le même nom que le principal.
        
            * Convertissez le fichier pfx au format pem (avec l’utilitaire OpenSSL).

          2.  Connectez-vous à l’interface CLI :
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>Tester la connectivité

Quand tout est configuré, utilisez CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problèmes connus

Il existe des problèmes connus liés à l’utilisation de CLI dans Azure Stack :

 - Le mode interactif CLI ; par exemple, la commande `az interactive` n’est pas encore prise en charge dans Azure Stack.
 - Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack, utilisez la commande `az vm image list --all` au lieu de la commande `az vm image list`. La spécification de l’option `--all` garantit que la réponse retourne uniquement les images disponibles dans votre environnement Azure Stack.
 - Les alias d’images de machines virtuelles disponibles dans Azure ne s’appliquent pas forcément à Azure Stack. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
- [Activer Azure CLI pour les utilisateurs d’Azure Stack (opérateur)](../azure-stack-cli-admin.md)
- [Gérer les autorisations utilisateur](azure-stack-manage-permissions.md) 