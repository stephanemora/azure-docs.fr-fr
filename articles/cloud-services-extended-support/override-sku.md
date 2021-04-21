---
title: Remplacer les informations de référence (SKU) de CSCFG/CSDEF pour Azure Cloud Services (support étendu)
description: Remplacer les informations de référence (SKU) de CSCFG/CSDEF pour Azure Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739257"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Remplacer les informations de référence (SKU) de CSCFG/CSDEF dans Azure Cloud Services (support étendu) 

Cette fonctionnalité permet à l’utilisateur de mettre à jour la taille et le nombre d’instances du rôle dans son service cloud à l’aide de la propriété **allowModelOverride** sans avoir à mettre à jour les fichiers de configuration et de définition de service, ce qui permet au service cloud de d’adapter son échelle sans devoir repackager et redéployer.

## <a name="set-allowmodeloverride-property"></a>Définir la propriété allowModelOverride
Vous pouvez définir la propriété allowModelOverride comme suit :
* Quand allowModelOverride = true, l’appel d’API met à jour la taille et le nombre d’instances du rôle pour le service cloud sans valider les valeurs avec les fichiers csdef et cscfg. 
> [!Note]
> Le fichier cscfg est mis à jour pour refléter le nombre d’instances du rôle, mais le fichier csdef (dans le cspkg) conserve les anciennes valeurs
* Quand allowModelOverride = false, l’appel d’API génère une erreur quand les valeurs de taille et de nombre d’instances du rôle ne correspondent respectivement aux fichiers csdef et cscfg

Par défaut, la valeur définie est false. Si la propriété est réinitialisée de true à false, les fichiers csdef et cscfg sont de nouveau vérifiés pour validation.

Parcourez les exemples ci-dessous pour appliquer la propriété dans PowerShell, un modèle et Kit de développement logiciel (SDK)

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
La définition de la propriété « allowModelOverride » sur true a pour effet de mettre à jour le service cloud avec les propriétés de rôle définies dans la section roleProfile
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
Le réglage du commutateur « AllowModelOverride » sur la cmdlet New-AzCloudService a pour effet de mettre à jour le service cloud avec les propriétés de référence (SKU) définies dans le RoleProfile
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
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
La définition de la variable AllowModelOverride = true a pour effet de mettre à jour le service cloud avec les propriétés de référence (SKU) définies dans le RoleProfile

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
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Portail Azure
Le portail ne permet pas à la propriété ci-dessus de remplacer la taille et le nombre d’instances du rôle dans les fichiers csdef et cscfg. 


## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
