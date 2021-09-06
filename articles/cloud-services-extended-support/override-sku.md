---
title: Remplacer les informations de référence (SKU) de CSCFG/CSDEF pour Azure Cloud Services (support étendu)
description: Cet article explique comment remplacer les informations de référence SKU dans les fichiers .cscfg et .csdef pour Azure cloud Services (support étendu).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31ff47cd4e110e62769678836bdd803b71369e0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437876"
---
# <a name="override-sku-settings-in-cscfg-and-csdef-files-for-cloud-services-extended-support"></a>Remplacer les paramètres de référence SKU dans les fichiers .cscfg et .csdef pour Cloud Services (support étendu)

Cet article décrit comment mettre à jour la taille et le nombre d’instances du rôle dans Azure Cloud Services à l’aide de la propriété **allowModelOverride**. Lorsque vous utilisez cette propriété, vous n’avez pas besoin de mettre à jour les fichiers de configuration du service (.cscfg) et de définition du service (.csdef). Vous pouvez ainsi mettre à l’échelle le service cloud vers le haut, vers le bas, vers l’intérieur ou vers l’extérieur sans avoir à le repackager et à le redéployer.

## <a name="set-the-allowmodeloverride-property"></a>Définir la propriété allowModelOverride
Vous pouvez définir la propriété **allowModelOverride** sur `true` ou `false`. 
* Quand **allowModelOverride** est sur `true`, un appel d’API met à jour la taille et le nombre d’instances du rôle pour le service cloud sans valider les valeurs avec les fichiers .csdef et .cscfg. 
   > [!Note]
   > Le fichier .cscfg est mis à jour pour refléter le nombre d’instances du rôle. Le fichier. csdef (incorporé dans le fichier. cspkg) conserve les anciennes valeurs.

* Quand **allowModelOverride** est sur `false`, un appel d’API génère une erreur si les valeurs de taille et de nombre d’instances du rôle ne correspondent pas respectivement aux fichiers .csdef et .cscfg.

La valeur par défaut est `false`. Si la propriété est rétablie sur `false` après avoir été définie sur `true`, les fichiers .csdef et .cscfg sont à nouveau validés.

Les exemples suivants montrent comment définir la propriété **allowModelOverride** à l’aide d’un modèle Azure Resource Manager (ARM), de PowerShell ou du SDK.

### <a name="arm-template"></a>Modèle ARM
Ici, la définition de la propriété **allowModelOverride** sur `true` a pour effet de mettre à jour le service cloud avec les propriétés de rôle définies dans la section `roleProfile` :
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
La définition du commutateur `AllowModelOverride` sur la nouvelle cmdlet `New-AzCloudService` a pour effet de mettre à jour le service cloud avec les propriétés de référence SKU définies dans le profil de rôle :
```powershell
New-AzCloudService ` 
-Name "ContosoCS" ` 
-ResourceGroupName "ContosOrg" ` 
-Location "East US" `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>Kit SDK
La définition de la variable `AllowModelOverride` sur `true` a pour effet de mettre à jour le service cloud avec les propriétés de référence SKU définies dans le profil de rôle :

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate("ContosOrg", "ContosoCS", cloudService);
```
### <a name="azure-portal"></a>Portail Azure
La Portail Azure ne vous permet pas d’utiliser la propriété **allowModelOverride** pour remplacer la taille et le nombre d’instances du rôle dans les fichiers .csdef et .cscfg. 


## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis de déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez les [questions fréquentes](faq.yml) de Cloud Services (support étendu).
