---
title: Schéma de journal de connexion Azure Active Directory dans Azure Monitor | Microsoft Docs
description: Décrire l’authentification Azure AD dans le schéma de journal pour une utilisation dans Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285198"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpréter le schéma des journaux de connexion Azure AD dans Azure Monitor

Cet article décrit le schéma de journal de connexion Azure Active Directory (Azure AD) dans Azure Monitor. La plupart des informations liées aux connexions sont fournies sous l’attribut *Propriétés* de l’objet `records`.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Descriptions de champs

| Nom du champ | Description |
|------------|-------------|
| Temps | Date et heure UTC. |
| ResourceId | Valeur non mappée, vous pouvez ignorer ce champ.  |
| OperationName | Pour les connexions, cette valeur est toujours *Activité de connexion*. |
| operationVersion | Version d’API REST demandée par le client. |
| Catégorie | Pour les connexions, cette valeur est toujours *SignIn*. | 
| TenantId | GUID de locataire associé aux journaux d’activité. |
| ResultType | Le résultat de l’opération de connexion peut être *Success* (Réussite) ou *Failure* (Échec). | 
| ResultSignature | Contient le code d’erreur éventuel de l’opération de connexion. |
| resultDescription | Fournit la description de l’erreur pour l’opération de connexion. |
| DurationMs |  Valeur non mappée, vous pouvez ignorer ce champ.|
| callerIpAddress | Adresse IP du client à l’origine de la demande. | 
| CorrelationId | GUID facultatif transmis par le client. Cette valeur peut aider à corréler des opérations côté client avec des opérations côté serveur, et est utile lors du suivi de journaux d’activité couvrant plusieurs services. |
| Identité | Identité extraite du jeton présenté lors de la création de la demande. Il peut s’agir d’un compte d’utilisateur, d’un compte système ou d’un principal du service. |
| Niveau | Fournit le type de message. Pour l’audit, il s’agit toujours d’*Information*. |
| Lieu | Indique l’emplacement de l’activité de connexion. |
| properties | Répertorie toutes les propriétés associées aux connexions. Pour plus d’informations, voir la [documentation de référence sur l’API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Ce schéma utilise les mêmes noms d’attribut que la ressource de connexion pour une meilleure lisibilité.

## <a name="next-steps"></a>Étapes suivantes

* [Interpréter le schéma des journaux d’audit dans Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [En savoir plus sur les journaux de diagnostic Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)