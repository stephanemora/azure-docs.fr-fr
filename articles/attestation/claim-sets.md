---
title: Ensembles de revendications d’Azure Attestation
description: Ensembles de revendications d’Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: afe2cf288cd4a15091e8278309b3ecf74a2d35a4
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572746"
---
# <a name="claim-sets"></a>Ensembles de revendications

Les revendications générées dans le processus d’attestation d’enclaves à l’aide de Microsoft Azure Attestation peuvent être réparties dans les catégories ci-dessous :

- **Revendications entrantes** : Revendications générées par Microsoft Azure Attestation après l’analyse de la preuve d’attestation et qui peuvent être utilisées par les auteurs de stratégie pour définir des règles d’autorisation dans une stratégie personnalisée

- **Revendications sortantes** : Revendications générées par Azure Attestation et contenant toutes les revendications qui se trouvent dans le jeton d’attestation

- **revendications de propriété** : revendications créées en tant que sortie par Azure Attestation. Cette catégorie contient toutes les revendications qui représentent les propriétés du jeton d’attestation, telles que l’encodage du rapport, la durée de validité du rapport, etc.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Revendications entrantes communes à tous les types d’attestations

Les revendications ci-dessous sont générées par Azure Attestation et peuvent être utilisées pour définir des règles d’autorisation dans une stratégie personnalisée :
- **x-ms-ver** : version du schéma JWT (valeur « 1.0 » attendue)
- **x-ms-attestation-type** : valeur de chaîne représentant le type d’attestation 
- **x-ms-policy-hash** : hachage de stratégie d’évaluation Azure Attestation calculé comme BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)))))
- **x-ms-policy-signer** : objet JSON avec un membre « jwk » représentant la clé utilisée par un client pour signer sa stratégie, lorsque le client charge une stratégie signée

Les revendications ci-dessous sont considérées comme dépréciées, mais sont entièrement prises en charge. Nous vous recommandons d’utiliser les noms des revendications non dépréciées.

Revendication dépréciée | Revendication recommandée 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
maa-policyHash | x-ms-policy-hash
policy_hash | x-ms-policy-hash
policy_signer | x-ms-policy-signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Revendications sortantes communes à tous les types d’attestations

Les revendications ci-dessous sont définies par le document [IETF JWT](https://tools.ietf.org/html/rfc7519) et utilisées par Azure Attestation dans l’objet de réponse :

- **Revendication « jti » (ID JWT)**
- **Revendication « iss » (émetteur)**
- **Revendication « iat » (émission à)**
- **Revendication « exp » (délai d’expiration)**
- **Revendication « nbf » (pas avant)**

Les revendications ci-dessous sont définies par le document [IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) et utilisées par Azure Attestation dans l’objet de réponse :
- **« Revendication nonce » (nonce)**

## <a name="claims-specific-to-sgx-enclaves"></a>Revendications propres aux enclaves SGX

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Revendications entrantes propres à l’attestation SGX

Les revendications ci-dessous sont générées par le service pour l’attestation SGX et peuvent être utilisées pour définir des règles d’autorisation dans une stratégie personnalisée :
- **x-ms-sgx-is-debuggable** : valeur booléenne qui indique si le débogage est activé ou non pour l’enclave.
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner** : valeur encodée hexadécimale du champ « mrsigner » de la déclaration
- **x-ms-sgx-mrenclave** : valeur encodée hexadécimale du champ « mrenclave » de la déclaration
- **x-ms-sgx-svn** : numéro de version de sécurité encodé dans la déclaration 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Revendications sortantes propres à l’attestation SGX

Les revendications ci-dessous sont générées par le service et incluses dans l’objet de réponse pour l’attestation SGX :
- **x-ms-sgx-is-debuggable** : valeur booléenne qui indique si le débogage est activé ou non pour l’enclave.
- **x-ms-sgx-product-id**
- **x-ms-ver**
- **x-ms-sgx-mrsigner** : valeur encodée hexadécimale du champ « mrsigner » de la déclaration
- **x-ms-sgx-mrenclave** : valeur encodée hexadécimale du champ « mrenclave » de la déclaration
- **x-ms-sgx-svn** : numéro de version de sécurité encodé dans la déclaration 
- **x-ms-sgx-ehd** : données détenues par une enclave au format BASE64URL (EnclaveHeldData)
- **x-ms-sgx-collateral** : objet JSON décrivant les informations collatérales utilisées pour effectuer l’attestation. La valeur de la revendication x-ms-sgx-collateral est un objet JSON imbriqué avec les paires clé/valeur suivantes :
    - **qeidcertshash** : valeur SHA256 des certificats émetteurs d’identité QE
    - **qeidcrlhash** : valeur SHA256 de la liste de révocation de certificats des certificats émetteurs d’identité QE
    - **qeidhash** : valeur SHA256 des informations collatérales de l’identité QE
    - **quotehash** : valeur SHA256 de la déclaration évaluée
    - **tcbinfocertshash** : valeur SHA256 des certificats d’émission d’informations TCB
    - **tcbinfocrlhash** : valeur SHA256 de la liste de révocation de certificats des certificats d’émission d’informations TCB
    - **tcbinfohash** : objet JSON décrivant les informations collatérales utilisées pour effectuer l’attestation

Les revendications ci-dessous sont considérées comme dépréciées, mais sont entièrement prises en charge et continueront à être incluses à l’avenir. Nous vous recommandons d’utiliser les noms des revendications non dépréciées.

Revendication dépréciée | Revendication recommandée
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product-id | x-ms-sgx-product-id
$svn | x-ms-sgx-svn
$tee | x-ms-attestation-type
maa-ehd | x-ms-sgx-ehd
aas-ehd | x-ms-sgx-ehd
maa-attestationcollateral | x-ms-sgx-collateral

## <a name="claims-issued-specific-to-trusted-platform-module-tpm-attestation"></a>Revendications émises propres à l’attestation de Module de plateforme sécurisée (TPM)

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Revendications entrantes (également utilisables comme revendications sortantes)

- **aikValidated** :  valeur booléenne contenant des informations si le certificat de la clé d’attestation d’identité (AIK) a été validé ou non.
- **aikPubHash** :  chaîne contenant la valeur base64(SHA256(clé publique AIK au format DER)).
- **tpmVersion** :   valeur entière contenant la version principale du module de plateforme sécurisée (TPM).
- **secureBootEnabled** : valeur booléenne indiquant si le démarrage sécurisé est activé.
- **iommuEnabled** :  valeur booléenne indiquant si l’unité de gestion de la mémoire d’entrée-sortie (Iommu) est activée.
- **bootDebuggingDisabled** : valeur booléenne pour indiquer si le débogage de démarrage est désactivé.
- **notSafeMode** :  valeur booléenne indiquant si Windows ne s’exécute pas en mode sans échec.
- **notWinPE** :  valeur booléenne indiquant si Windows ne s’exécute pas en mode WinPE.
- **vbsEnabled** :  valeur booléenne indiquant si VBS est activé.
- **vbsReportPresent** :  valeur booléenne indiquant si le rapport de l’enclave VBS est disponible.
- **enclaveAuthorId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID de l’auteur de l’enclave. Identificateur de l’auteur du module principal de l’enclave.
- **enclaveImageId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID de l’image de l’enclave. Identificateur de l’image du module principal de l’enclave.
- **enclaveOwnerId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID du propriétaire de l’enclave. Identificateur du propriétaire de l’enclave.
- **enclaveFamilyId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID de famille de l’enclave. Identificateur de la famille du module principal de l’enclave.
- **enclaveSvn** :  valeur entière contenant le numéro de la version de sécurité du module principal de l’enclave.
- **enclavePlatformSvn** :  valeur entière contenant le numéro de la version de sécurité de la plateforme qui héberge l’enclave.
- **enclaveFlags** :  la revendication enclaveFlags est une valeur entière qui contient des indicateurs décrivant la stratégie d’exécution pour l’enclave.
  
### <a name="outgoing-claims-specific-to-tpm-attestation"></a>Revendications sortantes propres à l’attestation TPM

- **policy_hash** :  valeur de chaîne contenant le hachage SHA256 du texte de la stratégie calculé par BASE64URL(SHA256(BASE64URL(UTF8(texte de la stratégie)))).
- **policy_signer** :  Contient un objet JWK avec la clé publique ou la chaîne de certificats présente dans l’en-tête de stratégie signé.
- **ver (Version)**  :  valeur de chaîne contenant la version du rapport. Il s’agit de la version 1.0.
- **Revendication cnf (confirmation)**  :  La revendication « cnf » est utilisée pour identifier la clé de preuve de possession. Les revendications de confirmation telles que définies dans le document RFC 7800 contiennent la partie publique de la clé de l’enclave attestée représentée sous la forme d’un objet JWK (JSON Web Key) (RFC 7517).
- **rp_data (données de la partie de confiance)**  :  données de la partie de confiance, le cas échéant, spécifiées dans la demande, utilisées par la partie de confiance comme nonce pour garantir le caractère actuel du rapport.
- **Revendication « jti »(ID JWT)**  : La revendication « JTI » (ID JWT) fournit un identificateur unique pour le jeton JWT. La valeur de l’identificateur est attribuée de manière à garantir qu’il y a une probabilité négligeable que la même valeur soit affectée par accident à un objet de données différent.

### <a name="property-claims"></a>Revendications de propriété

- **report_validity_in_minutes** : revendication sous forme d’entier indiquant la durée de validité du jeton.
  - **Valeur par défaut (durée)**  : un jour en minutes.
  - **Valeur maximale (durée)**  : un an en minutes.
- **omit_x5c** : revendication sous forme booléenne indiquant si Azure Attestation doit omettre le certificat utilisé pour fournir la preuve de l’authenticité du service. Si la valeur est true, x5t est ajouté au jeton d’attestation. Si la valeur est false (valeur par défaut), x5c est ajouté au jeton d’attestation.

## <a name="next-steps"></a>Étapes suivantes
- [Guide pratique pour créer et signer une stratégie d’attestation](author-sign-policy.md)
- [Configurer Azure Attestation à l’aide de PowerShell](quickstart-powershell.md)
