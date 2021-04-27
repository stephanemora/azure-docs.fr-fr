---
title: Ensembles de revendications d’Azure Attestation
description: Ensembles de revendications d’Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517553"
---
# <a name="claim-sets"></a>Ensembles de revendications

Les revendications générées dans le processus d’attestation d’enclaves à l’aide de Microsoft Azure Attestation peuvent être réparties dans les catégories ci-dessous :

- **Revendications entrantes** : Les revendications générées par Microsoft Azure Attestation après l’analyse de la preuve d’attestation et qui peuvent être utilisées par les auteurs de stratégie pour définir des règles d’autorisation dans une stratégie personnalisée

- **Revendications sortantes** : Les revendications générées par Azure Attestation et incluses dans le jeton d’attestation

- **Revendications de propriété** : Les revendications créées en tant que sortie par Azure Attestation. Cette catégorie contient toutes les revendications qui représentent les propriétés du jeton d’attestation, telles que l’encodage du rapport, la durée de validité du rapport, etc.

## <a name="incoming-claims"></a>Revendications entrantes 

### <a name="sgx-attestation"></a>Attestation SGX

Revendications devant être utilisées par les auteurs de stratégie pour définir des règles d’autorisation dans une stratégie d’attestation SGX :

- **x-ms-sgx-is-debuggable** : valeur booléenne indiquant si le débogage est activé ou non pour l’enclave.
  
  Il est possible de charger des enclaves SGX quand le débogage est désactivé ou activé. Lorsque l’indicateur a la valeur true dans l’enclave, il active les fonctionnalités de débogage pour le code de l’enclave. La possibilité d’accéder à la mémoire de l’enclave en fait partie. Par conséquent, il est recommandé de définir l’indicateur sur true uniquement à des fins de développement. S’il est activé dans l’environnement de production, les garanties de sécurité SGX ne seront pas maintenues.
  
  Les utilisateurs Azure Attestation peuvent se servir de la stratégie d’attestation afin de vérifier si le débogage est désactivé pour l’enclave SGX. Une fois la règle de stratégie ajoutée, l’attestation échouera quand un utilisateur malveillant activera la prise en charge du débogage pour obtenir l’accès au contenu de l’enclave.

- **x-ms-sgx-product-id** : valeur entière indiquant l’ID de produit de l’enclave SGX.

  L’auteur de l’enclave affecte un ID de produit à chaque enclave. L’ID de produit permet à l’auteur de l’enclave de segmenter les enclaves signées à l’aide du même MRSIGNER. En ajoutant une règle de validation dans la stratégie d’attestation, les clients peuvent vérifier s’ils utilisent les enclaves prévues. L’attestation échouera si l’ID de produit de l’enclave ne correspond pas à la valeur publiée par l’auteur de l’enclave.

- **x-ms-sgx-mrsigner** : valeur de chaîne identifiant l’auteur de l’enclave SGX.

  MRSIGNER représente le hachage de la clé publique de l’auteur de l’enclave qui sert à signer le code binaire de l’enclave. En validant MRSIGNER via une stratégie d’attestation, les clients peuvent vérifier si les fichiers binaires approuvés s’exécutent à l’intérieur d’une enclave. Lorsque la revendication de stratégie ne correspond pas à la stratégie MRSIGNER de l’auteur de l’enclave, le fichier binaire de l’enclave n’est alors pas signé par une source approuvée et l’attestation échoue.
  
  Lorsque l’auteur d’une enclave préfère effectuer une rotation de MRSIGNER pour des raisons de sécurité, la stratégie Azure Attestation doit être mise à jour pour prendre en charge les nouvelles et les anciennes valeurs MRSIGNER avant la mise à jour des fichiers binaires. Dans le cas contraire, les contrôles d’autorisation échoueront, entraînant des échecs d’attestation.
  
  La stratégie d’attestation doit être mise à jour à l’aide du format ci-dessous. 
 
  #### <a name="before-key-rotation"></a>Avant la rotation des clés
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Lors de la rotation des clés

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Après la rotation des clés

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave** : valeur de chaîne identifiant le code et les données chargées dans la mémoire de l’enclave. 

  MRENCLAVE est une des mesures de l’enclave pouvant être utilisée pour vérifier les fichiers binaires de l’enclave. Il s’agit du hachage du code qui s’exécute dans l’enclave. La mesure change à chaque modification apportée au code binaire de l’enclave. En validant MRENCLAVE au moyen d’une stratégie d’attestation, les clients peuvent vérifier si les fichiers binaires prévus s’exécutent dans une enclave. Cela étant, comme MRENCLAVE est censé changer fréquemment à la moindre modification du code existant, il est recommandé de vérifier les fichiers binaires de l’enclave à l’aide de la validation MRSIGNER dans une stratégie d’attestation.

- **x-ms-sgx-svn** : valeur entière indiquant le numéro de la version de sécurité de l’enclave SGX

  L’auteur de l’enclave attribue un numéro de version de sécurité (SVN, Security Version Number) à chaque version de l’enclave SGX. Lorsqu’un problème de sécurité est découvert dans le code de l’enclave, l’auteur de l’enclave incrémente la valeur du SVN après la correction de vulnérabilité. Pour empêcher l’interaction avec le code d’enclave non sécurisé, les clients peuvent ajouter une règle de validation dans la stratégie d’attestation. Si le SVN du code de l’enclave ne correspond pas à la version recommandée par l’auteur de l’enclave, l’attestation échouera.

Les revendications ci-dessous sont considérées comme dépréciées, mais sont entièrement prises en charge et continueront à être incluses à l’avenir. Nous vous recommandons d’utiliser les noms des revendications non dépréciées.

Revendication dépréciée | Revendication recommandée
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>Attestation TPM

Revendications devant être utilisées par les auteurs de stratégie pour définir des règles d’autorisation dans une stratégie d’attestation TPM :

- **aikValidated** : valeur booléenne contenant des informations indiquant si le certificat de la clé d’attestation d’identité (AIK) a été validé ou non
- **aikPubHash** :  chaîne contenant la valeur base64(SHA256(clé publique AIK au format DER))
- **tpmVersion** :   valeur entière contenant la version principale du module de plateforme sécurisée (TPM)
- **secureBootEnabled** : valeur booléenne indiquant si le démarrage sécurisé est activé
- **iommuEnabled** :  valeur booléenne indiquant si l’unité de gestion de la mémoire d’entrée-sortie (Iommu) est activée
- **bootDebuggingDisabled** : valeur booléenne pour indiquer si le débogage de démarrage est désactivé
- **notSafeMode** :  valeur booléenne indiquant si Windows ne s’exécute pas en mode sans échec
- **notWinPE** :  valeur booléenne indiquant si Windows ne s’exécute pas en mode WinPE
- **vbsEnabled** :  valeur booléenne indiquant si VBS est activé
- **vbsReportPresent** :  valeur booléenne indiquant si le rapport de l’enclave VBS est disponible

### <a name="vbs-attestation"></a>Attestation VBS

Outre les revendications de stratégie d’attestation TPM, les auteurs de stratégie peuvent utiliser les revendications ci-dessous pour définir des règles d’autorisation dans une stratégie d’attestation VBS.

- **enclaveAuthorId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID de l’auteur de l’enclave. Identificateur de l’auteur du module principal de l’enclave
- **enclaveImageId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID de l’image de l’enclave. Identificateur de l’image du module principal de l’enclave
- **enclaveOwnerId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID du propriétaire de l’enclave. Identificateur du propriétaire de l’enclave
- **enclaveFamilyId** :  valeur de chaîne contenant la valeur encodée Base64Url de l’ID de famille de l’enclave. Identificateur de la famille du module principal de l’enclave
- **enclaveSvn** :  valeur entière contenant le numéro de la version de sécurité du module principal de l’enclave
- **enclavePlatformSvn** :  valeur entière contenant le numéro de la version de sécurité de la plateforme qui héberge l’enclave
- **enclaveFlags** :  la revendication enclaveFlags est une valeur entière qui contient des indicateurs décrivant la stratégie d’exécution pour l’enclave

## <a name="outgoing-claims"></a>Revendications sortantes 

### <a name="common-for-all-attestation-types"></a>Communes à tous les types d’attestation

Azure Attestation comprend les revendications ci-dessous dans le jeton d’attestation pour tous les types d’attestations. 

- **x-ms-ver** : version du schéma JWT (valeur « 1.0 » attendue)
- **x-ms-attestation-type** : valeur de chaîne représentant le type d’attestation 
- **x-ms-policy-hash** : hachage de stratégie d’évaluation Azure Attestation calculé comme BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)))))
- **x-ms-policy-signer** : objet JSON avec un membre « jwk » représentant la clé utilisée par un client pour signer sa stratégie. Cela s’applique lorsque le client charge une stratégie signée

Les noms de revendication ci-dessous sont utilisés à partir de la [spécification IETF JWT](https://tools.ietf.org/html/rfc7519)

- **Revendication « jti » (ID JWT)**  : identificateur unique du jeton JWT
- **Revendication « iss » (émetteur)**  : principal qui a émis le jeton JWT 
- **Revendication « iat » (émission à)**  : heure à laquelle le jeton JWT a été émis 
- **Revendication « exp » (délai d’expiration)**  : délai d’expiration à l’issue duquel le jeton JWT ne doit pas être accepté pour être traité
- **Revendication « nbf » (pas avant)**  : délai pas avant lequel le jeton JWT ne doit pas être accepté pour être traité 

Les noms de revendication ci-dessous sont utilisés à partir de l’[avant-projet de la spécification IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **« Revendication nonce » (valeur à usage unique)**  : copie directe non transformée d’une valeur nonce facultative fournie par un client 

Les revendications ci-dessous sont considérées comme dépréciées, mais sont entièrement prises en charge et continueront à être incluses à l’avenir. Nous vous recommandons d’utiliser les noms des revendications non dépréciées.

Revendication dépréciée | Revendication recommandée
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>Attestation SGX 

Les revendications ci-dessous sont générées par le service et incluses dans le jeton d’attestation pour l’attestation SGX.

- **x-ms-sgx-is-debuggable** : valeur booléenne qui indique si le débogage est activé ou non pour l’enclave.
- **x-ms-sgx-product-id** : valeur de l’ID produit de l’enclave SGX 
- **x-ms-sgx-mrsigner** : valeur encodée hexadécimale du champ « mrsigner » de la déclaration
- **x-ms-sgx-mrenclave** : valeur encodée hexadécimale du champ « mrenclave » de la déclaration
- **x-ms-sgx-svn** : numéro de version de sécurité encodé dans la déclaration 
- **x-ms-sgx-ehd** : données détenues par une enclave au format BASE64URL (EnclaveHeldData)
- **x-ms-sgx-collateral** : objet JSON décrivant les informations collatérales utilisées pour effectuer l’attestation. La valeur de la revendication x-ms-sgx-collateral est un objet JSON imbriqué avec les paires clé/valeur suivantes :
    - **qeidcertshash** : valeur SHA256 des certificats émetteurs d’identité QE (Quoting Enclave)
    - **qeidcrlhash** : valeur SHA256 de la liste de révocation de certificats des certificats émetteurs d’identité QE
    - **qeidhash** : valeur SHA256 des informations collatérales de l’identité QE
    - **quotehash** : valeur SHA256 de la déclaration évaluée
    - **tcbinfocertshash** : valeur SHA256 des certificats d’émission d’informations TCB
    - **tcbinfocrlhash** : valeur SHA256 de la liste de révocation de certificats des certificats d’émission d’informations TCB
    - **tcbinfohash** : valeur SHA256 des documentations et ressources d’accompagnement des informations TCB

Les revendications ci-dessous sont considérées comme dépréciées, mais sont entièrement prises en charge et continueront à être incluses à l’avenir. Nous vous recommandons d’utiliser les noms des revendications non dépréciées.

Revendication dépréciée | Revendication recommandée
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-collateral

### <a name="tpm-and-vbs-attestation"></a>Attestation TPM et VBS

- **cnf (Confirmation)**  : La revendication « cnf » est utilisée pour identifier la clé de preuve de possession. La revendication de confirmation telle que définie dans le document RFC 7800 contient la partie publique de la clé de l’enclave attestée représentée sous la forme d’un objet JWK (JSON Web Key) (RFC 7517)
- **rp_data (données de la partie de confiance)**  : données de la partie de confiance, le cas échéant, spécifiées dans la demande, utilisées par la partie de confiance comme nonce pour garantir le caractère actuel du rapport. rp_data est ajouté uniquement si rp_data est présent

## <a name="property-claims"></a>Revendications de propriété

### <a name="tpm-and-vbs-attestation"></a>Attestation TPM et VBS

- **report_validity_in_minutes** : revendication sous forme d’entier pour indiquer la durée de validité du jeton.
  - **Valeur par défaut (durée)**  : un jour en minutes.
  - **Valeur maximale (durée)**  : un an en minutes.
- **omit_x5c** : revendication sous forme booléenne indiquant si Azure Attestation doit omettre le certificat utilisé pour fournir la preuve de l’authenticité du service. Si la valeur est true, x5t est ajouté au jeton d’attestation. Si la valeur est false (valeur par défaut), x5c est ajouté au jeton d’attestation.

## <a name="next-steps"></a>Étapes suivantes
- [Guide pratique pour créer et signer une stratégie d’attestation](author-sign-policy.md)
- [Configurer Azure Attestation à l’aide de PowerShell](quickstart-powershell.md)
