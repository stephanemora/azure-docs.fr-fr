---
title: Développer pour Azure NetApp Files avec l’API REST | Microsoft Docs
description: Décrit la prise en main de l’utilisation de l’API REST Azure NetApp Files.
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
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65957043"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Développer pour Azure NetApp Files avec l’API REST 

L’API REST pour le service Azure NetApp Files définit des opérations HTTP sur des ressources telles que le compte NetApp, le pool de capacité, les volumes et les captures instantanées. Cet article vous aide à prendre en main l’utilisation de l’API REST Azure NetApp Files.

## <a name="azure-netapp-files-rest-api-specification"></a>Spécification de l’API REST Azure NetApp Files

La spécification de l’API REST pour Azure NetApp Files est publiée via [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) :

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Accéder à l’API REST Azure NetApp Files  

1. Si ce n’est déjà fait, [installez l’interface CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
2. Créer un principal de service dans votre Azure Active Directory (Azure AD) :
   1. Vérifiez que vous disposez des [autorisations suffisantes](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Dans Azure CLI, entrez la commande suivante :  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      La sortie de commande ressemble à l’exemple suivant :  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Conservez la sortie de commande.  Vous aurez besoin des valeurs `appId`, `password` et `tenant`. 

3. Demandez un jeton d’accès OAuth :

    Les exemples de cet article utilisent cURL.  Vous pouvez également utiliser différents outils d’API comme [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) et [Paw](https://paw.cloud/).  

    Remplacez les variables dans l’exemple suivant avec la sortie de commande de l’étape 2 ci-dessus. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    La sortie fournit un jeton d’accès semblable à l’exemple suivant :

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Le jeton affiché est valide pendant 3 600 secondes. Après cela, vous devez demander un nouveau jeton. 
    Enregistrez le jeton dans un éditeur de texte.  Vous en aurez besoin à l’étape suivante.

4. Envoyez un appel de test et incluez le jeton pour valider votre accès à l’API REST :

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Exemples d’utilisation de l’API  

Cet article utilise l’URL suivante pour la ligne de base des requêtes. Cette URL pointe vers la racine de l’espace de noms Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Dans les exemples suivants, remplacez les valeurs `subID` et `resourceGroups` par vos propres valeurs. 

### <a name="get-request-examples"></a>Exemples de requête GET

Vous utilisez une requête GET pour interroger des objets Azure NetApp Files dans un abonnement, comme le montrent les exemples suivants : 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Exemples de requête PUT

Vous utilisez une requête PUT pour créer de nouveaux objets dans Azure NetApp Files, comme le montrent les exemples suivants. Le corps de la requête PUT peut inclure les données au format JSON pour les modifications, ou il peut spécifier un fichier à lire. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Exemples JSON

L’exemple suivant montre comment créer un compte NetApp :

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

L’exemple suivant montre comment créer un pool de capacité : 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

L’exemple suivant montre comment créer un nouveau volume : 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

L’exemple suivant montre comment créer une capture instantanée d’un volume : 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Vous devez spécifier `fileSystemId` pour la création d’une capture instantanée.  Vous pouvez obtenir la valeur `fileSystemId` avec une requête GET sur un volume. 

## <a name="next-steps"></a>Étapes suivantes

[Voir les informations de référence de l’API REST Azure NetApp Files](https://docs.microsoft.com/rest/api/netapp/)
