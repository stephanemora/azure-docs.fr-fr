---
title: Interpréter le schéma des journaux d’audit Azure Active Directory dans Azure Monitor (préversion) | Microsoft Docs
description: Décrire le schéma de journal d’audit Azure AD pour une utilisation dans Azure Monitor (préversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240390"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Interpréter le schéma des journaux d’audit Azure Active Directory dans Azure Monitor (préversion)

Cet article décrit le schéma de journal d’audit Azure AD dans Azure Monitor. Chaque entrée de journal est stockée sous forme de texte, mis en forme en tant qu’objet blob JSON, comme indiqué dans les deux exemples ci-dessous. 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| Nom du champ | Description |
|------------|-------------|
| time       | Date et heure (UTC) |
| operationName | Nom de l’opération |
| operationVersion | Version d’API REST demandée par le client. |
| category | Actuellement, *Audit* est la seule valeur prise en charge. |
| tenantId | GUID de locataire associé aux journaux |
| resultType | Résultat de l’opération : *Réussite* ou *Échec* |
| resultSignature |  Non mappé, vous pouvez ignorer ce champ. | 
| resultDescription | Description supplémentaire du résultat, le cas échéant. | 
| durationMS |  Non mappé, vous pouvez ignorer ce champ. |
| callerIpAddress | Adresse IP du client qui a effectué la requête. | 
| correlationId | GUID facultatif transmis par le client. Il peut aider à mettre en corrélation les opérations côté client avec les opérations côté serveur, et il est utile lors du suivi des journaux qui concernent plusieurs services. |
| identité | Identité issue du jeton présenté lors de la création de la requête. Il peut s’agir d’un compte d’utilisateur, d’un compte système ou d’un principal de service. |
| level | Type de message, Pour les journaux d’audit, il s’agit toujours d’*information*. |
| location | Emplacement du centre de données |
| properties | Répertorie les propriétés prises en charge d’un journal d’audit. Pour plus d'informations, consultez le tableau ci-dessous. | 


| Nom de la propriété | Description |
|---------------|-------------|
| AuditEventCategory | Type d’événement d’audit. Il peut s’agir de *Gestion des utilisateurs*, *Gestion des applications*, etc.|
| Type d'identité | *Application* ou *Utilisateur* |
| Type d'opération | Il peut s’agir d’*Ajouter*, *Mettre à jour*, *Supprimer* ou *Autres*. |
| Type de ressource cible | Spécifie le type de ressource cible sur lequel a été effectuée l’opération. Il peut s’agir d’*Application*, *Utilisateur*, *Rôle* ou *Stratégie*. | 
| Nom de ressource cible | Nom de la ressource cible. Par exemple, il peut s’agir d’un nom d’application, d’un nom de rôle, d’un nom d'utilisateur principal ou d’un nom de principal de service |
| additionalTargets | Répertorie les propriétés supplémentaires pour des opérations spécifiques. Par exemple, pour une opération de mise à jour, les anciennes valeurs et les nouvelles valeurs sont répertoriées sous *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux de connexion dans Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [En savoir plus sur les journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Questions fréquentes et problèmes connus](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)