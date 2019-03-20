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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f6e604940c9e2e84f119fdd1859ad4b2cda23aef
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588701"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Spécifier un groupe de ressources pour les machines virtuelles de labo dans Azure DevTest Labs

En tant que propriétaire de labo, vous pouvez configurer les machines virtuelles de votre labo de manière à ce qu’elles soient créées dans un groupe de ressources en particulier. Cette fonctionnalité vous aide dans les scénarios suivants :

- Vous disposez de moins de groupes de ressources créés par les laboratoires dans votre abonnement.
- Avoir vos laboratoires fonctionnent au sein d’un ensemble fixe de groupes de ressources que vous configurez.
- Vous contournez les restrictions et les approbations requises pour créer des groupes de ressources au sein de votre abonnement Azure.
- Consolider toutes vos ressources de laboratoire au sein d’un seul groupe de ressources pour simplifier le suivi de ces ressources et l’application [stratégies](../governance/policy/overview.md) pour gérer les ressources au niveau du groupe de ressources.

Avec cette fonctionnalité, vous pouvez utiliser un script pour spécifier un groupe de ressources nouveau ou existant au sein de votre abonnement Azure pour toutes vos machines virtuelles de laboratoire. Actuellement, Azure DevTest Labs prend en charge cette fonctionnalité via une API.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Suivez ces étapes pour spécifier un groupe de ressources pour toutes les machines virtuelles créées dans le laboratoire. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **tous les Services** dans le menu de navigation gauche. 
3. Sélectionnez **dev/test** dans la liste.
4. Dans la liste des laboratoires, sélectionnez votre **lab**.  
5. Sélectionnez **Configuration et stratégies** dans le **paramètres** section dans le menu de gauche. 
6. Sélectionnez **les paramètres de Lab** sur le menu de gauche. 
7. Sélectionnez **toutes les machines virtuelles dans un groupe de ressources**. 
8. Sélectionnez un groupe de ressources existant dans la liste déroulante liste (ou) sélectionnez **créer**, entrez un **nom** pour le groupe de ressources, puis sélectionnez **OK**. 

    ![Sélectionnez le groupe de ressources pour toutes les machines virtuelles de laboratoire](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Utiliser PowerShell 
L’exemple suivant montre comment utiliser un script PowerShell pour créer toutes les machines virtuelles de laboratoire dans un groupe de ressources.

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

Appeler le script à l’aide de la commande suivante. ResourceGroup.ps1 est le fichier qui contient le script précédent :

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Utiliser un modèle Azure Resource Manager
Si vous utilisez un modèle Azure Resource Manager pour créer un laboratoire, utiliser le **vmCreationResourceGroupId** propriété dans la section Propriétés de laboratoire de votre modèle, comme illustré dans l’exemple suivant :

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API pour configurer un groupe de ressources pour les machines virtuelles de laboratoire
Vous disposez des options suivantes en tant que propriétaire d’un laboratoire lors de l’utilisation de cette API :

- Choisissez le **groupe de ressources du laboratoire** pour toutes les machines virtuelles.
- Choisissez un **groupe de ressources existant** autre que le groupe de ressources du laboratoire pour toutes les machines virtuelles.
- Entrez un **nouveau groupe de ressources** nom pour toutes les machines virtuelles.
- Continuer à utiliser le comportement existant, dans lequel un groupe de ressources est créé pour chaque machine virtuelle dans le laboratoire.
 
Ce paramètre s’applique aux nouvelles machines virtuelles créées dans le labo. Les anciennes machines virtuelles dans votre laboratoire qui ont été créées dans leurs propres groupes de ressources seront pas touchés. Les environnements sont créés dans votre laboratoire continuent de leurs propres groupes de ressources.

Comment utiliser cette API :
- Utilisez API version **2018_10_15_preview**.
- Si vous spécifiez un nouveau groupe de ressources, assurez-vous d’avoir **autorisations en écriture sur les groupes de ressources** dans votre abonnement. Si vous ne disposez pas des autorisations en écriture, la création d’ordinateurs virtuels dans le groupe de ressources spécifié échoue.
- Lors de l’utilisation de l’API, passez l’**ID complet du groupe de ressources**. Par exemple : `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Assurez-vous que le groupe de ressources est dans le même abonnement que le laboratoire. 


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- [Définir des stratégies pour un laboratoire](devtest-lab-get-started-with-lab-policies.md)
- [Forum Aux Questions](devtest-lab-faq.md)
