---
title: Ajouter des nœuds d’échelle dans Azure Stack | Microsoft Docs
description: Ajouter des nœuds pour mettre à l’échelle les unités dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: thoroet
ms.openlocfilehash: 15c46bb0fee4689b614636d8c9e01e36f7f0be3d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227687"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Ajouter de nouveaux nœuds d’unité d’échelle dans Azure Stack

Les opérateurs Azure Stack peuvent augmenter la capacité globale d’une unité d’échelle existante en ajoutant un nouveau calculateur physique. Le calculateur physique s’appelle aussi un nœud d’unité d’échelle. Chaque nouveau nœud d’unité d’échelle que vous ajoutez doit être homogène en termes de type de CPU, de mémoire, de numéro de disque et de taille avec les nœuds déjà présents dans l’unité d’échelle.  

> [!NOTE]  
Pour ajouter de nouveaux nœuds d’unités d’échelle, vous devez exécuter Azure Stack 1807 ou une version ultérieure.

Pour ajouter un nœud d’unité d’échelle, vous devez intervenir dans Azure Stack et exécuter les outils de votre fabricant OEM. Les outils du fabricant OEM s’exécutent sur l’hôte du cycle de vie matériel (HLH) pour s’assurer que le nouveau calculateur physique correspond au même niveau de microprogramme que les nœuds existants.

Le diagramme de flux suivant montre le processus général pour ajouter un nœud d’unité d’échelle.

![Flux d’ajout d’unités d’échelle](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *Votre contrat de support indique si votre fournisseur OEM doit choisir l’emplacement physique du rack du serveur et mettre à jour le microprogramme.*

L’opération d’ajout de nouveau nœud peut prendre plusieurs heures ou plusieurs jours.

## <a name="add-scale-unit-nodes"></a>Ajouter des nœuds d’unités d’échelle 
Les étapes suivantes présentent de façon générale la procédure à suivre pour ajouter un nœud. N’effectuez pas ces étapes sans avoir consulté au préalable la documentation concernant les extensions de capacité fournie par le fabricant OEM.

1. Placez le nouveau serveur physique dans le rack et câblez-le de manière appropriée. 
2. Configurez l’adresse IP appropriée dans le contrôleur de gestion de la carte de base (BMC) et appliquez tous les paramètres du BIOS conformément à la documentation fournie par le fabricant OEM.
3. Appliquez la ligne de base actuelle du microprogramme à tous les composants en utilisant les outils fournis par le fabricant du matériel fonctionnant sur l’HLH.
4. Exécutez l’opération Ajouter un nœud dans le portail d’administration Azure Stack.
5. Vérifiez que l’opération d’ajout de nœud a réussi. Pour ce faire, vérifiez l’[ **état** de l’unité d’échelle](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Ajouter le nœud 
Pour ajouter de nouveaux nœuds, vous pouvez vous servir de la console d’administration ou de PowerShell. L’opération Ajouter un nœud commence par ajouter le nouveau nœud d’unité d’échelle en tant que capacité de calcul disponible, puis augmente automatiquement la capacité de stockage. La capacité augmente automatiquement, car Azure Stack est un système hyperconvergé dans lequel le *calcul* et le *stockage* se mettent à l’échelle simultanément.

### <a name="use-the-admin-console"></a>Utiliser la console d’administration
1. Connectez-vous au portail d’administration d’Azure Stack en tant qu’opérateur Azure Stack.
2. Accédez à **Gestion des régions** > **Unités d’échelle**, puis sélectionnez l’unité d’échelle dont vous souhaitez augmenter la capacité.  
   ![Sélectionnez l’unité d’échelle](media/azure-stack-add-scale-node/select-node1.png)

   ![Afficher les détails de l’unité d’échelle](media/azure-stack-add-scale-node/select-node2.png)
 
3. Les paramètres suivants sont requis :  
   - **ADRESSE IP BMC** pour chaque nouveau nœud d’unité d’échelle. (une seule adresse IP par ligne) ![Ajouter un nœud](media/azure-stack-add-scale-node/add-node.png)

### <a name="use-powershell"></a>Utiliser PowerShell

Utilisez la cmdlet **New-AzsScaleUnitNodeObject** pour ajouter un ou plusieurs nœuds.  

Avant d’utiliser l’un des exemples de scripts PowerShell suivants, remplacez les valeurs *node names* et *IP addresses* par les valeurs de votre environnement Azure Stack.

**Pour un seul nœud :**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

**Ajouter plusieurs noeuds :**
  ```powershell
  ## Add multiple nodes 
  $NewNode1=New-AzsScaleUnitNodeObject -computername "<name_of_new_node01>"  -BMCIPv4Address "<BMCIP_address_of_new_node01>" 
 
  $NewNode2=New-AzsScaleUnitNodeObject -computername "<name_of_new_node02>" -BMCIPv4Address “<BMCIP_address_of_new_node02>”$ 
 
  Add-AzsScaleUnitNode -NodeList @($NewNode1,$NewNode2) -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```

## <a name="monitor-add-node-operations"></a>Surveiller les opérations Ajouter un nœud 
Vous pouvez vous servir du portail d’administration ou PowerShell pour connaître l’état de l’opération Ajouter un nœud. Les opérations Ajouter un nœud peuvent prendre de plusieurs heures à plusieurs jours.

### <a name="use-the-admin-console"></a>Utiliser la console d’administration 
Pour surveiller l’ajout d’un nouveau nœud, vous pouvez examiner l’unité d’échelle ou les objets de nœud d’unité d’échelle dans le portail d’administration. Pour ce faire, accédez à **Gestion des régions** > **Unités d’échelle**. Ensuite, sélectionnez l’unité d’échelle ou le nœud d’unité d’échelle que vous souhaitez examiner. 

### <a name="use-powershell"></a>Utiliser PowerShell
L’état de l’unité d’échelle et des nœuds de l’unité d’échelle peut être récupéré à l’aide de PowerShell comme suit :
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>État de l’opération Ajouter un nœud 
**Pour une unité d’échelle :**
|Statut               |Description  |
|---------------------|---------|
|Exécution              |Tous les nœuds participent de façon active à l’unité d’échelle.|
|Arrêté              |Le nœud de l’unité d’échelle est désactivé ou inaccessible.|
|Extension            |Un ou plusieurs nœuds d’unité d’échelle sont actuellement ajoutés en tant que capacité de calcul.|
|Configuration du stockage  |La capacité de calcul a été augmentée et la configuration du stockage est en cours d’exécution.|
|Correction nécessaire |Une erreur nécessitant la réparation d’un ou de plusieurs nœuds de l’unité d’échelle a été détectée.|


**Pour un nœud de l’unité d’échelle :**
|Statut                |Description  |
|----------------------|---------|
|Exécution               |Le nœud participe de façon active à l’unité d’échelle.|
|Arrêté               |Le nœud est indisponible.|
|Ajout                |Le nœud est ajouté de façon active à l’unité d’échelle.|
|Réparation             |Le nœud est réparé de façon active.|
|Maintenance            |Le nœud est suspendu et aucune charge de travail utilisateur active n’est en cours d’exécution. |
|Correction nécessaire  |Une erreur nécessitant la réparation du nœud a été détectée.|


## <a name="troubleshooting"></a>Résolution de problèmes
Voici les problèmes couramment rencontrés lors de l’ajout d’un nœud. 

**Scénario 1 :** L’opération d’ajout de nœud d’unité d’échelle échoue, mais un ou plusieurs nœuds sont répertoriés dans la liste avec l’état Arrêté.  
- Correction : Exécutez l’opération de réparation afin de réparer un ou plusieurs nœuds. Il n’est possible d’effectuer qu’une seule opération de réparation à la fois.

**Scénario 2 :** Un ou plusieurs nœuds d’unité d’échelle ont été ajoutés, mais l’extension de stockage a échoué. Dans ce scénario, l’objet du nœud de l’unité d’échelle rapporte un état de fonctionnement, mais la tâche de configuration du stockage n’est pas lancée.  
- Correction : Utilisez le point de terminaison privilégié pour examiner l’intégrité du stockage en exécutant la cmdlet PowerShell suivante :
 
**Scénario 3 :** Vous recevez une alerte indiquant que la tâche d’échelle du stockage a échoué.  
- Correction : Dans ce cas, la tâche de configuration du stockage a échoué. Ce problème nécessite que vous contactiez le support technique.


## <a name="next-steps"></a>Étapes suivantes 
Examiner les [Actions de nœud](azure-stack-node-actions.md) 
