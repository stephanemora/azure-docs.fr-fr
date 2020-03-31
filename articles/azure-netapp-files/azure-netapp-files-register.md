---
title: S’inscrire à Azure NetApp Files | Microsoft Docs
description: Décrit comment s’inscrire pour utiliser Azure NetApp Files.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79234109"
---
# <a name="register-for-azure-netapp-files"></a>S’inscrire à Azure NetApp Files

> [!IMPORTANT] 
> Avant d'inscrire le fournisseur de ressources Azure NetApp Files, vous devez avoir reçu un e-mail de l’équipe Azure NetApp Files confirmant que vous avez désormais accès au service. 

Dans cet article, vous allez découvrir comment vous inscrire à Azure NetApp Files pour utiliser le service.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Soumettre une requête de liste d’attente pour l’accès au service

1. Soumettez une requête de liste d'attente pour accéder au service Azure NetApp Files via le [page de soumission de liste d’attente Azure NetApp Files](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Une inscription sur la liste d’attente ne garantit pas un accès immédiat au service. 

2. Attendre un e-mail de confirmation officiel de l’équipe Azure NetApp Files avant d'effectuer d'autres tâches. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Inscrire le fournisseur de ressources NetApp

Pour utiliser le service, vous devez inscrire le fournisseur de ressources Azure pour Azure NetApp Files.

> [!NOTE] 
> Vous pouvez vous inscrire au fournisseur de ressources NetApp même sans accès au service. Cela étant, sans autorisation d’accès, toute requête sur le portail Azure ou l'API visant à créer un compte NetApp ou toute autre ressource Azure NetApp Files sera rejetée avec l’erreur suivante :  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Dans le portail Azure, cliquez sur l’icône d’Azure Cloud Shell située dans le coin supérieur droit :

      ![Icône d’Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Si vous avez plusieurs abonnements sur votre compte Azure, sélectionnez celui qui a été mis en liste verte pour Azure NetApp Files :
    
        az account set --subscription <subscriptionId>

3. Dans la console Azure Cloud Shell, entrez la commande suivante pour vérifier que votre abonnement a été mis en liste verte :
    
        az feature list | grep NetApp

   La sortie de la commande se présente comme suit :
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` est votre ID d’abonnement.

    Si le nom de la fonctionnalité `Microsoft.NetApp/ANFGA`ne s'affiche pas, cela signifie que vous n'avez pas accès au service. Arrêtez-vous à cette étape. Suivez les instructions contenues dans [Soumettre une requête de liste d’attente pour l’accès au service](#waitlist) afin de demander un accès au service avant de continuer. 

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