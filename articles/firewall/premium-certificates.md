---
title: Certificats du Pare-feu Azure Premium - Préversion
description: Pour configurer correctement l’inspection TLS sur le Pare-feu Azure Premium - Préversion, vous devez configurer et installer les certificats d’autorité de certification intermédiaires.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: 47ebc752dedd72bbdedc02908911f1686584acda
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615497"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Certificats du Pare-feu Azure Premium - Préversion 

> [!IMPORTANT]
> Pare-feu Azure Premium est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Pour configurer correctement l’inspection TLS du Pare-feu Azure Premium - Préversion, vous devez fournir un certificat d’autorité de certification intermédiaire valide et le déposer dans Azure Key Vault.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certificats utilisés par le Pare-feu Azure Premium - Préversion

Dans un déploiement classique, trois types de certificats sont utilisés :

- **Certificat d’autorité de certification intermédiaire (certificat d’autorité de certification)**

   Une autorité de certification est une organisation approuvée pour signer les certificats numériques. Une autorité de certification vérifie l’identité et la légitimité d’une entreprise ou d’une personne qui demande un certificat. Si la vérification aboutit, l’autorité de certification émet un certificat signé. Lorsque le serveur présente le certificat au client (par exemple, votre navigateur web) au cours de l’établissement d'une liaison SSL/TLS, le client tente de vérifier la signature par rapport à une liste de signataires *reconnus*. Les navigateurs web sont normalement accompagnés de listes d’autorités de certification qu’ils approuvent implicitement pour identifier les hôtes. Si l’autorité ne figure pas dans la liste, comme avec certains sites qui signent leurs propres certificats, le navigateur avertit l’utilisateur que le certificat n’est pas signé par une autorité reconnue et lui demande s’il souhaite continuer à communiquer avec un site non vérifié.

- **Certificat de serveur (certificat de site web)**

   Certificat approuvé associé à un nom de domaine spécifique. Si un site web dispose d’un certificat valide, cela signifie qu’une autorité de certification a pris les mesures nécessaires pour vérifier que l’adresse web appartient effectivement à cette organisation. Lorsque vous entrez une URL ou que vous suivez un lien vers un site web sécurisé, votre navigateur vérifie les caractéristiques suivantes du certificat :
   - L’adresse du site web correspond à l’adresse du certificat.
   - Le certificat est signé par une autorité de certification reconnue par le navigateur en tant qu’autorité *approuvée*.
   
   Les utilisateurs peuvent parfois se connecter à un serveur à l’aide d’un certificat non approuvé. Le Pare-feu Azure annule la connexion comme si le serveur avait mis fin à la connexion.

- **Certificat d’autorité de certification racine (certificat racine)**

   Une autorité de certification peut émettre plusieurs certificats sous forme d’arborescence. Un certificat racine correspond au certificat le plus haut de l’arborescence.

Le Pare-feu Azure Premium - Préversion peut intercepter le trafic HTTP/S sortant et générer automatiquement un certificat de serveur pour `www.website.com`. Ce certificat est généré à l’aide du certificat d’autorité de certification intermédiaire que vous fournissez. Le navigateur de l’utilisateur final et les applications clientes doivent approuver le certificat de l’autorité de certification racine de votre organisation ou le certificat d’autorité de certification intermédiaire pour que cette procédure fonctionne. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Processus du certificat":::

## <a name="intermediate-ca-certificate-requirements"></a>Conditions requises pour le certificat d’autorité de certification intermédiaire

Assurez-vous que votre certificat d’autorité de certification est conforme aux conditions suivantes :

- En cas de déploiement en tant que secret Key Vault, vous devez utiliser un fichier PFX sans mot de passe (PKCS12) avec un certificat et une clé privée.

- Le certificat est unique et n’inclut pas la chaîne entière de certificats.  

- Il doit être valide pour un an.  

- Il doit s’agir d’une clé privée RSA d’une taille minimale de 4 096 octets.  

- L’extension `KeyUsage` du certificat doit être marquée comme Critique avec l’indicateur `KeyCertSign` (RFC 5280 ; utilisation de la clé 4.2.1.3).

- L’extension `BasicContraints` du certificat doit être marquée comme Critique (RFC 5280 ; contraintes de base 4.2.1.9).  

- L’indicateur `CA` doit être défini sur TRUE.

- La longueur du chemin doit êtredoit être supérieure ou égale à un.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) est un magasin des secrets managé par une plateforme que vous pouvez utiliser pour protéger des secrets, des clés et des certificats TLS/SSL. Le Pare-feu Azure Premium prend en charge l’intégration avec Key Vault pour les certificats de serveur associés à une stratégie de pare-feu.
 
Pour configurer votre coffre de clés :

- Vous devez importer un certificat existant avec sa paire de clés dans votre coffre de clés. 
- Vous pouvez également utiliser un secret de coffre de clés, qui est stocké dans un fichier PFX encodé en Base64 et sans mot de passe.  Un fichier PFX est un certificat numérique contenant une clé privée et une clé publique.
- Il est recommandé d’utiliser une importation de certificat d’autorité de certification, car elle vous permet de configurer une alerte en fonction de la date d’expiration du certificat.
- Après avoir importé un certificat ou un secret, vous devez définir des stratégies d’accès dans le coffre de clés pour autoriser l’identité qui disposera d’un accès get au secret/certificat.
- Le certificat d’autorité de certification fourni doit être approuvé par votre charge de travail Azure. Assurez-vous de leur bon déploiement.

Vous pouvez créer ou réutiliser une identité managée existante affectée à l’utilisateur, que le Pare-feu Azure utilise pour récupérer les certificats de Key Vault à votre place. Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md). 

## <a name="configure-a-certificate-in-your-policy"></a>Configurer un certificat dans votre stratégie

Pour configurer un certificat d’autorité de certification dans votre stratégie de Pare-feu Premium, sélectionnez votre stratégie, puis **Inspection TLS (préversion)** . Sélectionnez **Activé** sur la page **Inspection TLS**. Sélectionnez ensuite votre certificat d’autorité de certification dans Azure Key Vault, comme illustré dans la figure suivante :

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Diagramme de vue d’ensemble du Pare-feu Azure Premium":::
 
> [!IMPORTANT]
> Pour afficher et configurer un certificat à partir du portail Azure, vous devez ajouter votre compte d’utilisateur Azure à la stratégie d’accès Key Vault. Dotez votre compte d’utilisateur de **Get** et **List** sous **Autorisations de secret**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Stratégies d’accès Azure Key Vault":::


## <a name="create-your-own-self-signed-ca-certificate"></a>Créer votre propre certificat d’autorité de certification auto-signé

Pour vous aider à tester et à vérifier l’inspection TLS, vous pouvez utiliser les scripts suivants afin de créer votre propre autorité de certification racine auto-signée et votre propre autorité de certification intermédiaire.

> [!IMPORTANT]
> Pour la production, vous devez utiliser votre infrastructure à clé publique d’entreprise pour créer un certificat d’autorité de certification intermédiaire. Une infrastructure à clé publique d’entreprise s’appuie sur l’infrastructure existante et gère la distribution de l’autorité de certification racine sur tous les ordinateurs de point de terminaison. Pour plus d’informations, consultez [Déployer et configurer des certificats d’autorité de certification d’entreprise pour Pare-feu Azure Premium](premium-deploy-certificates-enterprise-ca.md).

Il existe deux versions de ce script :
- Un script Bash `cert.sh` 
- Un script PowerShell `cert.ps1` 

 Par ailleurs, les deux scripts utilisent le fichier de configuration `openssl.cnf`. Pour utiliser les scripts, copiez le contenu de `openssl.cnf`, et `cert.sh` ou `cert.ps1` sur votre ordinateur local.

Les scripts génèrent les fichiers suivants :
- rootCA.crt/rootCA.key : clé privée et certificat public de l’autorité de certification racine.
- interCA.crt/interCA.key : clé privée et certificat public de l’autorité de certification intermédiaire.
- interCA.pfx : package pkcs12 d’autorité de certification intermédiaire qui sera utilisé par le pare-feu

> [!IMPORTANT]
> rootCA.key doit être stockée à un emplacement hors connexion sécurisé. Les scripts génèrent un certificat avec une validité de 1024 jours.
> Les scripts requièrent l’installation de fichiers binaires OpenSSL sur votre ordinateur local. Pour plus d'informations, consultez https://www.openssl.org/
> 
Une fois les certificats créés, déployez-les aux emplacements suivants :
- rootCA.crt : déployer sur les ordinateurs de point de terminaison (certificat public uniquement).
- interCA.pfx : importer en tant que certificat dans un coffre de clés et affecter à la stratégie de pare-feu.

### <a name="opensslcnf"></a>**openssl.cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Script Bash - cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell - cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>Dépannage

Si votre certificat d’autorité de certification est valide, mais que vous ne pouvez pas accéder aux FQDN ou URL dans le cadre de l’inspection TLS, vérifiez les points suivants :

- Vérifiez que le certificat de serveur web est valide.  

- Vérifiez que le certificat d’autorité de certification racine est installé sur le système d’exploitation client.  

- Vérifiez que le navigateur ou le client HTTPS contient un certificat racine valide. Firefox et d’autres navigateurs peuvent présenter des stratégies de certification spécifiques.  

- Vérifiez que le type de destination de l’URL dans votre règle d’application couvre le chemin correct et tous les autres liens hypertexte incorporés dans la page HTML de destination. Vous pouvez utiliser des caractères génériques pour une couverture aisée du chemin d’URL nécessaire complet.  


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les fonctionnalités du Pare-feu Azure Premium](premium-features.md)
