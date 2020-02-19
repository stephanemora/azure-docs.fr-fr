---
title: Stratégies d’authentification dans Gestion des API Azure | Microsoft Docs
description: Découvrez les stratégies d’authentification disponibles dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 5ca153f0d52b65aa1ee56d5757381f1f31c7eeb5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120823"
---
# <a name="api-management-authentication-policies"></a>Stratégies d’authentification dans Gestion des API
Cette rubrique est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a> Stratégies d’authentification

-   [Authenticate with Basic](api-management-authentication-policies.md#Basic) : authentification avec un service principal à l’aide de l’authentification de base.

-   [Authenticate with client certificate](api-management-authentication-policies.md#ClientCertificate) : authentification avec un service principal à l’aide de certificats clients.

-   [Authenticate with managed identity](api-management-authentication-policies.md#ManagedIdentity) (Authentifier avec identité gérée) : authentification avec [l’identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour le service Gestion des API.

##  <a name="Basic"></a> Authenticate with Basic
 La stratégie `authentication-basic` permet l’authentification auprès d’un service principal à l’aide de l’authentification de base. Cette stratégie définit en réalité l’en-tête d’autorisation HTTP sur la valeur correspondant aux informations d’identification fournies dans la stratégie.

### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exemple

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Éléments

|Name|Description|Obligatoire|
|----------|-----------------|--------------|
|authentification-basic|Élément racine.|Oui|

### <a name="attributes"></a>Attributs

|Name|Description|Obligatoire|Default|
|----------|-----------------|--------------|-------------|
|username|Spécifie le nom d’utilisateur associé aux informations d’identification de base.|Oui|N/A|
|password|Spécifie le mot de passe associé aux informations d’identification de base.|Oui|N/A|

### <a name="usage"></a>Usage
 Cette stratégie peut être utilisée dans les [sections](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.

-   **Sections de la stratégie :** inbound

-   **Étendues de la stratégie :** toutes les étendues

##  <a name="ClientCertificate"></a> Authenticate with client certificate
 La stratégie `authentication-certificate` permet l’authentification auprès d’un service principal à l’aide d’un certificat client. Le certificat doit être [installé dans Gestion des API](https://go.microsoft.com/fwlink/?LinkID=511599) en premier et identifié par son empreinte.

### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exemples

Dans cet exemple, le certificat client est identifié par son empreinte.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Dans cet exemple, le certificat client est identifié par le nom de ressource.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Éléments  
  
|Name|Description|Obligatoire|  
|----------|-----------------|--------------|  
|authentication-certificate|Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Name|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Empreinte du certificat client.|`thumbprint` ou `certificate-id` doit être présent.|N/A|  
|certificate-id|Le nom de ressource du certificat.|`thumbprint` ou `certificate-id` doit être présent.|N/A|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** toutes les étendues  

##  <a name="ManagedIdentity"></a> Authentifier avec l’identité managée  
 Utilisez la stratégie `authentication-managed-identity` pour vous authentifier auprès d’un service principal à l’aide de l’identité managée du service Gestion des API. Cette stratégie utilise essentiellement l’identité managée pour obtenir un jeton d’accès auprès d’Azure Active Directory afin d’accéder à la ressource spécifiée. Une fois le jeton obtenu, la stratégie définit la valeur du jeton dans l'en-tête `Authorization` à l’aide du schéma `Bearer`.
  
### <a name="policy-statement"></a>Instruction de la stratégie  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exemple  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Utiliser Identité managée pour s’authentifier auprès d’un service principal
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Utiliser Identité managée dans la stratégie d’envoi de requête
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Éléments  
  
|Name|Description|Obligatoire|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Élément racine.|Oui|  
  
### <a name="attributes"></a>Attributs  
  
|Name|Description|Obligatoire|Default|  
|----------|-----------------|--------------|-------------|  
|resource|Chaîne. ID d’application de l’API web cible (ressource sécurisée) dans Azure Active Directory.|Oui|N/A|  
|output-token-variable-name|Chaîne. Nom de la variable de contexte qui recevra la valeur du jeton en tant que type d’objet `string`. |Non|N/A|  
|ignore-error|Propriété booléenne. Si cet attribut a la valeur `true`, le pipeline de stratégie continuera à s’exécuter même si aucun jeton d’accès n’est obtenu.|Non|false|  
  
### <a name="usage"></a>Usage  
 Cette stratégie peut être utilisée dans les [sections](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) et [étendues](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de stratégie suivantes.  
  
-   **Sections de la stratégie :** inbound  
  
-   **Étendues de la stratégie :** toutes les étendues

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de stratégies, consultez les pages :

+ [Stratégies dans Gestion des API](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
+ [Référence de stratégie](api-management-policy-reference.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
+ [Exemples de stratégie](policy-samples.md)
