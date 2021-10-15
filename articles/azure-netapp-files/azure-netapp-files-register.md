---
title: Inscrire le fournisseur de ressources NetApp à utiliser avec Azure NetApp Files | Microsoft Docs
description: Découvrez comment inscrire le fournisseur de ressources NetApp pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: b8b70a06a6bda9757f9019a6f753af226235a985
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407738"
---
# <a name="register-for-netapp-resource-provider"></a>S’inscrire au fournisseur de ressources NetApp

Pour utiliser le service Azure NetApp Files, vous devez inscrire le fournisseur de ressources NetApp.

1. Dans le portail Azure, cliquez sur l’icône d’Azure Cloud Shell située dans le coin supérieur droit :

      ![Icône d’Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Si vous disposez de plusieurs abonnements sur votre compte Azure, sélectionnez celui qui a été approuvé pour Azure NetApp Files :
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. Dans la console Azure Cloud Shell, entrez la commande suivante pour vérifier que votre abonnement a été approuvé :
    
    ```azurecli
    az feature list | grep NetApp
    ```

   La sortie de la commande se présente comme suit :
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` est votre ID d’abonnement.



4. Dans la console Azure Cloud Shell, entrez la commande suivante pour inscrire le fournisseur de ressources Azure : 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Le paramètre `--wait` indique à la console d’attendre que l’inscription soit terminée. Le processus d’inscription peut prendre du temps.

5. Dans la console Azure Cloud Shell, entrez la commande suivante pour vérifier que le fournisseur de ressources Azure a été inscrit : 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   La sortie de la commande se présente comme suit :
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` est votre ID d’abonnement.  La valeur du paramètre `state` indique `Registered`.

6. Dans le portail Azure, cliquez sur le panneau **Abonnements**.
7. Dans le panneau Abonnements, cliquez sur l’ID de votre abonnement. 
8. Dans les paramètres de l’abonnement, cliquez sur **Fournisseurs de ressources** pour vérifier que le fournisseur Microsoft.NetApp indique l’état Inscrit : 

      ![Microsoft.NetApp inscrit](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte NetApp](azure-netapp-files-create-netapp-account.md)
* [Créer une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
