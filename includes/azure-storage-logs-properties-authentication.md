---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: fc7157fad4227686ab4fddfe6ce3119119827c6b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803205"
---
| Propriété | Description |
|:--- |:---|
|**identity / type** | Type d’authentification qui a été utilisé pour effectuer la demande. Par exemple : `OAuth`, `Kerberos`, `SAS Key`, `Account Key` ou `Anonymous` |
|**identity / tokenHash**|Hachage SHA-256 du jeton d’authentification utilisé sur la requête. <br>Lorsque le type d’authentification est `Account Key`, le format est « clé1 \| clé2 (hachage SHA256 de la clé) ». Par exemple : `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Lorsque le type d’authentification est `SAS Key`, le format est « clé1 \| clé2 (hachage SHA 256 de la clé),SignatureSas(hachage SHA 256 du jeton SAS) ». Par exemple : `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Lorsque le type d’authentification est `OAuth`, le format est « hachage SHA 256 du jeton OAuth ». Par exemple : `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DD25C`<br> Pour les autres types d’authentification, il n’y a aucun champ tokenHash. |
|**authorization / action** | Action affectée à la demande. |
|**authorization / roleAssignmentId** | ID d’attribution de rôle. Par exemple : `4e2521b7-13be-4363-aeda-111111111111`.|
|**authorization / roleDefinitionId** | ID de définition de rôle. Par exemple : `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals / id** | ID du principal de sécurité. Par exemple : `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals / type** | Type du principal de sécurité. Par exemple : `ServicePrincipal`. |
|**requester / appID** | ID d’application Open Authorization (OAuth) utilisé comme demandeur. <br> Par exemple : `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / audience** | Audience OAuth de la demande. Par exemple : `https://storage.azure.com`. |
|**requester / objectId** | ID d’objet OAuth du demandeur. Dans le cas de l’authentification Kerberos, représente l’identificateur d’objet de l’utilisateur authentifié Kerberos. Par exemple : `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / tenantId** | ID de locataire OAuth de l’identité. Par exemple : `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | Émetteur de jeton OAuth. Par exemple : `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | Nom d’utilisateur principal (UPN) du demandeur. Par exemple : `someone@contoso.com`. |
|**requester / userName** | Ce champ est réservé à un usage interne.|
