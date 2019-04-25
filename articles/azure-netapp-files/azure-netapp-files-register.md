---
title: S’inscrire à Azure NetApp Files | Microsoft Docs
description: Décrit comment envoyer une requête d’inscription au service Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452765"
---
# <a name="register-for-azure-netapp-files"></a>S’inscrire à Azure NetApp Files
Avant d’utiliser Azure NetApp Files, vous devez envoyer une requête d’inscription au service Azure NetApp Files.  Après l’inscription, vous vous inscrivez pour utiliser le service.

## <a name="request-to-enroll-in-the-service"></a>Requête d’inscription au service
Vous devez faire partie du programme Préversion publique et figurer dans la liste verte pour l’accès au fournisseur de ressources Azure Microsoft.NetApp. Pour plus d’informations sur l’adhésion au programme Préversion publique, consultez la [page d’inscription au programme Préversion publique d’Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Inscrire le fournisseur de ressources NetApp

Pour utiliser le service, vous devez inscrire le fournisseur de ressources Azure pour Azure NetApp Files. 

1. Dans le portail Azure, cliquez sur l’icône d’Azure Cloud Shell située dans le coin supérieur droit :

      ![Icône d’Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Si vous avez plusieurs abonnements sur votre compte Azure, sélectionnez celui qui a été mis en liste verte pour Azure NetApp Files :
    
        az account set --subscription <subscriptionId>

3. Dans la console Azure Cloud Shell, entrez la commande suivante pour vérifier que votre abonnement a été mis en liste verte :
    
        az feature list | grep NetApp

   La sortie de la commande se présente comme suit :
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` est votre ID d’abonnement.

4. Dans la console Azure Cloud Shell, entrez la commande suivante pour inscrire le fournisseur de ressources Azure : 
    
        az provider register --namespace Microsoft.NetApp --wait

   Le paramètre `--wait` indique à la console d’attendre que l’inscription soit terminée. Le processus d’inscription peut prendre du temps.

5. Dans la console Azure Cloud Shell, entrez la commande suivante pour vérifier que le fournisseur de ressources Azure a été inscrit : 
    
        az provider show --namespace Microsoft.NetApp

   La sortie de la commande se présente comme suit :
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` est votre ID d’abonnement.  La valeur du paramètre `state` indique `Registered`.

6. Dans le portail Azure, cliquez sur le panneau **Abonnements**.
7. Dans le panneau Abonnements, cliquez sur l’ID de votre abonnement. 
8. Dans les paramètres de l’abonnement, cliquez sur **Fournisseurs de ressources** pour vérifier que le fournisseur Microsoft.NetApp indique l’état Inscrit : 

      ![Microsoft.NetApp inscrit](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Étapes suivantes  

[Créer un compte NetApp](azure-netapp-files-create-netapp-account.md)