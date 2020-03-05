---
title: À propos des clés, des secrets et des certificats Azure Key Vault - Azure Key Vault
description: Vue d’ensemble de l’interface REST Azure Key Vault et des détails de développement sur les clés, les secrets et les certificats.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 9bbbcc38116c5681e3b5c867690c296f60507ad1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196108"
---
# <a name="about-keys-secrets-and-certificates"></a>À propos des clés, des secrets et des certificats

Azure Key Vault permet aux utilisateurs et aux applications Microsoft Azure de stocker et d’utiliser plusieurs types de données de secret/clé :

- Clés de chiffrement : Prend en charge plusieurs types de clés et algorithmes, et permet d’utiliser des modules de sécurité matériels (HSM) pour les clés ayant une valeur importante. 
- Secrets : Fournit un stockage sécurisé des secrets, comme les mots de passe et les chaînes de connexion de base de données.
- Certificats : Prend en charge les certificats, qui sont basés sur des clés et des secrets, et ajoute une fonctionnalité de renouvellement automatique.
- Stockage Azure : Peut gérer pour vous les clés d’un compte Stockage Azure. En interne, Key Vault peut lister (synchroniser) les clés avec un compte Stockage Azure et regénérer (faire tourner) régulièrement les clés. 

Pour plus d’informations générales sur Key Vault, consultez [Présentation d’Azure Key Vault](/azure/key-vault/key-vault-overview).

## <a name="azure-key-vault"></a>Azure Key Vault

Les sections suivantes fournissent des informations générales applicables à l’implémentation du service Key Vault.

### <a name="supporting-standards"></a>Prise en charge des standards

Les spécifications JSON (JavaScript Object Notation) et JOSE (JavaScript Object Signing and Encryption) sont des informations d’arrière-plan importantes.  

-   [Clé web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Chiffrement web JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Algorithmes web JSON (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [Signature web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Types de données

Reportez-vous aux spécifications JOSE pour les types de données appropriés pour les clés, le chiffrement et la signature.  

-   **algorithm** : algorithme pris en charge pour une opération sur les clés, RSA1_5 par exemple  
-   **ciphertext-value** : octets de texte de chiffrement, codés avec Base64URL  
-   **digest-value** : sortie d’un algorithme de hachage, codée avec Base64URL  
-   **key-type** : un des types de clés pris en charge, par exemple RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value** : octets de texte en clair, codés avec Base64URL  
-   **signature-value** : sortie d’un algorithme de signature, codée avec Base64URL  
-   **base64URL** : valeur binaire codée Base64URL [RFC4648]  
-   **boolean** : true (vrai) ou false (faux)  
-   **Identity** : identité d’Azure Active Directory (AAD).  
-   **IntDate** : valeur décimale JSON représentant le nombre de secondes entre 1970-01-01T0:0:0Z UTC et la date/heure UTC spécifiée. Consultez la RFC3339 pour plus d’informations sur les dates/heures en général, et sur UTC en particulier.  

### <a name="objects-identifiers-and-versioning"></a>Objets, identificateurs et gestion de versions

Les objets stockés dans Key Vault sont versionnés chaque fois qu’une nouvelle instance d’un objet est créée. Chaque version se voit assigner un identificateur unique et une URL. Quand un objet est créé, il se voit attribuer un identificateur de version unique et est marqué comme version actuelle de l’objet. La création d’une nouvelle instance portant le même nom d’objet attribue au nouvel objet un identificateur de version unique, ce qui en fait la version actuelle.  

Les objets dans Key Vault peuvent être reconnus à l’aide de l’identificateur actuel ou d’un identificateur spécifique à la version. Par exemple, pour une clé nommée `MasterKey`, l’exécution d’opérations avec l’identificateur actuel amène le système à utiliser la dernière version disponible. L’exécution d’opérations avec l’identificateur spécifique à la version amène le système à utiliser cette version spécifique de l’objet.  

Les objets sont identifiés de façon unique dans Key Vault avec une URL. Il n’y a pas deux objets avec la même URL dans le système, quel que soit l’emplacement géographique. L’URL complète d’un objet est appelée identificateur d’objet. L’URL est constituée d’un préfixe qui identifie le coffre de clés, du type d’objet, du nom d’objet fourni par l’utilisateur et d’une version d’objet. Le nom d’objet n’est pas sensible à la casse et est non modifiable. Les identificateurs qui n’incluent pas la version d’objet sont appelés des identificateurs de base.  

Pour plus d’informations, consultez [Authentification, requêtes et réponses](authentication-requests-and-responses.md)

Un identificateur d’objet a le format général suivant :  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Où :  

|||  
|-|-|  
|`keyvault-name`|Le nom d’un coffre de clés dans le service Microsoft Azure Key Vault.<br /><br /> Les noms de coffre de clés sont choisis par l’utilisateur et sont globalement uniques.<br /><br /> Le nom d’un coffre de clés doit être une chaîne comprise entre 3 et 24 caractères qui doit contenir uniquement des chiffres, des lettres et des tirets (0-9, a-z, A-Z et -).|  
|`object-type`|Le type de l’objet, « clés » ou « secrets ».|  
|`object-name`|Un `object-name` est un nom fourni par l’utilisateur et doit être unique dans un coffre de clés. Le nom doit être une chaîne comprise entre 1 et 127 caractères qui doit contenir uniquement des chiffres, des lettres et des tirets (0-9, a-z, A-Z et -).|  
|`object-version`|Un `object-version` est un identificateur de chaîne de 32 caractères généré par le système qui peut être utilisé pour une version unique d’un objet.|  

## <a name="key-vault-keys"></a>Clés Key Vault

### <a name="keys-and-key-types"></a>Clés et types de clés

Les clés de chiffrement dans Key Vault sont représentées en tant qu’objets de clé web JSON [JWK]. Les spécifications JWK/JWA de base sont également étendues pour rendre les types de clés uniques lors de l’implémentation du coffre de clés. Par exemple, l’importation de clés avec l’empaquetage spécifique au fournisseur HSM permet de sécuriser le transport des clés susceptibles d’être utilisées uniquement dans les modules HSM Key Vault.  

- **Clés « logicielles »**  : clé traitée dans le logiciel par Key Vault, mais qui est chiffrée au repos avec une clé système qui se trouve dans un HSM. Les clients peuvent importer une clé RSA ou EC (Elliptic Curve) existante ou demander à Key Vault d’en générer une.
- **Clés « matérielles »**  : clé traitée dans un module de sécurité matériel (HSM). Ces clés sont protégées dans un des mondes de sécurité HSM Key Vault (il existe un monde de sécurité par emplacement géographique afin de garantir l’isolation). Les clients peuvent importer une clé RSA ou EC, sous forme logicielle ou en exportant depuis un appareil HSM compatible. Les clients peuvent également demander à Key Vault de générer une clé. Ce type de clé ajoute l’attribut key_hsm à la JWK pour le transport du matériel de clé HSM.

     Pour plus d’informations sur les frontières géographiques, consultez [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault prend en charge les clés RSA et Elliptic Curve uniquement. 

-   **EC** : clé « logicielle » à courbe elliptique.
-   **EC-HSM** : clé « matérielle » à courbe elliptique.
-   **RSA** : clé « logicielle » RSA.
-   **RSA-HSM** : clé « matérielle » RSA.

Key Vault prend en charge les clés RSA de taille 2 048, 3 072 et 4 096. Key Vault prend en charge les clés Elliptic Curve de type P-256, P-384, P-521 et P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Protection par chiffrement

Les modules de chiffrement qu’utilise Key Vault, HSM ou logiciel, sont conformes aux standards FIPS (Federal Information Processing Standards). Aucune action spéciale ne doit être effectuée pour l’exécution en mode FIPS. Les clés **créées** ou **importées** comme clés protégées par HSM sont traitées dans un module HSM, et sont conformes à FIPS 140-2 niveau 2. Les clés **créées** ou **importées** comme clés protégées par logiciel sont traitées dans des module de chiffrement conformes à FIPS 140-2 niveau 1. Pour plus d’informations, consultez [Clés et types de clés](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algorithmes EC
 Les identificateurs d’algorithme suivants sont pris en charge avec les clés EC et EC-HSM dans Key Vault. 

#### <a name="curve-types"></a>Types de courbe

-   **P-256** - La courbe NIST P-256, définie sur [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** : courbe SEC SECP256K1, définie dans [SEC 2: Recommended Elliptic Curve Domain Parameters](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - La courbe NIST P-384, définie sur [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - La courbe NIST P-521, définie sur [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** : ECDSA pour codes de hachage SHA-256 et clés créées avec la courbe P-256. Cet algorithme est décrit dans [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** : ECDSA pour codes de hachage SHA-256 et clés créées avec la courbe P-256K. Cet algorithme est en attente de normalisation.
-   **ES384** : ECDSA pour codes de hachage SHA-384 et clés créées avec la courbe P-384. Cet algorithme est décrit dans [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** : ECDSA pour codes de hachage SHA-512 et clés créées avec la courbe P-521. Cet algorithme est décrit dans [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algorithmes RSA  
 Les identificateurs d’algorithme suivants sont pris en charge avec les clés RSA et RSA-HSM dans Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5** : chiffrement à clé RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** : RSAES utilisant OAEP (Optimal Asymmetric Encryption Padding) [RFC3447], avec les paramètres par défaut spécifiés par RFC 3447, section A.2.1. Ces paramètres par défaut utilisent une fonction de hachage de SHA-1 et une fonction de génération de masque de MGF1 avec SHA-1.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** : RSASSA-PKCS-v1_5 utilisant SHA-256. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-256 et doit être d’une longueur de 32 octets.  
-   **RS384** : RSASSA-PKCS-v1_5 utilisant SHA-384. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-384 et doit être d’une longueur de 48 octets.  
-   **RS512** : RSASSA-PKCS-v1_5 utilisant SHA-512. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-512 et doit être d’une longueur de 64 octets.  
-   **RSNULL** : consultez [RFC2437], un cas d’utilisation spécial permettant certains scénarios TLS.  

###  <a name="key-operations"></a>Opérations sur les clés

Key Vault prend en charge les opérations sur les objets de clés suivantes :  

-   **Créer** : permet à un client de créer une clé dans Key Vault. La valeur de la clé est générée par le coffre de clés et stockée, mais n’est pas communiquée au client. Les clés asymétriques peuvent être créées dans un coffre de clés.  
-   **Importer** : permet à un client d’importer une clé existante dans Key Vault. Des clés asymétriques peuvent être importées dans un coffre de clés selon diverses méthodes d’empaquetage dans un construct JWK. 
-   **Mettre à jour** : permet à un client disposant des autorisations suffisantes de modifier les métadonnées (attributs de clé) associées à une clé précédemment stockée dans Key Vault.  
-   **Supprimer** : permet à un client disposant des autorisations suffisantes de supprimer une clé dans Key Vault.  
-   **Lister** : permet à un client de lister toutes les clés d’un coffre de clés donné.  
-   **Lister les versions** : permet à un client de lister toutes les versions d’une clé donnée dans un coffre de clés donné.  
-   **Obtenir** : permet à un client de récupérer les parties publiques d’une clé donnée dans un coffre de clés.  
-   **Sauvegarder** : permet d’exporter une clé sous une forme protégée.  
-   **Restaurer** : permet d’importer une clé précédemment sauvegardée.  

Pour plus d’informations, voir [Informations de référence sur les opérations liées aux clés dans l’API REST Key Vault](/rest/api/keyvault).  

Lorsqu’une clé a été créée dans un coffre de clés, les opérations de chiffrement suivantes peuvent être exécutées à l’aide de la clé :  

-   **Signer et vérifier** : cette opération vise à « signer le hachage » ou à « vérifier le hachage », car Key Vault ne prend pas en charge le hachage du contenu lors la création de la signature. Les applications doivent hacher les données à signer localement puis demander à Key Vault de signer le hachage. La vérification des hachages signés est prise en charge par souci pratique pour les applications qui n’ont peut-être pas accès au matériel de clé [publique]. Pour optimiser les performances des applications, vérifiez que les opérations sont effectuées localement.  
-   **Chiffrement / encapsulation de clé** : une clé stockée dans Key Vault peut être utilisée pour protéger une autre clé, généralement une clé de chiffrement symétrique de contenu (CEK). Lorsque la clé dans Key Vault est asymétrique, le chiffrement de clé est utilisé. Par exemple, les opérations WRAPKEY/UNWRAPKEY et RSA-OAEP sont équivalentes à ENCRYPT/DECRYPT. Lorsque la clé dans Key Vault est symétrique, le wrapping de clé est utilisé. Par exemple, AES-KW. L’opération WRAPKEY est prise en charge par souci pratique pour les applications qui n’ont peut-être pas accès au matériel de clé [publique]. Pour optimiser les performances des applications, les opérations WRAPKEY doivent être effectuées localement.  
-   **Chiffrer et déchiffrer** : une clé stockée dans Key Vault peut être utilisée pour chiffrer ou déchiffrer un bloc de données. La taille du bloc est déterminée par le type de clé et l’algorithme de chiffrement sélectionné. L’opération Encrypt est fournie par souci pratique pour les applications qui n’ont peut-être pas accès au matériel de clé [publique]. Pour optimiser les performances des applications, les opérations de chiffrement doivent être effectuées localement.  

Alors que les opérations WRAPKEY/UNWRAPKEY utilisant des clés asymétriques peuvent sembler superflues (car elles sont équivalentes à ENCRYPT/DECRYPT), l’utilisation d’opérations distinctes est importante. La distinction fournit une séparation de la sémantique et des autorisations de ces opérations, ainsi qu’une cohérence quand d’autres types de clés sont pris en charge par le service.  

Key Vault ne prend pas en charge les opérations EXPORT. Lorsqu’une clé est provisionnée dans le système, elle ne peut pas être extraite et son matériel de clé ne peut pas être modifié. Toutefois, les utilisateurs de Key Vault peuvent avoir besoin de leur clé pour d’autres utilisations, par exemple après sa suppression. Dans ce cas, ils peuvent utiliser les opérations BACKUP et RESTORE pour exporter/importer la clé dans un formulaire protégé. Les clés créées par l’opération BACKUP ne peuvent pas être utilisées en dehors de Key Vault. L’opération IMPORT peut également être utilisée sur plusieurs instances de Key Vault.  

Les utilisateurs peuvent limiter les opérations de chiffrement prises en charge par Key Vault, par clé, à l’aide de la propriété key_ops de l’objet JWK.  

Pour plus d’informations sur les objets JWK, consultez [Clé web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Attributs de clé

Les attributs suivants peuvent être spécifiés en plus du matériel de clé. Dans une requête JSON, le mot clé attributes et les accolades, {}, sont requis même si aucun attribut n’est spécifié.  

- *enabled* : booléen, facultatif, **true** par défaut. Spécifie si la clé est activée et peut être utilisée pour des opérations de chiffrement. L’attribut *enabled* est utilisé avec *nbf* et *exp*. Lorsqu’une opération se produit entre *nbf* et *exp*, elle n’est autorisée que si *enabled* est défini sur **true**. Les opérations en dehors de la fenêtre *nbf* / *exp* sont automatiquement interdites, à l’exception de certains types d’opérations dans des [conditions particulières](#date-time-controlled-operations).
- *nbf* : IntDate, facultatif, « now » par défaut. L’attribut *nbf* (not before ou pas avant) identifie l’heure avant laquelle la clé NE DOIT PAS être utilisée pour des opérations de chiffrement, à l’exception de certains types d’opérations dans des [conditions particulières](#date-time-controlled-operations). Le traitement de l’attribut *nbf* requiert que la date/heure actuelle SOIT postérieure ou égale à la date/heure « not-before » (pas avant) indiquée dans l’attribut *nbf*. Key Vault PEUT prévoir une légère marge, normalement pas plus de quelques minutes, pour prendre en compte la variation d’horloge. Sa valeur DOIT être un nombre contenant une valeur IntDate.  
- *exp* : IntDate, facultatif, « forever » par défaut. L’attribut *exp* (heure d’expiration) identifie l’heure à ou après laquelle la clé NE DOIT PAS être utilisée pour une opération de chiffrement, à l’exception de certains types d’opérations dans des [conditions particulières](#date-time-controlled-operations). Le traitement de l’attribut *exp* requiert que la date/heure actuelle SOIT antérieure à la date/heure d’expiration indiquée dans l’attribut *exp*. Key Vault PEUT prévoir une légère marge, normalement pas plus de quelques minutes, pour prendre en compte la variation d’horloge. Sa valeur DOIT être un nombre contenant une valeur IntDate.  

Des attributs supplémentaires en lecture seule sont inclus dans toute réponse contenant des attributs de clé :  

- *created* : IntDate, facultatif. L’attribut *created* indique quand cette version de la clé a été créée. La valeur est null pour les clés créées avant l’ajout de cet attribut. Sa valeur DOIT être un nombre contenant une valeur IntDate.  
- *updated* : IntDate, facultatif. L’attribut *updated* indique quand cette version de la clé a été mise à jour. La valeur est null pour les clés qui ont été mises à jour pour la dernière fois avant l’ajout de cet attribut. Sa valeur DOIT être un nombre contenant une valeur IntDate.  

Pour plus d’informations sur IntDate et d’autres types de données, consultez [Types de données](#data-types)  

#### <a name="date-time-controlled-operations"></a>Opérations contrôlées par date/heure

Les clés pas encore valides ou expirées, en dehors de la fenêtre *nbf* / *exp*, s’appliquent pour les opérations **decrypt**, **unwrap** et **verify** (elles ne retournent pas d’erreur 403, Forbidden). La logique d’attribution d’utilisation de l’état « not-yet-valid » (pas encore valide) consiste à autoriser le test de la clé avant son utilisation en production. La logique d’attribution d’utilisation de l’état « expired » (expiré) consiste à autoriser des opérations de récupération sur des données qui ont été créées alors que la clé était valide. Vous pouvez également désactiver l’accès à une clé à l’aide de stratégies de Key Vault, ou en définissant l’attribut de clé *enabled* sur **false**.

Pour plus d’informations sur les types de données, consultez [Types de données](#data-types).

Pour plus d’informations sur les autres attributs possibles, consultez [Clé web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Balises de clé

Vous pouvez spécifier des métadonnées spécifiques à l’application supplémentaires sous la forme de balises. Key Vault prend en charge jusqu’à 15 balises, chacune d’entre elles pouvant avoir un nom de 256 caractères et une valeur de 256 caractères.  

>[!Note]
>Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet (clés, secrets ou certificats).

###  <a name="key-access-control"></a>Contrôle d’accès aux clés

Le contrôle d’accès pour les clés gérées par Key Vault est fourni au niveau d’un coffre de clés qui fait office de conteneur de clés. La stratégie de contrôle d’accès pour les clés est différente de la stratégie de contrôle d’accès pour les secrets dans un même coffre de clés. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les clés et doivent maintenir une segmentation et une gestion des clés appropriées au scénario. Le contrôle d’accès pour les clés est indépendant du contrôle d’accès pour les secrets.  

Les autorisations suivantes peuvent être accordées, par utilisateur/principal du service, dans l’entrée du contrôle d’accès aux clés sur un coffre. Ces autorisations reflètent précisément les opérations autorisées sur un objet clé.  L’octroi de l’accès à un principal de service dans le coffre de clés est une opération unique et demeure identique pour tous les abonnements Azure. Vous pouvez l’utiliser pour déployer autant de certificats que vous le souhaitez. 

- Autorisations pour les opérations de gestion de clés
  - *get* : lire la partie publique d’une clé, ainsi que ses attributs
  - *list* : lister les clés ou les versions d’une clé stockée dans un coffre de clés
  - *update* : mettre à jour les attributs d’une clé
  - *create* : créer des clés
  - *import* : importer une clé dans un coffre de clés
  - *delete* : supprimer l’objet clé
  - *recover* : récupérer une clé supprimée
  - *backup* : sauvegarder une clé dans un coffre de clés
  - *restore* : restaurer une clé sauvegardée dans un coffre de clés

- Autorisations pour les opérations de chiffrement
  - *decrypt* : utiliser la clé pour déprotéger une séquence d’octets
  - *encrypt* : utiliser la clé pour protéger une séquence arbitraire d’octets
  - *unwrapKey* : utiliser la clé pour déprotéger des clés symétriques wrappées
  - *wrapKey* : utiliser la clé pour protéger une clé symétrique
  - *verify* : utiliser la clé pour vérifier des synthèses  
  - *sign* : utiliser la clé pour signer des synthèses
    
- Autorisations pour les opérations privilégiées
  - *purge* : effacer (supprimer définitivement) une clé supprimée

Pour plus d’informations sur l’utilisation des clés, consultez [Informations de référence sur les opérations de clé dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Secrets Key Vault 

### <a name="working-with-secrets"></a>Utilisation de secrets

Pour les développeurs, les API Key Vault acceptent et retournent des valeurs de secret sous forme de chaînes. En interne, Key Vault stocke et gère les secrets par séquence d’octets (8 bits), avec une taille maximale de 25 Ko chacune. Le service Key Vault ne fournit pas la sémantique pour les secrets. Il accepte simplement les données, les chiffre, les stocke et retourne un identificateur de secret (« id »). L’identificateur peut être utilisé pour récupérer le secret ultérieurement.  

Pour les données extrêmement sensibles, les clients doivent envisager des couches supplémentaires de protection des données. Un exemple serait de chiffrer les données à l’aide d’une clé de protection distincte avant le stockage dans Key Vault.  

Key Vault prend également en charge un champ contentType pour les secrets. Les clients peuvent spécifier le type de contenu d’un secret pour simplifier l’interprétation des données du secret lors de leur récupération. La longueur maximale de ce champ est de 255 caractères. Il n’existe aucune valeur prédéfinie. L’utilisation suggérée est donnée à titre de conseil pour interpréter les données du secret. Par exemple, une implémentation peut stocker des mots de passe et des certificats en tant que secret. Ensuite, utilisez ce champ pour les différencier. Il n’existe aucune valeur prédéfinie.  

### <a name="secret-attributes"></a>Attributs de secret

Les attributs suivants peuvent être spécifiés en plus des données du secret :  

- *exp* : IntDate, facultatif, la valeur par défaut est **forever**. L’attribut *exp* (heure d’expiration) identifie l’heure d’expiration à ou après laquelle les données du secret NE DOIVENT PAS être récupérées, sauf dans des [conditions particulières](#date-time-controlled-operations). Ce champ n’est fourni qu’à titre d’**information**, il informe uniquement les utilisateurs du service de coffre de clés qu’un secret particulier ne peut pas être utilisé. Sa valeur DOIT être un nombre contenant une valeur IntDate.   
- *nbf* : IntDate, facultatif, la valeur par défaut est **now**. L’attribut *nbf* (pas avant) identifie l’heure avant laquelle les données du secret NE DOIVENT PAS être récupérées, sauf dans des [conditions particulières](#date-time-controlled-operations). Ce champ est fourni à titre d’**information** uniquement. Sa valeur DOIT être un nombre contenant une valeur IntDate. 
- *enabled* : booléen, facultatif, **true** par défaut. Cet attribut spécifie si les données du secret peuvent être récupérées. L’attribut enabled est utilisé avec *nbf* et *nbf* lorsqu’une opération se produit entre *nbf* et *exp*, elle ne sera autorisée que si enabled est défini sur **true**. Les opérations en dehors de la fenêtre *nbf* et *exp* sont automatiquement interdites, sauf dans des [conditions particulières](#date-time-controlled-operations).  

Des attributs supplémentaires en lecture seule sont inclus dans toute réponse contenant des attributs de secret :  

- *created* : IntDate, facultatif. L’attribut created indique quand cette version du secret a été créée. Cette valeur est null pour les secrets créés avant l’ajout de cet attribut. Sa valeur doit être un nombre contenant une valeur IntDate.  
- *updated* : IntDate, facultatif. L’attribut updated indique quand cette version du secret a été mise à jour. Cette valeur est null pour les secrets qui ont été mis à jour pour la dernière fois avant l’ajout de cet attribut. Sa valeur doit être un nombre contenant une valeur IntDate.

#### <a name="date-time-controlled-operations"></a>Opérations contrôlées par date/heure

L’opération **get** d’un secret s’appliquera aux secrets pas encore valides ou ayant expiré, en dehors de la fenêtre *nbf* / *exp*. L’appel de l’opération **get** d’un secret, pour un secret pas encore valide, peut être utilisé à des fins de test. La récupération (opération **get**ting) d’un secret expiré peut être utilisée pour des opérations de récupération.

Pour plus d’informations sur les types de données, consultez [Types de données](#data-types).  

### <a name="secret-access-control"></a>Contrôle d’accès aux secrets

Le contrôle d’accès pour les secrets géré dans Key Vault est fourni au niveau du coffre de clés qui contient ces secrets. La stratégie de contrôle d’accès pour les secrets est différente de la stratégie de contrôle d’accès pour les clés dans un même coffre de clés. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les secrets et doivent maintenir une segmentation et une gestion des secrets appropriées au scénario.   

Les autorisations suivantes peuvent être utilisées, par principal, dans l’entrée de contrôle d’accès aux secrets sur un coffre, et reflètent précisément les opérations autorisées sur un objet secret :  

- Autorisations pour les opérations de gestion de secrets
  - *get* : lire un secret  
  - *list* : lister les secrets ou les versions d’un secret stockés dans un coffre de clés  
  - *set* : Créer un secret  
  - *delete* : supprimer un secret  
  - *recover* : récupérer un secret supprimé
  - *backup* : sauvegarder un secret dans un coffre de clés
  - *restore* : restaurer un secret sauvegardé sur un coffre de clés

- Autorisations pour les opérations privilégiées
  - *purge* : effacer (supprimer définitivement) un secret supprimé

Pour plus d’informations sur l’utilisation des secrets, voir [Informations de référence sur les opérations liées aux secrets dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Balises de secret  
Vous pouvez spécifier des métadonnées spécifiques à l’application supplémentaires sous la forme de balises. Key Vault prend en charge jusqu’à 15 balises, chacune d’entre elles pouvant avoir un nom de 256 caractères et une valeur de 256 caractères.  

>[!Note]
>Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet (clés, secrets ou certificats).

## <a name="key-vault-certificates"></a>Certificats Key Vault

La prise en charge des certificats Key Vault permet de gérer vos certificats x509 et les comportements suivants :  

-   Permet à un propriétaire de certificat de créer un certificat via un processus de création de Key Vault ou l’importation d’un certificat existant. Concerne à la fois les certificats auto-signés et ceux générés par une autorité de certification.
-   Permet à un propriétaire de certificat Key Vault d’implémenter le stockage sécurisé et la gestion des certificats X509 sans interaction avec des éléments de clé privée.  
-   Permet à un propriétaire de certificat de créer une stratégie qui ordonne à Key Vault de gérer le cycle de vie d’un certificat.  
-   Permet aux propriétaires de certificat de fournir des informations de contact pour les notifications concernant des événements d’expiration du cycle de vie et le renouvellement de certificat.  
-   Prend en charge le renouvellement automatique avec des émetteurs sélectionnés : fournisseurs de certificats X509 de partenaire Key Vault/autorités de certification.

>[!Note]
>Les fournisseurs/autorités non partenaires sont également autorisés, mais ils ne prendront pas en charge la fonctionnalité de renouvellement automatique.

### <a name="composition-of-a-certificate"></a>Composition d’un certificat

Lorsqu’un certificat Key Vault est créé, une clé et un secret adressables sont également créés avec le même nom. La clé Key Vault permet d’exécuter des opérations sur les clés et le secret Key Vault permet de récupérer la valeur du certificat en tant que secret. Un certificat Key Vault contient également des métadonnées de certificat x509 publiques.  

L’identificateur et la version de certificats sont similaires à ceux de clés et de secrets. Une version spécifique d’une clé et d’un secret adressables créés avec la version du certificat Key Vault est disponible dans la réponse de certificat Key Vault.
 
![Les certificats sont des objets complexes](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Clé exportable ou non exportable

Lorsqu’un certificat Key Vault est créé, il peut être récupéré dans le secret adressable avec la clé privée au format PFX ou PEM. La stratégie utilisée pour créer le certificat doit indiquer que la clé est exportable. Si la stratégie indique qu’elle n’est pas exportable, la clé privée ne fait pas partie de la valeur quand elle est récupérée en tant que secret.  

La clé adressable est plus pertinente avec des certificats KV non exportables. Les opérations de la clé KV adressable sont mappées à partir du champ *keyusage* de la stratégie de certificat KV utilisée pour créer le certificat KV.  

Deux types de clés sont pris en charge, *RSA* ou *RSA HSM*, avec les certificats. Exportable est autorisé avec RSA uniquement, il n’est pas pris en charge par RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Attributs et balises de certificat

En plus des métadonnées de certificat, une clé adressable, un secret adressable et un certificat Key Vault contiennent également des attributs et des balises.  

#### <a name="attributes"></a>Attributs

Les attributs de certificat sont reproduits dans des attributs de la clé et du secret adressables créés lors de la création du certificat KV.  

Un certificat Key Vault comprend les attributs suivants :  

-   *enabled* : booléen, facultatif, **true** par défaut. Peut être spécifié pour indiquer si les données du certificat peuvent être récupérées en tant que secret ou utilisables en tant que clé. Également utilisé avec *nbf* et *exp*. Quand une opération se produit entre *nbf* et *exp*, elle est autorisée seulement si enabled a la valeur true. Les opérations en dehors de la fenêtre *nbf* et *exp* sont automatiquement interdites.  

Des attributs supplémentaires en lecture seule sont inclus dans la réponse :

-   *created* : IntDate, indique quand cette version du certificat a été créée.  
-   *updated* : IntDate, indique quand cette version du certificat a été mise à jour.  
-   *exp* : IntDate, contient la valeur de la date d’expiration du certificat X.509.  
-   *nbf* : IntDate, contient la valeur de la date du certificat X.509.  

> [!Note] 
> Si un certificat Key Vault expire, sa clé et son secret adressables ne sont plus utilisables.  

#### <a name="tags"></a>Balises

 Dictionnaire de paires clé/valeur spécifié par le client, similaire aux balises dans les clés et les secrets.  

 > [!Note]
> Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet (clés, secrets ou certificats).

### <a name="certificate-policy"></a>Stratégie de certificat

Une stratégie de certificat contient des informations sur la création et la gestion du cycle de vie d’un certificat Key Vault. Lorsqu’un certificat avec une clé privée est importé dans le coffre de clés, une stratégie par défaut est créée en lisant le certificat x509.  

Quand un certificat Key Vault est créé de zéro, une stratégie doit être fournie. La stratégie spécifie comment créer cette version du certificat Key Vault ou la prochaine version. Lorsqu’une stratégie a été définie, des opérations de création successives ne sont pas nécessaires pour les prochaines versions. Il n’existe qu’une seule instance d’une stratégie pour toutes les versions d’un certificat Key Vault.  

Globalement, une stratégie de certificat contient les informations suivantes :  

-   Propriétés du certificat X.509 : contient le nom du sujet, les autres noms du sujet et d’autres propriétés utilisées pour créer une demande de certificat X.509.  
-   Propriétés des clés : contient les champs type de clé, longueur de clé, exportable et réutiliser la clé. Ces champs indiquent au coffre de clés comment générer une clé.  
-   Propriétés du secret : propriétés du secret comme le type de contenu de secret adressable pour générer la valeur du secret, pour récupérer le certificat en tant que secret.  
-   Actions de la durée de vie : actions de la durée de vie du certificat KV. Chaque action de la durée de vie contient :  

     - Déclencheur : spécifié en jours avant l’expiration ou en pourcentage de la durée de vie  

     - Action : spécifie le type d’action, *emailContacts* ou *autoRenew*  

-   Émetteur : paramètres relatifs à l’émetteur de certificat à utiliser pour émettre des certificats X.509.  
-   Attributs de stratégie : attributs liés à la stratégie  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mappage d’utilisation de clé X509 avec Key Vault

Le tableau suivant représente le mappage de la stratégie d’utilisation de la clé x509 avec des opérations sur les clés effectives d’une clé créée lors de la création d’un certificat Key Vault.

|**Indicateurs d’utilisation de la clé X509**|**Opérations sur la clé Key Vault**|**Comportement par défaut**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| N/A |
|DecipherOnly|decrypt| N/A  |
|DigitalSignature|sign, verify| Valeur par défaut de Key Vault sans spécification de l’utilisation au moment de la création du certificat | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign|sign, verify|N/A|
|KeyEncipherment|wrapKey, unwrapKey| Valeur par défaut de Key Vault sans spécification de l’utilisation au moment de la création du certificat | 
|NonRepudiation|sign, verify| N/A |
|crlsign|sign, verify| N/A |

### <a name="certificate-issuer"></a>Émetteur de certificat

Un objet certificat Key Vault conserve une configuration utilisée pour communiquer avec un fournisseur de l’émetteur de certificat sélectionné pour demander des certificats x509.  

-   Partenaires Key Vault avec les fournisseurs de l’émetteur de certificat suivants pour les certificats TLS/SSL

|**Nom du fournisseur**|**Emplacements**|
|----------|--------|
|DigiCert|Pris en charge dans tous les emplacements de service de coffre de clés dans le cloud public et Azure Government|
|GlobalSign|Pris en charge dans tous les emplacements de service de coffre de clés dans le cloud public et Azure Government|

Avant de pouvoir créer un émetteur de certificat dans un coffre de clés, les étapes préliminaires 1 et 2 suivantes doivent être exécutées.  

1. Intégrer aux fournisseurs d’autorités de certification  

    -   Un administrateur de l’organisation doit intégrer sa société (par ex. Contoso) à au moins un fournisseur d’autorité de certification.  

2. L’administrateur crée des informations d’identification du demandeur pour Key Vault afin d’inscrire (et de renouveler) des certificats TLS/SSL  

    -   Fournit la configuration à utiliser pour créer un objet émetteur du fournisseur dans le coffre de clés  

Pour plus d’informations sur la création d’objets Émetteur à partir du portail Certificats, consultez le [blog de certificats Key Vault](https://aka.ms/kvcertsblog)  

Key Vault permet de créer plusieurs objets émetteurs avec une configuration de fournisseur de l’émetteur différente. Lorsqu’un objet émetteur est créé, son nom peut être référencé dans une ou plusieurs stratégies de certificat. Le référencement de l’objet émetteur indique à Key Vault d’utiliser la configuration telle que spécifiée dans l’objet émetteur lors de la demande du certificat x509 à partir du fournisseur d’autorité de certification lors de la création ou du renouvellement du certificat.  

Les objets émetteur sont créés dans le coffre et ne peuvent être utilisés qu’avec des certificats KV dans le même coffre.  

### <a name="certificate-contacts"></a>Contacts du certificat

Les contacts du certificat contiennent des informations de contact pour l’envoi de notifications déclenchées par des événements de durée de vie de certificat. Les informations de contact sont partagées par tous les certificats dans le coffre de clés. Une notification est envoyée à tous les contacts spécifiés pour un événement pour n’importe quel certificat dans le coffre de clés.  

Si la stratégie d’un certificat est définie sur le renouvellement automatique, une notification est alors envoyée pour les événements suivants.  

- Avant le renouvellement du certificat
- Après le renouvellement du certificat, indiquant si le certificat a été renouvelé, ou si une erreur s’est produite, nécessitant un renouvellement manuel du certificat.  

  Quand la stratégie d’un certificat est définie pour un renouvellement manuel (e-mail uniquement), une notification est envoyée lorsqu’il est temps de renouveler le certificat.  

### <a name="certificate-access-control"></a>Contrôle d’accès aux certificats

 Le contrôle d’accès pour les certificats est géré par Key Vault et fourni par le coffre de clés qui contient ces certificats. La stratégie de contrôle d’accès pour les certificats est différente des stratégies de contrôle d’accès pour les clés et les secrets dans un même coffre de clés. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les certificats afin de maintenir une segmentation et une gestion des certificats appropriées au scénario.  

 Les autorisations suivantes peuvent être utilisées, par principal, dans l’entrée de contrôle d’accès aux secrets sur un coffre de clés, et reflètent précisément les opérations autorisées sur un objet secret :  

- Autorisations pour les opérations de gestion des certificats
  - *get* : obtenir la version actuelle ou n’importe quelle version d’un certificat 
  - *list* : lister les certificats actuels ou les versions d’un certificat  
  - *update* : mettre à jour un certificat
  - *create* : créer un certificat Key Vault
  - *import* : importer les éléments d’un certificat dans un certificat Key Vault
  - *delete* : supprimer un certificat, sa stratégie et toutes ses versions  
  - *recover* : récupérer un certificat supprimé
  - *backup* : sauvegarder un certificat dans un coffre de clés
  - *restore* : restaurer un certificat sauvegardé sur un coffre de clés
  - *managecontacts* : gérer les contacts du certificat Key Vault  
  - *manageissuers* : gérer les autorités/émetteurs du certificat Key Vault
  - *getissuers* : obtenir les autorités/émetteurs d’un certificat
  - *listissuers* : lister les autorités/émetteurs d’un certificat  
  - *setissuers* : créer ou mettre à jour les autorités/émetteurs d’un certificat Key Vault  
  - *deleteissuers* : supprimer les autorités/émetteurs d’un certificat Key Vault  
 
- Autorisations pour les opérations privilégiées
  - *purge* : effacer (supprimer définitivement) un certificat supprimé

Pour plus d’informations, voir [Informations de référence sur les opérations liées aux certificats dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Gestion des clés de compte de stockage Azure

Key Vault peut gérer les clés de compte de stockage Azure :

- En interne, Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure. 
- Key Vault regénère (fait tourner) les clés régulièrement.
- Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
- Key Vault gère les clés des comptes de stockage et des comptes de stockage classiques.

Pour plus d’informations, consultez [Clés de compte de stockage Azure Key Vault](key-vault-ovw-storage-keys.md).

### <a name="storage-account-access-control"></a>Contrôle d’accès aux comptes de stockage

Vous pouvez utiliser les autorisations suivantes quand vous autorisez un utilisateur ou un principal d’application à effectuer des opérations sur un compte de stockage géré :  

- Autorisations pour les opérations de définition SAS et de compte de stockage géré
  - *get* : obtenir des informations sur un compte de stockage 
  - *list* : lister les comptes de stockage gérés par un coffre de clés
  - *update* : mettre à jour un compte de stockage
  - *delete* : Suppression d'un compte de stockage  
  - *recover* : récupérer un compte de stockage supprimé
  - *backup* : sauvegarder un compte de stockage
  - *restore* : restaurer un compte de stockage sauvegardé sur un coffre de clés
  - *set* : créer ou mettre à jour un compte de stockage
  - *regeneratekey* : regénérer une valeur de clé spécifiée pour un compte de stockage
  - *getsas* : obtenir des informations sur une définition SAS pour un compte de stockage
  - *listsas* : lister les définitions SAS de stockage d’un compte de stockage
  - *deletesas* : supprimer une définition SAS d’un compte de stockage
  - *setsas* : créer ou mettre à jour une définition/des attributs SAS pour un compte de stockage

- Autorisations pour les opérations privilégiées
  - *purge* : effacer (supprimer définitivement) un compte de stockage géré

Pour plus d’informations, consultez [Informations de référence sur les opérations de compte de stockage dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Voir aussi

- [Authentification, requêtes et réponses](authentication-requests-and-responses.md)
- [Guide du développeur Key Vault](/azure/key-vault/key-vault-developers-guide)
