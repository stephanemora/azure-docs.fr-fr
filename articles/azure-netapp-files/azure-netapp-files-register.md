---
title: S’inscrire à Azure NetApp Files | Microsoft Docs
description: Décrit comment s’inscrire pour utiliser Azure Files de NetApp.
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
ms.openlocfilehash: fbe0b82008d7b15332c4e2cd62c49c611f20fe89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794712"
---
# <a name="register-for-azure-netapp-files"></a>S’inscrire à Azure NetApp Files

> [!IMPORTANT] 
> Avant d’enregistrer le fournisseur de ressources de fichiers NetApp de Azure, vous devez reçu un e-mail à partir de l’équipe Azure NetApp Files confirmant que vous avez reçu l’accès au service. 

Dans cet article, découvrez comment inscrire des fichiers de NetApp Azure afin que vous pouvez commencer à utiliser le service.

## <a name="waitlist"></a>Envoyer une demande de liste d’attente pour l’accès au service

1. Envoyer une demande de liste d’attente pour l’accès au service Azure NetApp Files via le [page de soumission d’une liste d’attente Azure NetApp Files](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Inscription de la liste d’attente ne garantit pas l’accès au service d’exécution. 

2. Attendre un message de confirmation officiel de l’équipe Azure NetApp Files avant de continuer avec d’autres tâches. 

## <a name="resource-provider"></a>Inscrire le fournisseur de ressources de NetApp

Pour utiliser le service, vous devez inscrire le fournisseur de ressources Azure pour Azure NetApp Files.

> [!NOTE] 
> Vous ne pourrez pas inscrire correctement le fournisseur de ressources NetApp même sans être accordé l’accès pour le service. Toutefois, sans l’autorisation d’accès, n’importe quel portail Azure ou une demande d’API pour créer un compte NetApp ou toute autre ressource Azure NetApp fichiers est rejetée avec l’erreur suivante :  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


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

    Si vous ne voyez pas le nom de la fonctionnalité `Microsoft.NetApp/publicPreviewADC`, vous n’avez pas accès au service. Arrêter à cette étape. Suivez les instructions dans [soumettre une demande de liste d’attente pour l’accès au service](#waitlist) pour demander l’accès service avant de continuer. 

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