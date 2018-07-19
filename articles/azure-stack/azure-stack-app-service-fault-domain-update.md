---
title: 'App Service sur Azure Stack : domaines d’erreur | Microsoft Docs'
description: Comment redistribuer Azure App Service sur Azure Stack dans les domaines d’erreur
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130368"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Comment redistribuer Azure App Service sur Azure Stack dans les domaines d’erreur

*S’applique à : systèmes intégrés Azure Stack*

Depuis la mise à jour 1802, Azure Stack prend en charge la distribution des charges de travail sur les domaines d’erreur, ce qui est essentiel à la haute disponibilité.

>[!IMPORTANT]
>Pour bénéficier de la prise en charge des domaines d’erreur, vous devez mettre à jour votre système intégré Azure Stack vers la mise à jour 1802. Ce document s’applique uniquement aux déploiements du fournisseur de ressources App Service qui ont été terminés avant la publication de la mise à jour 1802. Si vous avez déployé App Service sur Azure Stack après avoir appliqué la mise à jour 1802 à Azure Stack, le fournisseur de ressources aura déjà été distribué sur les domaines d’erreur.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Rééquilibrer un fournisseur de ressources App Service sur les domaines d’erreur

Pour redistribuer les groupes identiques déployés pour le fournisseur de ressources App Service, vous devez effectuer les étapes contenues dans cet article pour chacun d’eux. Par défaut, les noms des groupes identiques sont les suivants :

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Si aucune instance n’est déployée dans vos groupes identiques de niveau worker, il n’est pas utile de rééquilibrer ces groupes. Les groupes identiques seront correctement équilibrés lorsque vous effectuerez une montée en charge (scale out).

To scale out the scale sets, follow these steps:

1. Sign in to the Azure Stack Administrator Portal.
2. Sélectionnez **Plus de services**.
3. Under COMPUTE, select <bpt id="p1">**</bpt>Virtual machine scale sets<ept id="p1">**</ept>. Les groupes identiques existants déployés dans le cadre du déploiement d’App Service sont listés avec leur nombre d’instances. The following screen capture shows an example of scale sets.

      ![Groupes identiques Azure App Service listés dans l’interface utilisateur de Virtual Machine Scale Sets][1]

4. Scale out each set. For example, if you have three existing instances in the scale set you must scale out to 6 so the three new instances are deployed across fault domains. The following PowerShell example shows out to scale out the scale set.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >This step can take several of hours to finish, depending on the type of role and the number of instances.

5. In <bpt id="p1">**</bpt>App Service Administration Roles<ept id="p1">**</ept>, monitor the status of the new role instances. To check the status of a role instance, select the role type in the list

    ![Rôles d’Azure App Service sur Azure Stack][2]

6. When the status of the new role instances is <bpt id="p1">**</bpt>Ready<ept id="p1">**</ept>, go back to <bpt id="p2">**</bpt>Virtual Machine Scale Set<ept id="p2">**</ept> and <bpt id="p3">**</bpt>delete<ept id="p3">**</ept> the old role instances.

7. Répétez ces étapes pour **chacun** des groupes de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

* [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
