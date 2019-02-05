---
title: "App Service sur Azure Stack : Mise à jour du domaine d'erreur | Microsoft Docs"
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
ms.date: 09/05/2018
ms.author: anwestg
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 249e4b466e9be567111aaa22b40ca3e5dadb6cac
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246379"
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

Pour augmenter la taille des instances des groupes identiques, procédez comme suit :

1. Connectez-vous au portail d’administration d’Azure Stack.
1. Sélectionnez **Tous les services**.
2. Dans la catégorie **COMPUTE**, sélectionnez **Groupes de machines virtuelles identiques**. Les groupes identiques existants déployés dans le cadre du déploiement d’App Service sont listés avec leur nombre d’instances. La capture d’écran suivante présente un exemple de groupes identiques.

      ![Groupes identiques Azure App Service listés dans l’interface utilisateur de Virtual Machine Scale Sets][1]

1. Augmentez la taille des instances pour chaque groupe. Par exemple, si le groupe identique comprend trois instances, vous devez augmenter la taille des instances pour atteindre 6 instances. Ainsi, les trois nouvelles instances sont déployées sur des domaines d’erreur. Les exemples PowerShell suivants montrent comment augmenter la taille des instances du groupe identique.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >L’exécution de cette étape peut prendre plusieurs heures en fonction du type de rôle et du nombre d’instances.

1. Sous **Rôles du portail Administration App Service**, surveillez l’état des nouvelles instances de rôle. Pour vérifier l’état d’une instance de rôle, sélectionnez le type de rôle dans la liste.

    ![Rôles d’Azure App Service sur Azure Stack][2]

1. Lorsque l’état des nouvelles instances de rôle est **Prêt**, revenez à **Groupe de machines virtuelles identiques** et **supprimez** les anciennes instances de rôle.

1. Répétez ces étapes pour **chacun** des groupes de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

* [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
