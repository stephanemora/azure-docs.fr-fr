---
title: Stratégies de validation Gestion des API Azure | Microsoft Docs
description: En savoir plus sur les stratégies que vous pouvez utiliser dans Gestion des API Azure pour valider les demandes et les réponses.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801891"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>Stratégies Gestion des API pour valider les demandes et les réponses

Cet article est une ressource de référence au sujet des stratégies Gestion des API suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API](./api-management-policies.md).

Utilisez des stratégies de validation pour valider les demandes et réponses d’API par rapport à un schéma OpenAPI et établir une protection contre les vulnérabilités telles que l’injection d’en-têtes ou de charge utile. Bien qu’il ne s’agisse pas d’un remplacement d’un pare-feu d’applications web, les stratégies de validation offrent la flexibilité nécessaire pour répondre à une classe supplémentaire de menaces non couvertes par des produits de sécurité qui reposent sur des règles statiques prédéfinies.

## <a name="validation-policies"></a>Stratégies de validation

- [Valider le contenu ](#validate-content) : valide la taille ou le schéma JSON d’une demande ou d’un corps de réponse par rapport au schéma API. 
- [Valider les paramètres](#validate-parameters) : valide les paramètres de l’en-tête de la demande, de la requête ou du chemin par rapport au schéma API.
- [Valider les en-têtes](#validate-headers) : valide les en-têtes de réponse par rapport au schéma API.
- [Valider le code d’état](#validate-status-code) : valide les codes d’état HTTP dans les réponses par rapport au schéma API.

> [!NOTE]
> La taille maximale du schéma API qui peut être utilisée par une stratégie de validation est de 4 Mo. Si le schéma dépasse cette limite, les stratégies de validation retournent des erreurs au moment de l’exécution. Pour l’augmenter, contactez le [support technique](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Actions

Chaque stratégie de validation comprend un attribut qui spécifie une action, que Gestion des API effectue lors de la validation d’une entité dans une demande ou une réponse d’API par rapport au schéma API. Une action peut être spécifiée pour les éléments représentés dans le schéma API et, selon la stratégie, pour ceux qui ne sont pas représentés dans le schéma API. Une action spécifiée dans l’élément enfant d’une stratégie remplace une action spécifiée pour son parent.

Actions disponibles :

| Action         | Description          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | Ignorer la validation. |
| empêcher | Bloque le traitement de la demande ou de la réponse, journalise l’erreur de validation détaillée et retourne une erreur. Le traitement est interrompu lorsque le premier ensemble d’erreurs est détecté. |
| détecter | Consignez les erreurs de validation, sans interrompre le traitement de la demande ou de la réponse. |

## <a name="logs"></a>Journaux d’activité

Les détails des erreurs de validation survenues pendant l’exécution de la stratégie sont journalisés dans la variable `context.Variables` spécifiée dans l’attribut `errors-variable-name` de l’élément racine de la stratégie. Lorsqu’elle est configurée dans une action `prevent`, une erreur de validation bloque le traitement de la demande ou de la réponse et est également propagée à la propriété `context.LastError`. 

Pour examiner les erreurs, utilisez une stratégie de [trace](api-management-advanced-policies.md#Trace) pour consigner les erreurs des variables de contexte vers [Application Insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Impact sur les performances

L’ajout de stratégies de validation peut affecter le débit de l’API. Les principes suivants s’appliquent :
* Plus la taille du schéma API est grande, plus le débit est faible. 
* Plus la charge utile est importante dans une demande ou une réponse, plus le débit est faible. 
* La taille du schéma API a un impact plus important sur les performances que la taille de la charge utile. 
* La validation par rapport à un schéma API dont la taille est de plusieurs mégaoctets peut entraîner des délais d’attente de demande ou de réponse dans certaines conditions. L’effet est plus prononcé dans les niveaux de **consommation** et de **développement** du service. 

Nous vous recommandons d’effectuer des tests de charge avec vos charges de travail de production attendues pour évaluer l’impact des stratégies de validation sur le débit de l’API.

## <a name="validate-content"></a>Valider le contenu

La stratégie `validate-content` valide la taille ou le schéma JSON d’une demande ou d’un corps de réponse par rapport au schéma API. Les formats autres que JSON ne sont pas pris en charge.

### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Exemple

Dans l’exemple suivant, la charge utile JSON dans les demandes et les réponses est validée en mode de détection. Les messages avec des charges utiles supérieures à 100 Ko sont bloqués. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Éléments

| Nom         | Description                                                                                                                                   | Obligatoire |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-content | Élément racine.                                                                                                                               | Oui      |
| contenu | Ajoutez un ou plusieurs de ces éléments pour valider le type de contenu dans la demande ou la réponse, puis exécutez l’action spécifiée.  | Non |

### <a name="attributes"></a>Attributs

| Nom                       | Description                                                                                                                                                            | Obligatoire | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-content-type-action | [Action](#actions) à effectuer pour les demandes ou les réponses avec un type de contenu qui n’est pas spécifié dans le schéma API. |  Oui     | N/A   |
| max-size | Longueur maximale, en octets, du corps de la demande ou de la réponse, vérifiée par rapport à l'en-tête `Content-Length`. Si le corps de la demande ou le corps de la réponse est compressé, cette valeur est la longueur décompressée. Valeur maximale autorisée : 102 400 octets (100 Ko).  | Oui       | N/A   |
| size-exceeded-action | [Action](#actions) à effectuer pour les demandes ou les réponses dont le corps dépasse la taille spécifiée dans `max-size`. |  Oui     | N/A   |
| errors-variable-name | Nom de la variable dans `context.Variables` dans laquelle enregistrer les erreurs de validation.  |   Oui    | N/A   |
| type | Type de contenu pour lequel exécuter la validation du corps, vérifié par rapport à l’en-tête `Content-Type`. La valeur ne respecte pas la casse. S’il est vide, il s’applique à chaque type de contenu spécifié dans le schéma API. |   Non    |  N/A  |
| validate-as | Moteur de validation à utiliser pour la validation du corps d’une demande ou d’une réponse avec un type de contenu correspondant. Actuellement, la seule valeur possible est « json ».   |  Oui     |  N/A  |
| action | [Action](#actions) à effectuer pour les demandes ou les réponses dont le corps ne correspond pas au type de contenu spécifié.  |  Oui      | N/A   |

### <a name="usage"></a>Usage

Cette stratégie peut être utilisée dans les [sections](./api-management-howto-policies.md#sections) et [étendues](./api-management-howto-policies.md#scopes) de stratégie suivantes.

-   **Sections de la stratégie :** inbound, outbound, on-error

-   **Étendues de la stratégie :** toutes les étendues

## <a name="validate-parameters"></a>Valider les paramètres

La stratégie `validate-parameters` valide les paramètres d’en-tête, de demande ou de chemin d’accès dans les demandes par rapport au schéma API.

> [!IMPORTANT]
> Si vous avez importé une API à l’aide d’une version de l’API de gestion antérieure à `2021-01-01-preview`, la stratégie `validate-parameters` peut ne pas fonctionner. Vous pouvez être amené à [réimporter votre API](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) à l'aide de la version `2021-01-01-preview` ou ultérieure de l'API de gestion.


### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Exemple

Dans cet exemple, tous les paramètres de requête et de chemin d’accès sont validés dans le mode de prévention, et les en-têtes dans le mode de détection. La validation est remplacée pour plusieurs paramètres d’en-tête :

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Éléments

| Nom         | Description                                                                                                                                   | Obligatoire |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-parameters | Élément racine. Spécifie les actions de validation par défaut pour tous les paramètres dans les demandes.                                                                                                                              | Oui      |
| headers | Ajoutez cet élément pour remplacer les actions de validation par défaut des paramètres dans les demandes.   | Non |
| query | Ajoutez cet élément pour remplacer les actions de validation par défaut des paramètres de requête dans les demandes.  | Non |
| path | Ajoutez cet élément pour remplacer les actions de validation par défaut des paramètres de chemin d’accès d’URL dans les demandes.  | Non |
| paramètre | Ajoutez un ou plusieurs éléments pour les paramètres nommés pour remplacer la configuration de niveau supérieur des actions de validation. | Non |

### <a name="attributes"></a>Attributs

| Nom                       | Description                                                                                                                                                            | Obligatoire | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-parameter-action | [Action](#actions) à effectuer pour les paramètres de demande spécifiés dans le schéma API. <br/><br/> Lorsqu’il est fourni dans un élément `headers`, `query` ou `path`, la valeur se substitue à la valeur de `specified-parameter-action` dans l’élément `validate-parameters`.  |  Oui     | N/A   |
| unspecified-parameter-action | [Action](#actions) à effectuer pour les paramètres de demande non spécifiés dans le schéma API. <br/><br/>Lorsqu’il est fourni dans un élément `headers` ou `query`, la valeur se substitue à la valeur de `unspecified-parameter-action` dans l’élément `validate-parameters`. |  Oui     | N/A   |
| errors-variable-name | Nom de la variable dans `context.Variables` dans laquelle enregistrer les erreurs de validation.  |   Oui    | N/A   |
| name | Nom du paramètre pour lequel remplacer l’action de validation. La valeur ne respecte pas la casse.  | Oui | N/A |
| action | [Action](#actions) à effectuer pour le paramètre avec le nom correspondant. Si le paramètre est spécifié dans le schéma API, cette valeur remplace la configuration `specified-parameter-action` de niveau supérieur. Si le paramètre n’est pas spécifié dans le schéma API, cette valeur remplace la configuration `unspecified-parameter-action` de niveau supérieur.| Oui | N/A | 

### <a name="usage"></a>Usage

Cette stratégie peut être utilisée dans les [sections](./api-management-howto-policies.md#sections) et [étendues](./api-management-howto-policies.md#scopes) de stratégie suivantes.

-   **Sections de la stratégie :** inbound

-   **Étendues de la stratégie :** toutes les étendues

## <a name="validate-headers"></a>Valider les en-têtes

La stratégie `validate-headers` valide les en-têtes de réponse par rapport au schéma API.

> [!IMPORTANT]
> Si vous avez importé une API à l’aide d’une version de l’API de gestion antérieure à `2021-01-01-preview`, la stratégie `validate-headers` peut ne pas fonctionner. Vous devrez peut-être réimporter votre API à l’aide de l’API de gestion version `2021-01-01-preview` ou ultérieure.

### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Exemple

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Éléments

| Nom         | Description                                                                                                                                   | Obligatoire |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-headers | Élément racine. Spécifie les actions de validation par défaut pour tous les en-têtes dans les réponses.                                                                                                                              | Yes      |
| en-tête | Ajoutez un ou plusieurs éléments pour que les en-têtes nommés remplacent les actions de validation par défaut pour les en-têtes de réponse. | Non |

### <a name="attributes"></a>Attributs

| Nom                       | Description                                                                                                                                                            | Obligatoire | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-header-action | [Action](#actions) à effectuer pour les en-têtes de réponse spécifiés dans le schéma API.  |  Oui     | N/A   |
| unspecified-header-action | [Action](#actions) à effectuer pour les en-têtes de réponse non spécifiés dans le schéma API.  |  Oui     | N/A   |
| errors-variable-name | Nom de la variable dans `context.Variables` dans laquelle enregistrer les erreurs de validation.  |   Oui    | N/A   |
| name | Nom de l’en-tête pour lequel remplacer l’action de validation. La valeur ne respecte pas la casse. | Oui | N/A |
| action | [Action](#actions) à effectuer pour l’en-tête portant le nom correspondant. Si l’en-tête est spécifié dans le schéma API, cette valeur remplace la valeur de `specified-header-action` dans l’élément `validate-headers`. Sinon, elle remplace la valeur de `unspecified-header-action` dans l’élément validate-headers. | Oui | N/A | 

### <a name="usage"></a>Usage

Cette stratégie peut être utilisée dans les [sections](./api-management-howto-policies.md#sections) et [étendues](./api-management-howto-policies.md#scopes) de stratégie suivantes.

-   **Sections de la stratégie :** outbound, on-error

-   **Étendues de la stratégie :** toutes les étendues

## <a name="validate-status-code"></a>Valider le code d’état

La stratégie `validate-status-code` valide les codes d’état HTTP dans les réponses par rapport au schéma API. Cette stratégie peut être utilisée pour empêcher les fuites d’erreurs du serveur principal, qui peuvent contenir des traces de la pile. 

### <a name="policy-statement"></a>Instruction de la stratégie

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Exemple

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Éléments

| Nom         | Description                                                                                                                                   | Obligatoire |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-status-code | Élément racine.                                                                                                | Oui      |
| status-code | Ajoutez un ou plusieurs éléments pour les codes d’état HTTP pour remplacer l’action de validation par défaut pour les codes d’état dans les réponses. | Non |

### <a name="attributes"></a>Attributs

| Nom                       | Description                                                                                                                                                            | Obligatoire | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-status-code-action | [Action](#actions) à effectuer pour les codes d’état HTTP dans les réponses qui ne sont pas spécifiés dans le schéma API.  |  Oui     | N/A   |
| errors-variable-name | Nom de la variable dans `context.Variables` dans laquelle enregistrer les erreurs de validation.  |   Oui    | N/A   |
| code | Code d’état HTTP pour lequel remplacer l’action de validation. | Oui | N/A |
| action | [Action](#actions) à effectuer pour le code d’état correspondant, qui n’est pas spécifié dans le schéma API. Si le code d’état est spécifié dans le schéma API, ce remplacement n’entre pas en vigueur. | Oui | N/A | 

### <a name="usage"></a>Usage

Cette stratégie peut être utilisée dans les [sections](./api-management-howto-policies.md#sections) et [étendues](./api-management-howto-policies.md#scopes) de stratégie suivantes.

-   **Sections de la stratégie :** outbound, on-error

-   **Étendues de la stratégie :** toutes les étendues


## <a name="validation-errors"></a>Erreurs de validation
Le tableau suivant répertorie toutes les erreurs possibles des stratégies de validation. 

* **Détails** : peut être utilisé pour examiner les erreurs. Non destiné à être partagé publiquement.
* **Réponse publique** : erreur retournée au client. Ne divulgue pas les détails de l’implémentation.

| **Nom**                             | **Type**                                                        | **Règle de validation** | **Détails**                                                                                                                                       | **Réponse publique**                                                                                                                       | **Action**           |
|----|----|---|---|---|----|
| **validate-content** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | Le corps de la demande a une longueur de {size} octets, ce qui dépasse la limite configurée de {maxSize} octets.                                                       | Le corps de la demande a une longueur de {size} octets, ce qui dépasse la limite de {maxSize} octets.                                                          | détecter/empêcher |
||ResponseBody                                                    | SizeLimit           | Le corps de la réponse a une longueur de {size} octets, ce qui dépasse la limite configurée de {maxSize} octets.                                                      | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {messageContentType}                 | RequestBody                                                     | Non spécifié         | Le type de contenu non spécifié {messageContentType} n’est pas autorisé.                                                                                     | Le type de contenu non spécifié {messageContentType} n’est pas autorisé.                                                                             | détecter/empêcher |
| {messageContentType}                 | ResponseBody                                                    | Non spécifié         | Le type de contenu non spécifié {messageContentType} n’est pas autorisé.                                                                                     | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| | ApiSchema                                                       |                     | Le schéma API n’existe pas ou n’a pas pu être résolu.                                                                                          | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
|                                      | ApiSchema                                                       |                     | Le schéma API ne spécifie pas de définitions.                                                                                                        | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {messageContentType}                 | RequestBody/ResponseBody                                      | MissingDefinition   | Le schéma API ne contient pas la définition {definitionName}, qui est associée au type de contenu {messageContentType}.                        | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | Le corps de la demande n’est pas conforme à la définition {definitionName}, qui est associée au type de contenu {messageContentType}.<br/><br/>{valError.Message} Ligne : {valError.LineNumber}, Position : {valError.LinePosition}                  | Le corps de la demande n’est pas conforme à la définition {definitionName}, qui est associée au type de contenu {messageContentType}.<br/><br/>{valError.Message} Ligne : {valError.LineNumber}, Position : {valError.LinePosition}            | détecter/empêcher |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | Le corps de la réponse n’est pas conforme à la définition {definitionName}, qui est associée au type de contenu {messageContentType}.<br/><br/>{valError.Message} Ligne : {valError.LineNumber}, Position : {valError.LinePosition}                                       | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
|                                      | RequestBody                                                     | ValidationException | Impossible de valider le corps de la demande pour le type de contenu {messageContentType}.<br/><br/>{détails de l’exception}                                                                | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
|                                      | ResponseBody                                                    | ValidationException | Impossible de valider le corps de la réponse pour le type de contenu {messageContentType}.<br/><br/>{détails de l’exception}                                                                | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| **validate-parameter/validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName} / {headerName}           | QueryParameter / PathParameter / RequestHeader                  | Non spécifié         | Le {paramètre de chemin d’accès/paramètre de requête/en-tête} {paramName} non spécifié n’est pas autorisé.                                                               | Le {paramètre de chemin d’accès/paramètre de requête/en-tête} {paramName} non spécifié n’est pas autorisé.                                                       | détecter/empêcher |
| {headerName}                         | ResponseHeader                                                  | Non spécifié         | L’en-tête non spécifié {headerName} n’est pas autorisé.                                                                                                   | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
|                                      |ApiSchema                                                       |                     | Le schéma API n’existe pas ou n’a pas pu être résolu.                                                                                            | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
|                                       | ApiSchema                                                       |                     | Le schéma API ne spécifie pas de définitions.                                                                                                          | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | Le schéma API ne contient pas la définition {definitionName}, qui est associée au {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName}.  | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | La demande ne peut pas contenir plusieurs valeurs pour le {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName}.                                           | La demande ne peut pas contenir plusieurs valeurs pour le {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName}.                                   | détecter/empêcher |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | La réponse ne peut pas contenir plusieurs valeurs pour l’en-tête {headerName}.                                                                              | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | La valeur du {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName} n’est pas conforme à la définition.<br/><br/>{valError.Message} Ligne : {valError.LineNumber}, Position : {valError.LinePosition}                                          | La valeur du {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName} n’est pas conforme à la définition.<br/><br/>{valError.Message} Ligne : {valError.LineNumber}, Position : {valError.LinePosition}                              | détecter/empêcher |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | La valeur de l’en-tête {headerName} n’est pas conforme à la définition.<br/><br/>{valError.Message} Ligne : {valError.LineNumber}, Position : {valError.LinePosition}                                                                              | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | La valeur du {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName} ne peut pas être analysée en fonction de la définition. <br/><br/>{ex.Message}                                | La valeur du {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName} n’a pas pu être analysée en fonction de la définition. <br/><br/>{ex.Message}                      | détecter/empêcher |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | La valeur de l’en-tête {headerName} n’a pas pu être analysée en fonction de la définition.                                                                  | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | Le {paramètre de requête/paramètre de chemin d’accès/en-tête} {paramName} ne peut pas être validé.<br/><br/>{détails de l’exception}                                                                      | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | Impossible de valider l’en-tête {headerName}.<br/><br/>{détails de l’exception}                                                                                                          | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |
| **validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | StatusCode                                                      | Non spécifié         | Le code d’état de réponse {status-code} n’est pas autorisé.                                                                                                | Impossible de traiter la demande en raison d’une erreur interne. Contactez le propriétaire de l’API.                                                       | détecter/empêcher |


Le tableau suivant répertorie toutes les raisons possibles d’une erreur de validation, ainsi que les valeurs de message possibles :

|  **Motif**         |    **Message** |
|---|---|  
| Demande incorrecte       |     {Details} pour la variable de contexte, {réponse publique} pour le client|
| Réponse non autorisée  | {Details} pour la variable de contexte, {réponse publique} pour le client |






## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des stratégies, consultez :

-   [Stratégies dans Gestion des API](api-management-howto-policies.md)
-   [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
-   [Référence de stratégie](./api-management-policies.md) pour obtenir la liste complète des instructions et des paramètres de stratégie
-   [Exemples de stratégie](./policy-reference.md)
-   [Gestion des erreurs](./api-management-error-handling-policies.md)
