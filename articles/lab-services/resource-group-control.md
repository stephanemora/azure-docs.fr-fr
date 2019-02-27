---
title: Spécifier un groupe de ressources pour les machines virtuelles dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment spécifier un groupe de ressources pour les machines virtuelles dans Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312972"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Spécifier un groupe de ressources pour les machines virtuelles de labo dans Azure DevTest Labs
En tant que propriétaire de labo, vous pouvez configurer les machines virtuelles de votre labo de manière à ce qu’elles soient créées dans un groupe de ressources en particulier. Cette fonctionnalité vous aide dans les scénarios suivants : 

- Vous disposez de moins de groupes de ressources créés par les laboratoires dans votre abonnement.
- Vos laboratoires fonctionnent au sein d'un ensemble fixe de groupes de ressources configurés par vos soins.
- Vous contournez les restrictions et les approbations requises pour créer des groupes de ressources au sein de votre abonnement Azure.
- Vous consolidez toutes vos ressources de laboratoire au sein d’un seul groupe de ressources pour simplifier le suivi de ces ressources et l’application de [stratégies](../governance/policy/overview.md) afin de les gérer au niveau du groupe de ressources.

Avec cette fonctionnalité, vous pouvez utiliser un script afin de spécifier un nouveau groupe de ressources ou un groupe de ressources existant dans votre abonnement Azure pour toutes les machines virtuelles de votre labo. Pour le moment, DevTest Labs prend en charge cette fonctionnalité par le biais d’une API. 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>API de configuration d’un groupe de ressources pour des machines virtuelles de labo
Nous allons étudier les options dont vous disposez en tant que propriétaire d’un labo lors de l’utilisation de cette API : 

- Vous pouvez choisir le **groupe de ressources du labo** pour toutes les machines virtuelles.
- Vous pouvez choisir un **groupe de ressources existant** différent du groupe de ressources du labo pour toutes les machines virtuelles.
- Vous pouvez entre un nom de **nouveau groupe de ressources** pour toutes les machines virtuelles.
- Vous pouvez utiliser le comportement existant par défaut, dans lequel un groupe de ressources est créé pour chaque machine virtuelle du labo.
 
Ce paramètre s’applique aux nouvelles machines virtuelles créées dans le labo. Les anciennes machines virtuelles de votre labo qui ont été créées dans leurs propres groupes de ressources ne sont pas affectées par ces modifications. Les environnements créés dans votre labo demeurent dans leurs groupes de ressources.

### <a name="how-to-use-this-api"></a>Comment utiliser cette API :
- Utilisez la version **2018_10_15_preview** de cette API. 
- Si vous spécifiez un nouveau groupe de ressources, assurez-vous de disposer des **autorisations d’écriture sur les groupes de ressources** dans votre abonnement. Sans autorisations d’écriture, la création de nouvelles machines virtuelles dans le groupe de ressources spécifié entraîne un échec. 
- Lors de l’utilisation de l’API, passez l’**ID complet du groupe de ressources**. Par exemple : `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Assurez-vous que le groupe de ressources fait partie du même abonnement que le labo. 

## <a name="use-powershell"></a>Utiliser PowerShell 
L’exemple suivant décrit la procédure de création de toutes les machines virtuelles du labo dans un nouveau groupe de ressources à l’aide d’un script PowerShell.

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Appelez le script à l’aide de la commande suivante (ResourceGroup.ps1 est le fichier qui contient le script précédent) : 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Si vous utilisez le modèle Azure Resource Manager pour créer un laboratoire, utilisez la propriété **vmCreationResourceResourceGroupId** dans la section propriétés du labo de votre modèle Resource Manager comme indiqué dans l’exemple suivant :

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Définir des stratégies pour un laboratoire](devtest-lab-get-started-with-lab-policies.md)
- [Forum Aux Questions](devtest-lab-faq.md)
