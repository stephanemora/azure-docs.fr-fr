---
title: Cesser d’utiliser l’extension de machine virtuelle identité gérée et commencer à utiliser le point de terminaison de Service de métadonnées Instance Azure
description: Procédure pas à obtenir des instructions détaillées à l’aide de l’extension de machine virtuelle, démarrez à l’aide de Azure Instance Metadata Service (IMDS) pour l’authentification.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: priyamo
ms.openlocfilehash: c29eb22f2606fc44cfd7d948f2c363cb51c811dd
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227614"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Procédure d’arrêt à l’aide de la machine virtuelle managed extension des identités et commencer à utiliser le Service de métadonnées Instance Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Extension de machine virtuelle pour les entités gérées

L’extension de machine virtuelle pour les entités gérées est utilisée pour demander des jetons pour une identité gérée au sein de la machine virtuelle. Le flux de travail se compose des étapes suivantes :

1. Tout d’abord, la charge de travail au sein de la ressource appelle le point de terminaison local `http://localhost/oauth2/token` pour demander un jeton d’accès.
2. L’extension de machine virtuelle utilise ensuite les informations d’identification pour l’identité gérée, pour demander un jeton d’accès d’Azure AD... 
3. Le jeton d’accès est retourné à l’appelant et peut être utilisé pour s’authentifier auprès des services qui prennent en charge d’Azure AD authentication, comme Azure Key Vault ou de stockage Azure.

Dû à plusieurs limitations décrites dans la section suivante, l’extension de machine virtuelle identité gérée a été déconseillée en faveur de l’aide du point de terminaison équivalent dans Azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Configurer l’extension 

Lorsque vous configurez un ordinateur virtuel ou les machines virtuelles identiques pour avoir une identité gérée, vous pouvez facultatif choisissez, vous pouvez éventuellement choisir d’approvisionner des identités gérées pour l’extension de machine virtuelle de ressources Azure à l’aide du `-Type` paramètre sur le [ Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) applet de commande. Vous pouvez passer soit `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, selon le type de machine virtuelle et nommez-le en utilisant le `-Name` paramètre. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Vous pouvez également utiliser le modèle de déploiement Azure Resource Manager pour configurer l’extension de machine virtuelle, en ajoutant le code JSON suivant à la `resources` section au modèle (utilisez `ManagedIdentityExtensionForLinux` pour les éléments nom et le type de la version de Linux).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Si vous travaillez avec des machines virtuelles identiques, vous pouvez également configurer des identités gérées pour les ressources Azure machine virtuelle identique à l’aide de l’extension la [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) applet de commande. Vous pouvez passer soit `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, selon le type de mise à l’échelle de machine virtuelle définie et nommez-le en utilisant le `-Name` paramètre. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Pour approvisionner des machines virtuelles identiques, définissez l’extension avec le modèle de déploiement Azure Resource Manager, ajoutez le code JSON suivant à la `extensionpProfile` section au modèle (utilisez `ManagedIdentityExtensionForLinux` pour les éléments nom et le type de la version de Linux).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Approvisionnement de l’extension de machine virtuelle peut échouer en raison d’échecs de recherche DNS. Si cela se produit, redémarrez la machine virtuelle, puis réessayez. 

### <a name="remove-the-extension"></a>Supprimer l’extension 
Pour supprimer l’extension, utilisez `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` basculer (selon le type de machine virtuelle) avec [delete de az vm extension](https://docs.microsoft.com/cli/azure/vm/), ou [delete de az vmss extension](https://docs.microsoft.com/cli/azure/vmss) de machines virtuelles identiques définit à l’aide d’Azure CLI, ou `Remove-AzVMExtension` pour Powershell :

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Acquérir un jeton à l’aide de l’extension de machine virtuelle

Voici un exemple de demande à l’aide des identités gérées pour les ressources Azure VM Extension Endpoint :

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Élément | Description |
| ------- | ----------- |
| `GET` | Le verbe HTTP, indiquant votre souhait de récupérer des données du point de terminaison. Dans ce cas, un jeton d’accès OAuth. | 
| `http://localhost:50342/oauth2/token` | Le point de terminaison d’identités managées pour ressources Azure, où 50342 est le port par défaut configurable. |
| `resource` | Un paramètre de chaîne de requête, indiquant l’URI ID d’application de la ressource cible. Il apparaît également dans la revendication `aud` (audience) du jeton émis. Cet exemple demande un jeton pour accéder à Azure Resource Manager, qui possède un URI ID d’application, https://management.azure.com/. |
| `Metadata` | Un champ d’en-tête de requête HTTP, requis par les identités managées pour ressources Azure afin d’atténuer une attaque par falsification de requête côté serveur (SSRF). Cette valeur doit être définie sur « true », en minuscules.|
| `object_id` | (Facultatif) Un paramètre de chaîne de requête, indiquant l’élément object_id de l’identité managée pour laquelle vous souhaitez obtenir le jeton. Obligatoire, si votre machine virtuelle possède plusieurs identités managées affectées par l’utilisateur.|
| `client_id` | (Facultatif) Un paramètre de chaîne de requête, indiquant l’élément client_id de l’identité managée pour laquelle vous souhaitez obtenir le jeton. Obligatoire, si votre machine virtuelle possède plusieurs identités managées affectées par l’utilisateur.|


Exemple de réponse :

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Élément | Description |
| ------- | ----------- |
| `access_token` | Le jeton d’accès demandé. Lors de l’appel d’une API REST sécurisée, le jeton est incorporé dans le champ d’en-tête de requête `Authorization` comme un jeton « du porteur », autorisant l’API à authentifier l’appelant. | 
| `refresh_token` | Non utilisé par les identités managées pour ressources Azure. |
| `expires_in` | Le nombre de secondes pendant lesquelles le jeton d’accès est toujours valide, avant d’expirer, à partir de son émission. L’heure d’émission est accessible dans la revendication `iat` du jeton. |
| `expires_on` | L’intervalle de temps lorsque le jeton d’accès expire. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `exp` du jeton). |
| `not_before` | L’intervalle de temps pendant lequel le jeton d’accès prend effet, et peut être accepté. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `nbf` du jeton). |
| `resource` | La ressource pour laquelle le jeton d’accès a été demandé, correspondant au paramètre de chaîne de requête `resource` de la requête. |
| `token_type` | Le type de jeton, qui est un jeton d’accès « du porteur », ce qui signifie que la ressource peut donner l’accès au porteur de ce jeton. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Résoudre les problèmes de l’extension de machine virtuelle 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Redémarrez l’extension de machine virtuelle après un échec

Sur Windows et certaines versions de Linux, si l’extension s’arrête, la cmdlet suivante peut être utilisée pour redémarrer manuellement l’extension :

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Où : 
- Nom de l’extension type et pour Windows est : `ManagedIdentityExtensionForWindows`
- Nom de l’extension et type pour Linux est : `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Le « Script d’automatisation » échoue lors de la tentative d’exportation de schéma pour l’extension des entités managées pour ressources Azure

Lorsque des identités gérées pour les ressources Azure est activé sur une machine virtuelle, l’erreur suivante s’affiche lorsque vous tentez d’utiliser la fonctionnalité « Script d’automatisation » pour la machine virtuelle ou son groupe de ressources :

![Erreur d’exportation de script d’automatisation d’identités managées pour ressources Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Actuellement, les identités gérées pour l’extension de machine virtuelle de ressources Azure ne prend pas en charge la possibilité d’exporter son schéma à un modèle de groupe de ressources. Par conséquent, le modèle généré n’affiche pas les paramètres de configuration permettant d’activer des identités managées pour ressources Azure sur la ressource. Ces sections peuvent être ajoutées manuellement en suivant les exemples de [configurer managed identités pour les ressources Azure sur une machine virtuelle Azure à l’aide de des modèles](qs-configure-template-windows-vm.md).

Lorsque la fonctionnalité d’exportation de schéma est disponible pour les identités gérées pour l’extension de machine virtuelle de ressources Azure (planifié pour l’abandon de janvier 2019), il sera répertorié dans [groupes de ressources exportation qui contiennent des extensions de machine virtuelle ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitations de l’extension de machine virtuelle 

Il existe plusieurs limitations principales à l’aide de l’extension de machine virtuelle. 

 * La limitation plus important est le fait que les informations d’identification utilisées pour demander des jetons sont stockées sur l’ordinateur virtuel. Un attaquant qui a été atteinte à la machine virtuelle peut exfiltrer les informations d’identification. 
 * En outre, l’extension de machine virtuelle est toujours pris en charge par plusieurs distributions Linux, avec un coût pour le modifier, générer et tester l’extension sur chacun de ces distributions de développement énorme. Actuellement, seuls les distributions Linux suivantes sont prises en charge : 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Il existe un impact sur les performances pour le déploiement de machines virtuelles avec des identités gérées, comme l’extension de machine virtuelle doit également être configuré. 
 * Enfin, l’extension de machine virtuelle peut prendre uniquement en charge l’avoir 32 affectée à l’utilisateur des identités gérées par machine virtuelle. 

## <a name="azure-instance-metadata-service"></a>Service de métadonnées d’instance Azure

Le [Azure Instance Metadata Service (IMDS)](https://docs.microsoft.com/azure/virtual-machines/instance-metadata-service) est un point de terminaison REST qui fournit des informations sur les instances de machine virtuelle qui peuvent être utilisées pour gérer et configurer vos machines virtuelles en cours d’exécution. Le point de terminaison est disponible sur une adresse IP non routable bien connue (`169.254.169.254`) qui est accessible uniquement à partir de la machine virtuelle.

Il existe plusieurs avantages à l’utilisation d’Azure IMDS pour demander des jetons. 

1. Le service est externe à la machine virtuelle, par conséquent, les informations d’identification utilisées par des identités gérées ne sont plus présentes sur la machine virtuelle. Au lieu de cela, elles sont hébergées et sécurisés sur l’ordinateur hôte de la machine virtuelle Azure.   
2. Tous les systèmes d’exploitation Windows et Linux pris en charge sur Azure IaaS peuvent utiliser des identités gérées.
3. Déploiement est plus rapide et plus facile, étant donné que l’extension de machine virtuelle n’a plus besoin être mis en service.
4. Avec le IMDS point de terminaison jusqu'à 1 000 identités gérées affectée à l’utilisateur peut être affectée à une seule machine virtuelle.
5. Il n’existe pas de changement significatif pour les demandes à l’aide de IMDS par opposition à ceux qui utilisent l’extension de machine virtuelle, par conséquent il est relativement simple à un port sur les déploiements existants qui utilisent actuellement l’extension de machine virtuelle.

Pour ces raisons, le service Azure IMDS sera la façon de facto pour demander des jetons, une fois que l’extension de machine virtuelle est déconseillée. 


## <a name="next-steps"></a>Étapes suivantes

* [Comment utiliser des identités gérées pour les ressources Azure sur une machine virtuelle Azure pour acquérir un jeton d’accès](how-to-use-vm-token.md)
* [Service de métadonnées Instance Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
