---
title: Présentation des clés, des secrets et des certificats
description: Vue d’ensemble de l’interface REST et informations sur le plan Développeur KV
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 2c7dd89d9c2e5d50f2533101499a6e50e52047b3
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="about-keys-secrets-and-certificates"></a>Présentation des clés, des secrets et des certificats
Azure Key Vault permet aux utilisateurs de stocker et d’utiliser des clés de chiffrement dans l’environnement Microsoft Azure. Key Vault prend en charge plusieurs types de clés et algorithmes, et permet d’utiliser des modules de sécurité matériels (HSM) pour les clés de valeur supérieure. Key Vault permet également aux utilisateurs de stocker des secrets en toute sécurité. Les secrets sont des objets octets de taille limitée sans sémantique spécifique. Key Vault prend également en charge les certificats, qui sont basés sur des clés et secrets, et qui offrent une fonctionnalité de renouvellement automatique.

Pour plus d’informations sur Azure Key Vault, consultez [Présentation d’Azure Key Vault](/azure/key-vault/key-vault-whatis)

**Informations générales sur Key Vault**

-   [Prise en charge des normes](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Types de données](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objets, identificateurs et contrôle de version](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**À propos des clés**

-   [Clés et types de clés](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algorithmes RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algorithmes RSA-HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Protection par chiffrement](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Opérations sur les clés](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Attributs de clé](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Balises de clé](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**À propos des secrets** 

-   [Utilisation de secrets](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Attributs de secret](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Balises de secret](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Contrôle d’accès aux secrets](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**À propos des certificats**

-   [Composition d’un certificat](#BKMK_CompositionOfCertificate)  
-   [Attributs et balises de certificat](#BKMK_CertificateAttributesAndTags)  
-   [Stratégie de certificat](#BKMK_CertificatePolicy)  
-   [Émetteur de certificat](#BKMK_CertificateIssuer)  
-   [Contacts du certificat](#BKMK_CertificateContacts)  
-   [Contrôle d’accès aux certificats](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Informations générales sur Key Vault

Les sections suivantes fournissent des informations générales applicables sur l’implémentation du service Azure Key Vault.

###  <a name="BKMK_Standards"></a> Prise en charge des normes

Les spécifications JavaScript Object Notation (JSON) and JavaScript Object Signing and Encryption (JOSE) sont des informations d’arrière-plan importantes.  

-   [Clé web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Chiffrement web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorithmes web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Signature web JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Types de données

Reportez-vous aux [spécifications JOSE](#BKMK_Standards) pour les types de données appropriés pour les clés, le chiffrement et la signature.  

-   **algorithm** : algorithme pris en charge pour une opération sur les clés, RSA1_5 par exemple  
-   **ciphertext-value** : octets de texte de chiffrement, codés avec Base64URL  
-   **digest-value** : sortie d’un algorithme de hachage, codée avec Base64URL  
-   **key-type** : un des types de clés pris en charge, RSA par exemple.  
-   **plaintext-value** : octets de texte en clair, codés avec Base64URL  
-   **signature-value** : sortie d’un algorithme de signature, codée avec Base64URL  
-   **base64URL** : valeur binaire codée Base64URL [RFC4648]  
-   **boolean** : true (vrai) ou false (faux)  
-   **Identity** : identité d’Azure Active Directory (AAD).  
-   **IntDate** : valeur décimale JSON représentant le nombre de secondes entre 1970-01-01T0:0:0Z UTC et la date/heure UTC spécifiée. Consultez [RFC3339] pour plus d’informations sur les dates/heures en général et UTC en particulier.  

###  <a name="BKMK_ObjId"></a> Objets, identificateurs et contrôle de version

Les objets stockés dans Azure Key Vault conservent les versions chaque fois qu’une nouvelle instance d’un objet est créée, et chaque version a un identificateur et une URL uniques. Lors de la création d’un objet, il se voit attribuer un identificateur de version unique et est marqué comme version actuelle de l’objet. La création d’une nouvelle instance portant le même nom d’objet attribue au nouvel objet un identificateur de version unique et en fait la version actuelle.  

Les objets dans Azure Key Vault peuvent être adressés à l’aide de l’identificateur actuel ou d’un identificateur spécifique à la version. Par exemple, pour une clé nommée « MasterKey », l’exécution d’opérations avec l’identificateur actuel amène le système à utiliser la dernière version disponible. L’exécution d’opérations avec l’identificateur spécifique à la version amène le système à utiliser cette version spécifique de l’objet.  

Les objets sont identifiés de manière unique dans Azure Key Vault à l’aide d’une URL de sorte que deux objets dans le système, quel que soit leur emplacement géographique, n’ont pas la même URL. L’URL complète d’un objet est appelée l’identificateur d’objet et se compose d’un préfixe identifiant le Key Vault, du type d’objet, d’un nom d’objet fourni par l’utilisateur et d’une version d’objet. Le nom d’objet n’est pas sensible à la casse et est non modifiable. Les identificateurs qui n’incluent pas la version d’objet sont appelés des identificateurs de base.  

Pour plus d’informations, consultez [Authentification, requêtes et réponses](authentication-requests-and-responses.md)

Un identificateur d’objet a le format général suivant :  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Où :  

|||  
|-|-|  
|`keyvault-name`|Le nom d’un coffre de clés dans le service Microsoft Azure Key Vault.<br /><br /> Les noms de Key Vault sont choisis par l’utilisateur et sont globalement uniques.<br /><br /> Le nom de Key Vault doit être une chaîne comprise entre 3 et 24 caractères. Il doit contenir uniquement des chiffres, des lettres et des tirets (0-9, a-z, A-Z et -).|  
|`object-type`|Le type de l’objet, « clés » ou « secrets ».|  
|`object-name`|Un `object-name` est un nom fourni par l’utilisateur et doit être unique dans un Key Vault. Le nom doit être une chaîne comprise entre 1 et 127 caractères. Il doit contenir uniquement des chiffres, des lettres et des tirets : 0-9, a-z, A-Z et -.|  
|`object-version`|Un `object-version` est un identificateur de chaîne de 32 caractères généré par le système qui peut être utilisé pour adresser une version unique d’un objet.|  

## <a name="key-vault-keys"></a>Clés Key Vault

###  <a name="BKMK_KeyTypes"></a> Clés et types de clés

Les clés de chiffrement dans Azure Key Vault sont représentées en tant qu’objets de clé web JSON [JWK]. Les spécifications JWK/JWA de base sont également étendues pour activer des types de clés spécifiques à l’implémentation d’Azure Key Vault, par exemple l’importation de clés dans Azure Key Vault à l’aide de l’empaquetage spécifique du fournisseur HSM (Thales) afin de permettre un transport sécurisé de clés telles que celles ne pouvant être utilisées que dans les HSM Azure Key Vault.  

La version initiale d’Azure Key Vault prend en charge les clés RSA uniquement. Les versions ultérieures pourront prendre en charge d’autres types de clés comme les clés symétriques et courbe elliptique.  

-   **RSA** : clé RSA de 2 048 bits. Il s’agit d’une clé « logicielle », qui est traitée dans le logiciel par Key Vault, mais qui est stockée chiffrée au repos à l’aide d’une clé système qui se trouve dans un HSM. Les clients peuvent importer une clé RSA existante ou demander à Azure Key Vault d’en générer une.  
-   **RSA-HSM** : clé RSA qui est traitée dans un HSM. Les clés RSA-HSM sont protégées dans un des mondes de sécurité HSM Azure Key Vault (il existe un monde de sécurité par emplacement géographique afin de garantir l’isolation). Les clients peuvent importer une clé RSA, sous forme logicielle ou en exportant depuis un appareil HSM compatible, ou demander à Azure Key Vault d’en générer une. Ce type de clé ajoute l’attribut T à la JWK pour le transport du matériel de clé HSM.  

     Pour plus d’informations sur les frontières géographiques, consultez [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algorithmes RSA  
 Les identificateurs d’algorithme suivants sont pris en charge avec les clés RSA dans Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5** : chiffrement à clé RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** : RSAES utilisant OAEP (Optimal Asymmetric Encryption Padding) [RFC3447], avec les paramètres par défaut spécifiés par RFC 3447, section A.2.1. Ces paramètres par défaut utilisent une fonction de hachage de SHA-1 et une fonction de génération de masque de MGF1 avec SHA-1.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** : RSASSA-PKCS-v1_5 utilisant SHA-256. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-256 et doit être d’une longueur de 32 octets.  
-   **RS384** : RSASSA-PKCS-v1_5 utilisant SHA-384. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-384 et doit être d’une longueur de 48 octets.  
-   **RS512** : RSASSA-PKCS-v1_5 utilisant SHA-512. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-512 et doit être d’une longueur de 64 octets.  
-   **RSNULL** : consultez [RFC2437], un cas d’utilisation spécial permettant certains scénarios TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algorithmes RSA-HSM  
Les identificateurs d’algorithme suivants sont pris en charge avec les clés RSA-HSM dans Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Protection par chiffrement

Les modules de chiffrement utilisé par Azure Key Vault, HSM ou logiciels, sont conformes aux normes FIPS (Federal Information Processing Standard). Aucune action spéciale ne doit être effectuée pour l’exécution en mode FIPS. Si vous **créez** ou **importez** des clés protégées par HSM, leur traitement dans des HSM conformes à FIPS 140-2 niveau 2 ou supérieur est garanti. Si vous **créez** ou **importez** clés protégées par logiciel, elles sont traitées dans des modules de chiffrement conformes à FIPS 140-2 niveau 1 ou supérieur. Pour plus d’informations, consultez [Clés et types de clés](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>WRAP/UNWRAP, ENCRYPT/DECRYPT

-   **RSA1_5** : chiffrement à clé RSAES-PKCS1-V1_5 [RFC3447].  
-   **RSA-OAEP** : RSAES utilisant OAEP (Optimal Asymmetric Encryption Padding) [RFC3447], avec les paramètres par défaut spécifiés par RFC 3447, section A.2.1. Ces paramètres par défaut utilisent une fonction de hachage de SHA-1 et une fonction de génération de masque de MGF1 avec SHA-1.  

 #### <a name="signverify"></a>SIGN/VERIFY  

-   **RS256** : RSASSA-PKCS-v1_5 utilisant SHA-256. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-256 et doit être d’une longueur de 32 octets.  
-   **RS384** : RSASSA-PKCS-v1_5 utilisant SHA-384. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-384 et doit être d’une longueur de 48 octets.  
-   **RS512** : RSASSA-PKCS-v1_5 utilisant SHA-512. La valeur de synthèse fournie par l’application doit être calculée à l’aide de SHA-512 et doit être d’une longueur de 64 octets.  
-   RSNULL : consultez [RFC2437], un cas d’utilisation spécial permettant certains scénarios TLS.  

###  <a name="BKMK_KeyOperations"></a> Opérations sur les clés

Azure Key Vault prend en charge les opérations sur les objets clés suivantes :  

-   **Create** : permet à un client de créer une clé dans Azure Key Vault. La valeur de la clé est générée par Azure Key Vault et stockée, mais n’est pas communiquée au client. Des clés asymétriques (et à l’avenir, courbe elliptique et symétriques) peuvent être créées dans Azure Key Vault.  
-   **Import** : permet à un client d’importer une clé existante dans Azure Key Vault. Des clés asymétriques (et à l’avenir, courbe elliptique et symétriques) peuvent être importées dans Azure Key Vault selon diverses méthodes d’empaquetage dans une construction JWK.  
-   **Update** : permet à un client disposant d’autorisations suffisantes de modifier les métadonnées (attributs de clé) associées à une clé précédemment stockée dans Azure Key Vault.  
-   **Delete** : permet à un client disposant d’autorisations suffisantes de supprimer une clé d’Azure Key Vault.  
-   **List** : permet à un client de répertorier toutes les clés dans un Azure Key Vault donné.  
-   **List versions** : permet à un client de répertorier toutes les versions d’une clé donnée dans un Azure Key Vault donné.  
-   **Get** : permet à un client de récupérer les parties publiques d’une clé donnée dans un Azure Key Vault.  
-   **Backup** : permet d’exporter une clé sous une forme protégée.  
-   **Restore** : permet d’importer une clé précédemment sauvegardée.  

Pour plus d’informations, consultez [Opérations sur les clés](/rest/api/keyvault/key-operations.md)  

Lorsqu’une clé a été créée dans Azure Key Vault, les opérations de chiffrement suivantes peuvent être exécutées à l’aide de la clé :  

-   **Sign et Verify** : cette opération vise à « signer le hachage » ou à « vérifier le hachage » car Azure Key Vault ne prend pas en charge le hachage du contenu lors la création de la signature. Les applications doivent hacher les données à signer localement puis demander à Azure Key Vault de signer le hachage. La vérification des hachages signés est prise en charge en tant qu’opération pratique pour les applications n’ayant pas accès au matériel de clé [public] ; nous vous recommandons, pour de meilleures performances de l’application, d’exécuter les opérations de vérification localement.  
-   **Key Encryption / Wrapping** : une clé stockée dans Azure Key Vault peut être utilisée pour protéger une autre clé, généralement une clé de chiffrement symétrique de contenu (CEK). Lorsque la clé dans Azure Key Vault est asymétrique, le chiffrement de clé est utilisé, par exemple RSA-OAEP, et les opérations WRAPKEY/UNWRAPKEY sont équivalentes à ENCRYPT/DECRYPT. Lorsque la clé dans Azure Key Vault est symétrique, l’habillage de clé est utilisé, par exemple AES-KW. L’opération WRAPKEY est prise en charge en tant qu’opération pratique pour les applications n’ayant pas accès au matériel de clé [public] ; il est recommandé, pour de meilleures performances de l’application, que les opérations WRAPKEY soient exécutées localement.  
-   **Encrypt et Decrypt** : une clé stockée dans Azure Key Vault peut être utilisée pour chiffrer ou déchiffrer un bloc de données unique, dont la taille est déterminée par le type de clé et l’algorithme de chiffrement sélectionné. L’opération Encrypt est fournie à titre pratique pour les applications n’ayant pas accès au matériel de clé [public] ; il est recommandé, pour de meilleures performances de l’application, que les opérations de chiffrement soient exécutées localement.  

Bien que les opérations WRAPKEY/UNWRAPKEY utilisant des clés asymétriques puissent sembler superflues car équivalentes à ENCRYPT/DECRYPT, l’utilisation d’opérations distinctes est jugée importante pour offrir une sémantique, la séparation de l’autorisation de ces opérations et la cohérence lorsque d’autres types de clés sont pris en charge par le service.  

Azure Key Vault ne prend pas en charge les opérations EXPORT : lorsqu’une clé est configurée dans le système, elle ne peut pas être extraite et son matériel de clé ne peut pas être modifié.  Les utilisateurs d’Azure Key Vault pouvant avoir besoin de leur clé dans d’autres cas d’utilisation, ou après sa suppression d’Azure Key Vault, peuvent toutefois utiliser les opérations BACKUP et RESTORE pour exporter/importer la clé sous une forme protégée. Les clés créées par l’opération BACKUP ne peuvent pas être utilisées en dehors d’Azure Key Vault. L’opération IMPORT peut également être utilisée sur plusieurs instances d’Azure Key Vault.  

Les utilisateurs peuvent limiter les opérations de chiffrement prises en charge par Azure Key Vault, par clé, à l’aide de la propriété key_ops de l’objet JWK.  

Pour plus d’informations sur les objets JWK, consultez [Clé web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Attributs de clé

Les attributs suivants peuvent être spécifiés en plus du matériel de clé. Dans une requête JSON, le mot clé attributes et les accolades, {}, sont requis même si aucun attribut n’est spécifié.  

- *enabled* : booléen, facultatif, **true** par défaut. Spécifie si la clé est activée et peut être utilisée pour des opérations de chiffrement. L’attribut *enabled* est utilisé avec *nbf* et *exp*. Lorsqu’une opération se produit entre *nbf* et *exp*, elle n’est autorisée que si *enabled* est défini sur **true**. Les opérations en dehors de la fenêtre *nbf* / *exp* sont automatiquement interdites, à l’exception de certains types d’opérations dans des [conditions particulières](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *nbf* : IntDate, facultatif, « now » par défaut. L’attribut *nbf* (not before ou pas avant) identifie l’heure avant laquelle la clé NE DOIT PAS être utilisée pour des opérations de chiffrement, à l’exception de certains types d’opérations dans des [conditions particulières](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Le traitement de l’attribut *nbf* requiert que la date/heure actuelle SOIT postérieure ou égale à la date/heure « not-before » (pas avant) indiquée dans l’attribut *nbf*. Azure Key Vault PEUT prévoir une légère marge, généralement pas plus de quelques minutes, pour prendre en compte la variation d’horloge. Sa valeur DOIT être un nombre contenant une valeur IntDate.  
- *exp* : IntDate, facultatif, « forever » par défaut. L’attribut *exp* (heure d’expiration) identifie l’heure à ou après laquelle la clé NE DOIT PAS être utilisée pour une opération de chiffrement, à l’exception de certains types d’opérations dans des [conditions particulières](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Le traitement de l’attribut *exp* requiert que la date/heure actuelle SOIT antérieure à la date/heure d’expiration indiquée dans l’attribut *exp*. Azure Key Vault PEUT prévoir une légère marge, généralement pas plus de quelques minutes, pour prendre en compte la variation d’horloge. Sa valeur DOIT être un nombre contenant une valeur IntDate.  

Des attributs supplémentaires en lecture seule sont inclus dans toute réponse contenant des attributs de clé :  

- *created* : IntDate, facultatif. L’attribut *created* indique quand cette version de la clé a été créée. Cette valeur est null pour les clés créées avant l’ajout de cet attribut. Sa valeur DOIT être un nombre contenant une valeur IntDate.  
- *updated* : IntDate, facultatif. L’attribut *updated* indique quand cette version de la clé a été mise à jour. Cette valeur est null pour les clés qui ont été mises à jour pour la dernière fois avant l’ajout de cet attribut. Sa valeur DOIT être un nombre contenant une valeur IntDate.  

Pour plus d’informations sur IntDate et d’autres types de données, consultez [Types de données](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Activités contrôlées par date/heure

Les clés pas encore valides ou ayant expiré, celles en dehors de la fenêtre *nbf* / *exp*, s’appliqueront pour les opérations **decrypt**, **unwrap** et **verify** (elles ne retourneront pas d’erreur 403, Forbidden). La logique d’attribution d’utilisation de l’état « not-yet-valid » (pas encore valide) consiste à autoriser le test de la clé avant son utilisation en production. La logique d’attribution d’utilisation de l’état « expired » (expiré) consiste à autoriser des opérations de récupération sur des données qui ont été créées alors que la clé était valide. Vous pouvez également désactiver l’accès à une clé à l’aide de stratégies de Key Vault, ou en définissant l’attribut de clé *enabled* sur **false**.

Pour plus d’informations sur les types de données, consultez [Types de données](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Pour plus d’informations sur les autres attributs possibles, consultez [Clé web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Balises de clé

Vous pouvez spécifier des métadonnées spécifiques à l’application supplémentaires sous la forme de balises. Azure Key Vault prend en charge jusqu’à 15 balises, chacune d’entre elles pouvant avoir un nom de 256 caractères et une valeur de 256 caractères.  

>[!Note]
>Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet, les clés, les secrets ou les certificats.

###  <a name="BKMK_KeyAccessControl"></a> Contrôle d’accès aux clés

Le contrôle d’accès pour les clés géré par Key Vault est fourni au niveau d’un Key Vault qui agit comme le conteneur de clés. Il existe une stratégie de contrôle d’accès pour les clés différente de la stratégie de contrôle d’accès pour les secrets dans un même Key Vault. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les clés et doivent maintenir une segmentation et une gestion des clés appropriées au scénario. Le contrôle d’accès pour les clés est indépendant du contrôle d’accès pour les secrets.  

Les autorisations suivantes peuvent être accordées, par utilisateur/principal du service, dans l’entrée du contrôle d’accès aux clés sur un coffre. Ces autorisations reflètent précisément les opérations autorisées sur un objet clé :  

-   *create* : créer de nouvelles clés
-   *get* : lire la partie publique d’une clé, ainsi que ses attributs
-   *list* : répertorier les clés ou les versions d’une clé stockée dans un coffre de clés
-   *update* : mettre à jour les attributs d’une clé
-   *delete* : supprimer l’objet clé
-   *sign* : utiliser la clé pour signer des synthèses
-   *verify* : utiliser la clé pour vérifier des synthèses
-   *wrapKey* : utiliser la clé pour protéger une clé symétrique
-   *unwrapKey* : utiliser la clé pour supprimer la protection d’une clé symétrique
-   *encrypt* : utiliser la clé pour protéger une séquence arbitraire d’octets
-   *decrypt* : utiliser la clé pour annuler la protection d’une séquence d’octets
-   *import* : importer une clé dans un coffre de clés
-   *backup* : sauvegarder une clé dans un coffre de clés
-   *restore* : restaurer une clé sauvegardée sur un coffre de clés
-   *all* : toutes les autorisations

## <a name="key-vault-secrets"></a>Secrets Key Vault 

###  <a name="BKMK_WorkingWithSecrets"></a> Utilisation de secrets

Les secrets dans Azure Key Vault sont des séquences d’octets d’une taille maximale de 25 Ko. Le service Azure Key Vault ne fournit pas de sémantique pour les secrets. Il accepte simplement les données, les chiffre et les stocke, puis renvoie un identificateur secret, « id », qui peut être utilisé pour récupérer le secret ultérieurement.  

Pour les données hautement sensibles, les clients doivent envisager des couches supplémentaires de protection des données stockées dans Azure Key Vault ; par exemple, en chiffrant au préalable les données à l’aide d’une clé de protection distincte.  

Azure Key Vault prend également en charge un champ contentType pour les secrets. Les clients peuvent spécifier le type de contenu, « contentType », d’un secret pour simplifier l’interprétation des données du secret lors de leur récupération. La longueur maximale de ce champ est de 255 caractères. Il n’existe aucune valeur prédéfinie. L’utilisation suggérée est donnée à titre de conseil pour interpréter les données du secret. Par exemple, une implémentation peut stocker des mots de passe et de certificats en tant que secret. Utilisez donc ce champ pour les différencier. Il n’existe aucune valeur prédéfinie.  

###  <a name="BKMK_SecretAttrs"></a> Attributs de secret

Les attributs suivants peuvent être spécifiés en plus des données du secret :  

- *exp* : IntDate, facultatif, **forever** par défaut. L’attribut *exp* (heure d’expiration) identifie l’heure d’expiration à ou après laquelle les données du secret NE DOIVENT PAS être récupérées, sauf dans des [conditions particulières](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Le traitement de l’attribut *exp* requiert que la date/heure actuelle SOIT antérieure à la date/heure d’expiration indiquée dans l’attribut *exp*. Azure Key Vault PEUT prévoir une légère marge, généralement pas plus de quelques minutes, pour prendre en compte la variation d’horloge. Sa valeur DOIT être un nombre contenant une valeur IntDate.  
- *nbf* : IntDate, facultatif, **now** par défaut. L’attribut *nbf* (not before ou pas avant) identifie l’heure avant laquelle les données du secret NE DOIVENT PAS être récupérées, sauf dans des [conditions particulières](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Le traitement de l’attribut *nbf* requiert que la date/heure actuelle SOIT postérieure ou égale à la date/heure « not-before » (pas avant) indiquée dans l’attribut *nbf*. Azure Key Vault PEUT prévoir une légère marge, généralement pas plus de quelques minutes, pour prendre en compte la variation d’horloge. Sa valeur DOIT être un nombre contenant une valeur IntDate.  
- *enabled* : booléen, facultatif, **true** par défaut. Cet attribut spécifie si les données du secret peuvent être récupérées ou non. L’attribut enabled est utilisé avec nbf et *exp* lorsqu’une opération se produit entre nbf et exp, elle ne sera autorisée que si enabled est défini sur **true**. Les opérations en dehors de la fenêtre *nbf* et *exp* sont automatiquement interdites, sauf dans des [conditions particulières](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Des attributs supplémentaires en lecture seule sont inclus dans toute réponse contenant des attributs de secret :  

- *created* : IntDate, facultatif. L’attribut created indique quand cette version du secret a été créée. Cette valeur est null pour les secrets créés avant l’ajout de cet attribut. Sa valeur doit être un nombre contenant une valeur IntDate.  
- *updated* : IntDate, facultatif. L’attribut updated indique quand cette version du secret a été mise à jour. Cette valeur est null pour les secrets qui ont été mis à jour pour la dernière fois avant l’ajout de cet attribut. Sa valeur doit être un nombre contenant une valeur IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Activités contrôlées par date/heure

L’opération **get** d’un secret s’appliquera aux secrets pas encore valides ou ayant expiré, en dehors de la fenêtre *nbf* / *exp*. L’appel de l’opération **get** d’un secret, pour un secret pas encore valide, peut être utilisé à des fins de test. La récupération (opération **get**) d’un secret ayant expiré, peut être utilisée pour des opérations de récupération.

Pour plus d’informations sur les types de données, consultez [Types de données](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Contrôle d’accès aux secrets

Le contrôle d’accès pour les secrets géré dans Azure Key Vault est fourni au niveau d’un Key Vault qui agit comme le conteneur de ces secrets. Il existe une stratégie de contrôle d’accès pour les secrets différente de la stratégie de contrôle d’accès pour les clés dans un même Key Vault. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les secrets et doivent maintenir une segmentation et une gestion des secrets appropriées au scénario. Le contrôle d’accès pour les secrets est indépendant du contrôle d’accès pour les clés.  

Les autorisations suivantes peuvent être utilisées, par principal, dans l’entrée de contrôle d’accès aux secrets sur un coffre, et reflètent précisément les opérations autorisées sur un objet secret :  

-   *set* : créer de nouveaux secrets  
-   *get* : lire un secret  
-   *list* : répertorier les secrets ou les versions d’un secret stocké dans un Key Vault  
-   *delete* : supprimer le secret  
-   *all* : toutes les autorisations  

Pour plus d’informations sur l’utilisation des secrets, consultez [Opérations sur les secrets](/rest/api/keyvault/secret-operations.md).  

###  <a name="BKMK_SecretTags"></a> Balises de secret  
Vous pouvez spécifier des métadonnées spécifiques à l’application supplémentaires sous la forme de balises. Azure Key Vault prend en charge jusqu’à 15 balises, chacune d’entre elles pouvant avoir un nom de 256 caractères et une valeur de 256 caractères.  

>[!Note]
>Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet, les clés, les secrets ou les certificats.

## <a name="key-vault-certificates"></a>Certificats Key Vault

La prise en charge des certificats Key Vault permet de gérer vos certificats x509 et les comportements suivants :  

-   Permet à un propriétaire de certificat de créer un certificat via un processus de création de Key Vault ou l’importation d’un certificat existant. Cela concerne à la fois les certificats auto-signés et ceux générés par une autorité de certification.
-   Permet à un propriétaire de certificat Key Vault d’implémenter le stockage sécurisé et la gestion des certificats X509 sans interaction avec des éléments de clé privée.  
-   Permet à un propriétaire de certificat de créer une stratégie qui ordonne à Key Vault de gérer le cycle de vie d’un certificat.  
-   Permet aux propriétaires de certificat de fournir des informations de contact pour les notifications concernant des événements d’expiration du cycle de vie et le renouvellement de certificat.  
-   Prend en charge le renouvellement automatique avec des émetteurs sélectionnés : fournisseurs de certificats X509 de partenaire Key Vault/autorités de certification.

>[!Note]
>Les fournisseurs/autorités non partenaires sont également autorisés, mais ils ne prendront pas en charge la fonctionnalité de renouvellement automatique.

###  <a name="BKMK_CompositionOfCertificate"></a> Composition d’un certificat

Lorsqu’un certificat Key Vault est créé, une clé et un secret adressables sont également créés avec le même nom. La clé Key Vault permet d’exécuter des opérations sur les clés et le secret Key Vault permet de récupérer la valeur du certificat en tant que secret. Un certificat Key Vault contient également des métadonnées de certificat x509 publiques.  

L’identificateur et la version de certificats sont similaires à ceux de clés et de secrets. Une version spécifique d’une clé et d’un secret adressables créés avec la version du certificat Key Vault est disponible dans la réponse de certificat Key Vault.
 
![Les certificats sont des objets complexes](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Clé exportable ou non exportable

Lorsqu’un certificat Key Vault est créé, il peut être récupéré dans le secret adressable avec la clé privée au format PFX ou PEM si la stratégie utilisée pour créer le certificat indique que la clé est exportable. Si la stratégie utilisée pour créer le certificat Key Vault indique que la clé est non exportable, la clé privée n’est pas incluse dans la valeur lorsqu’elle est récupérée en tant que secret.  

La clé adressable est plus pertinente avec des certificats KV non exportables. Les opérations de la clé KV adressable sont mappées à partir du champ *keyusage* de la stratégie de certificat KV utilisée pour créer le certificat KV.  

Deux types de clés sont pris en charge, *RSA* ou *RSA HSM*, avec les certificats. Exportable est autorisé avec RSA uniquement, il n’est pas pris en charge par RSA HSM.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Attributs et balises de certificat

Outre des métadonnées de certificat, une clé adressable, un secret adressable et un certificat Key Vault contiennent également des attributs et des balises.  

#### <a name="attributes"></a>Attributs

Les attributs de certificat sont reproduits dans des attributs de la clé et du secret adressables créés lors de la création du certificat KV.  

Un certificat Key Vault comprend les attributs suivants :  

-   *enabled* : booléen, facultatif, **true** par défaut. Cet attribut peut être spécifié pour indiquer si les données du certificat peuvent être récupérées en tant que secret ou utilisables en tant que clé. Il est utilisé avec *nbf* and *exp* lorsqu’une opération se produit entre *nbf* et exp, elle ne sera autorisée que si enabled est défini sur true. Les opérations en dehors de la fenêtre *nbf* et *exp* sont automatiquement interdites.  

Des attributs supplémentaires en lecture seule sont inclus dans la réponse :

-   *created* : InDate, indique quand cette version du certificat a été créée.  
-   *updated* : InDate, indique quand cette version du certificat a été mise à jour.  
-   *exp* : IntDate, contient la valeur de la date d’expiration du certificat x509.  
-   *nbf* : IntDate, contient la valeur de la date du certificat x509.  

> [!Note] 
> Si un certificat Key Vault expire, sa clé et son secret adressables ne sont plus utilisables.  

#### <a name="tags"></a>Balises

 Dictionnaire de paires clé/valeur spécifié par le client, similaire aux balises dans les clés et les secrets.  

 > [!Note]
> Les balises peuvent être lues par un appelant s’il dispose de l’autorisation *list* ou *get* sur ce type d’objet, les clés, les secrets ou les certificats.

###  <a name="BKMK_CertificatePolicy"></a> Stratégie de certificat

Une stratégie de certificat contient des informations sur la création et la gestion du cycle de vie d’un certificat KV. Lorsqu’un certificat avec une clé privée est importé dans le coffre de clés, une stratégie par défaut est créée en lisant le certificat x509.  

Lorsqu’un certificat KV est créé à partir de zéro, une stratégie doit être fournie à Key Vault pour lui indiquer comment créer cette version du certificat KV ou la version suivante du certificat KV. Lorsqu’une stratégie a été définie, il n’est pas nécessaire de créer des versions suivantes du certificat KV à l’aide d’autres opérations create.  

Il n’existe qu’une seule instance d’une stratégie pour toutes les versions d’un certificat KV.  

À un niveau élevé, une stratégie de certificat contient les éléments suivants :  

-   Propriétés du certificat X509 : nom du sujet, autres noms du sujet, etc. utilisées pour créer une demande de certificat x509.  
-   Propriétés de la clé : type de clé, longueur de clé, clé exportable et clé à réutiliser. Ces champs indiquent au coffre de clés comment générer une clé.  
-   Propriétés du secret : propriétés du secret comme le type de contenu de secret adressable pour générer la valeur du secret, pour récupérer le certificat en tant que secret.  
-   Actions de la durée de vie : actions de la durée de vie du certificat KV. Chaque action de la durée de vie contient :  

     - Déclencheur : spécifié en jours avant l’expiration ou en pourcentage de la durée de vie  

     - Action : spécifie le type d’action, *emailContacts* ou *autoRenew*  

-   Émetteur : paramètres relatifs à l’émetteur de certificat à utiliser pour émettre des certificats x509.  
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

###  <a name="BKMK_CertificateIssuer"></a> Émetteur de certificat

Un objet certificat Key Vault conserve une configuration utilisée pour communiquer avec un fournisseur de l’émetteur de certificat sélectionné pour demander des certificats x509.  

-   Partenaires Key Vault avec les fournisseurs de l’émetteur de certificat suivants pour les certificats SSL

|**Nom du fournisseur**|**Emplacements**|
|----------|--------|
|DigiCert|Pris en charge dans tous les emplacements de service de coffre de clés dans le cloud public et Azure Government|
|GlobalSign|Pris en charge dans tous les emplacements de service de coffre de clés dans le cloud public et Azure Government|

Avant de pouvoir créer un émetteur de certificat dans un Key Vault, les étapes préliminaires 1 et 2 suivantes doivent être exécutées.  

1. Intégrer aux fournisseurs d’autorités de certification  

    -   Un administrateur de l’organisation doit intégrer sa société (par ex. Contoso) à au moins un fournisseur d’autorité de certification.  

2. L’administrateur crée des informations d’identification du demandeur pour Key Vault afin d’inscrire (et de renouveler) des certificats SSL  

    -   Fournit la configuration à utiliser pour créer un objet émetteur du fournisseur dans le coffre de clés  

Pour plus d’informations sur la création d’objets Émetteur à partir du portail Certificats, consultez le [blog de certificats Key Vault](http://aka.ms/kvcertsblog)  

Key Vault permet de créer plusieurs objets émetteurs avec une configuration de fournisseur de l’émetteur différente. Lorsqu’un objet émetteur est créé, son nom peut être référencé dans une ou plusieurs stratégies de certificat. Le référencement de l’objet émetteur indique à Key Vault d’utiliser la configuration telle que spécifiée dans l’objet émetteur lors de la demande du certificat x509 à partir du fournisseur d’autorité de certification lors de la création ou du renouvellement du certificat.  

Les objets émetteur sont créés dans le coffre et ne peuvent être utilisés qu’avec des certificats KV dans le même coffre.  

###  <a name="BKMK_CertificateContacts"></a> Contacts du certificat

Les contacts du certificat contiennent des informations de contact pour l’envoi de notifications déclenchées par des événements de durée de vie de certificat. Les informations de contact sont partagées par tous les certificats dans le coffre de clés. Une notification est envoyée à tous les contacts spécifiés pour un événement pour n’importe quel certificat dans le coffre de clés.  

Si la stratégie d’un certificat est définie sur le renouvellement automatique, une notification est alors envoyée pour les événements suivants.  

-   Avant le renouvellement du certificat
-   Après le renouvellement du certificat, indiquant si le certificat a été renouvelé, ou si une erreur s’est produite, nécessitant un renouvellement manuel du certificat.  

 Si la stratégie d’un certificat est définie pour un renouvellement manuel (courrier électronique uniquement), une notification est alors envoyée lorsqu’il est temps de renouveler le certificat.  

###  <a name="BKMK_CertificateAccessControl"></a> Contrôle d’accès aux certificats

 Le contrôle d’accès pour les certificats est géré par Key Vault et fourni au niveau d’un Key Vault qui agit comme le conteneur de ces certificats. Il existe une stratégie de contrôle d’accès pour les certificats différente de la stratégie de contrôle d’accès pour les clés dans un même Key Vault. Les utilisateurs peuvent créer un ou plusieurs coffres pour stocker les certificats et doivent maintenir une segmentation et une gestion des certificats appropriées au scénario.  

 Les autorisations suivantes peuvent être utilisées, par principal, dans l’entrée de contrôle d’accès aux secrets sur un coffre de clés, et reflètent précisément les opérations autorisées sur un objet secret :  

-   *get* : permet d’obtenir la version du certificat actuelle ou n’importe quelle version d’un certificat 
-   *list* : permet de répertorier les certificats actuels ou les versions d’un certificat  
-   *delete* : permet de supprimer un certificat, sa stratégie et toutes ses versions  
-   *create* : permet de créer un certificat Key Vault.  
-   *import* : permet d’importer du matériel de certificat dans un certificat Key Vault.  
-   *update* : permet de mettre à jour un certificat.  
-   *manageconnects* : permet de gérer les contacts du certificat Key Vault  
-   *getissuers* : permet d’obtenir les émetteurs d’un certificat  
-   *listissuers* : permet de répertorier les émetteurs du certificat  
-   *setissuers* : permet de créer ou de mettre à jour les émetteurs de certificat Key Vault  
-   *deleteissuers* : permet de supprimer les émetteurs de certificat Key Vault  
-   *all* : permet d’accorder toutes les autorisations  

## <a name="additional-information-for-certificates"></a>Informations supplémentaires pour les certificats

- [Certificats et stratégies](/rest/api/keyvault/certificates-and-policies.md)
- [Émetteurs de certificat](/rest/api/keyvault/certificate-issuers.md)
- [Contacts du certificat](/rest/api/keyvault/certificate-contacts.md)

## <a name="see-also"></a>Voir aussi

- [Authentification, requêtes et réponses](authentication-requests-and-responses.md)
- [Versions de Key Vault](key-vault-versions.md)
- [Guide du développeur Key Vault](/azure/key-vault/key-vault-developers-guide)
