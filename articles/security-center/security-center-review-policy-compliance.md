---
title: Examen de la conformité aux stratégies Security Center avec les API REST Azure | Microsoft Docs
description: Découvrez comment utiliser les API REST Azure pour examiner la conformité aux stratégies Security Center.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 1443486590859aac5591aff2ab0551bed9228d7b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301747"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Examiner la conformité aux stratégies Security Center à l’aide d’API REST

Security Center valide régulièrement vos ressources Azure par rapport à vos stratégies de sécurité définies. En outre, Security Center fournit une API REST qui vous permet d’examiner la conformité à partir de vos propres applications ; vous pouvez interroger le service directement ou importer les résultats JSON dans d’autres applications. 

Dans cet article, vous allez apprendre à récupérer l’ensemble actuel des recommandations à partir de toutes les ressources Azure associées à un abonnement.

Pour récupérer l’ensemble actuel des recommandations :
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Générer la demande  

Le paramètre `{subscription-id}` est obligatoire et doit contenir l’ID de l’abonnement Azure qui définit les stratégies. Si vous avez plusieurs abonnements, consultez [Utilisation de plusieurs abonnements](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

Le paramètre `api-version` est obligatoire. À ce stade, ces points de terminaison sont pris en charge uniquement pour `api-version=2015-06-01-preview`. 

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. Défini sur `application/json`.|  
|*Authorization :*|Requis. Défini sur un [jeton d’accès](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valide. |  

## <a name="response"></a>response  

Le code d’état 200 (OK) est retourné pour une réponse correcte, qui contient une liste de tâches recommandées pour sécuriser vos ressources Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Chaque élément de **value** représente une recommandation :

|Propriété de la réponse|Description|
|----------------|----------|
|**state** | Indique si la recommandation est `active` ou `resolved`. |
|**creationTimeUtc** | Date et heure, au format UTC, de la création de la recommandation. |
|**lastStateChangeUtc** | Date et heure, au format UTC, de la dernière modification d’état éventuelle. |
|**securityTaskParameters** | Détails de la recommandation ; les propriétés varient en fonction de la recommandation sous-jacente. |
||
  
Pour connaître les recommandations prises en charge, consultez [Implémenter les recommandations en matière de sécurité](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Les autres codes d’état indiquent les conditions d’erreur. Dans ces cas, l’objet de réponse inclut une description qui explique pourquoi la demande a échoué.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Exemple de réponse  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Cette réponse montre deux recommandations ; chaque élément de la liste correspond à une recommandation spécifique. La première recommande de chiffrer le stockage sur une machine virtuelle Linux, tandis que la seconde suggère d’activer l’audit pour un serveur SQL.

Les recommandations varient selon les stratégies que vous avez activées. Pour plus d’informations, notamment sur les recommandations disponibles, consultez [Gestion des recommandations de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Voir aussi  
- [Définir des stratégies de sécurité](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [API REST des fournisseurs de ressources de sécurité Azure](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Bien démarrer avec l’API REST Azure](https://docs.microsoft.com/rest/api/azure/)   
- [Azure Security Center PowerShell module](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22) (Module PowerShell Azure Security Center)
