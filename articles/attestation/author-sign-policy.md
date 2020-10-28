---
title: Guide pratique pour créer et signer une stratégie Azure Attestation
description: Explication de la création et de la signature d’une stratégie d’attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c8ffdcd0615913649e80b20f6873d005f4ad4410
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675996"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Guide pratique pour créer et signer une stratégie d’attestation

Une stratégie d’attestation est un fichier chargé sur Microsoft Azure Attestation. Azure Attestation offre la possibilité de charger une stratégie dans un format de stratégie spécifique à l’attestation. Une version encodée de la stratégie, au format JSON Web Signature, peut également être chargée. L’administrateur de stratégies est responsable de l’écriture de la stratégie d’attestation. Dans la plupart des scénarios d’attestation, la partie de confiance fait office d’administrateur de stratégies. Le client qui effectue l’appel d’attestation envoie une preuve d’attestation, que le service analyse et convertit en revendications entrantes (ensemble de propriétés, valeur). Le service traite ensuite les revendications, en fonction de ce qui est défini dans la stratégie, et retourne le résultat calculé.

La stratégie contient des règles qui déterminent les critères d’autorisation, les propriétés et le contenu du jeton d’attestation. Voici un exemple de fichier de stratégie :

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Un fichier de stratégie comporte trois sections, comme indiqué ci-dessus :

- **version**  :  la version est le numéro de version de la grammaire qui est suivie. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    La seule version prise en charge est la version « 1.0 ».

- **authorizationrules**  : collection de règles de revendication qui sont vérifiées en premier, pour déterminer si Azure Attestation doit passer à la section **issuancerules** . Les règles de revendication s’appliquent dans l’ordre dans lequel elles sont définies.

- **issuancerules**  : collection de règles de revendication qui sont évaluées pour ajouter des informations supplémentaires au résultat de l’attestation, tel que défini dans la stratégie. Les règles de revendication s’appliquent dans l’ordre dans lequel elles sont définies et sont également facultatives.

Pour plus d’informations, consultez [Revendication et règles de revendication](claim-rule-grammar.md).
   
## <a name="drafting-the-policy-file"></a>Rédaction du fichier de stratégie

1. Créez un fichier.
1. Ajoutez une version au fichier.
1. Ajoutez des sections pour **authorizationrules** et **issuancerules** .

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Les règles d’autorisation contiennent l’action deny() sans condition, afin qu’aucune règle d’émission ne soit traitée. La règle d’autorisation peut également contenir l’action permit(), afin de permettre le traitement des règles d’émission.
  
4. Ajouter des règles de revendication aux règles d’autorisation

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Si le jeu de revendications entrantes contient une revendication correspondant au type, à la valeur et à l’émetteur, l’action permit() indique au moteur de stratégie de traiter la section **issuancerules** .
  
5. Ajoutez des règles de revendication à la section **issuancerules** .

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Le jeu de revendications sortantes contiendra une revendication avec :

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Des stratégies complexes peuvent être créées de la même manière. Pour plus d’informations, consultez [Exemples de stratégie d’attestation](policy-examples.md).
  
6. Enregistrez le fichier .

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Création du fichier de stratégie au format JSON Web Signature

Après avoir créé un fichier de stratégie, pour charger une stratégie au format JWS, suivez les étapes ci-dessous.

1. Générez la signature JWS (RFC 7515) avec la stratégie (encodée en utf-8) comme charge utile.
     - L’identificateur de charge utile pour la stratégie encodée Base64Url doit être « AttestationPolicy ».
     
     Exemple de jeton JWT :
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (Facultatif) Signez la stratégie. Azure Attestation prend en charge les algorithmes suivants :
     - **Aucun**  : ne pas signer la charge utile de la stratégie.
     - **RS256**  : algorithme pris en charge pour signer la charge utile de la stratégie

3. Chargez la signature JWS et validez la stratégie.
     - Si le fichier de stratégie est exempt d’erreurs de syntaxe, il est accepté par le service.
     - Sinon, ce dernier le rejette.

## <a name="signing-the-policy"></a>Signature de la stratégie

Vous trouverez ci-dessous un exemple de script Python sur la façon d’effectuer une opération de signature de stratégie.

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Étapes suivantes
- [Configurer Azure Attestation à l’aide de PowerShell](quickstart-powershell.md)
- [Attester une enclave SGX à l’aide d’exemples de code](/samples/browse/?expanded=azure&terms=attestation)