---
title: Résoudre les problèmes d’authentification mutuelle sur Azure Application Gateway
description: Découvrez comment résoudre les problèmes d’authentification mutuelle sur Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230857"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Résolution des erreurs d’authentification mutuelle dans Application Gateway (préversion)

Découvrez comment résoudre les problèmes d’authentification mutuelle lors de l’utilisation d’Application Gateway. 

## <a name="overview"></a>Vue d’ensemble 

Une fois l’authentification mutuelle configurée sur Application Gateway, un certain nombre d’erreurs peuvent s’afficher lors d’une tentative d’utilisation de l’authentification mutuelle. Parmi les causes courantes de ces erreurs figurent les suivantes :

* Chargement d’un certificat ou d’une chaîne de certificats sans certificat d’autorité de certification racine
* Chargement d’une chaîne de certificats avec plusieurs certificats d’autorité de certification racine
* Chargement d’une chaîne de certificats contenant uniquement un certificat feuille sans certificat d’autorité de certification 
* Erreurs de validation liées à une incompatibilité de nom unique d’émetteur  

Nous allons examiner différents scénarios susceptibles de se produire, ainsi que la manière de résoudre les problèmes qui en découlent. Nous allons ensuite nous intéresser aux codes d’erreur et expliquer les causes probables de certains codes en lien avec l’authentification mutuelle. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Résolution des problèmes liés à la configuration
Vous pouvez être confronté à plusieurs scénarios lorsque vous essayez de configurer l’authentification mutuelle. Nous allons nous pencher sur la manière de déjouer certains des pièges les plus courants. 

### <a name="self-signed-certificate"></a>Certificat auto-signé

#### <a name="problem"></a>Problème 

Le certificat client que vous avez chargé est un certificat auto-signé et génère le code d’erreur ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate.

#### <a name="solution"></a>Solution 

Vérifiez que le certificat auto-signé que vous utilisez porte l’extension *BasicConstraintsOid* = "2.5.29.19", ce qui indique que l’objet peut agir en tant qu’autorité de certification. Cela permet de s’assurer que le certificat utilisé est un certificat d’autorité de certification. Pour plus d’informations sur la génération de certificats clients auto-signés, consultez [Certificats clients approuvés](./mutual-authentication-certificate-management.md).

## <a name="scenario-troubleshooting---connectivity-problems"></a>Résolution des problèmes liés à la connectivité

Vous êtes peut-être parvenu à correctement configurer l’authentification mutuelle, mais vous rencontrez des problèmes lors de l’envoi de requêtes à votre instance Application Gateway. Nous allons aborder certains problèmes courants et solutions dans la section suivante. La propriété *sslClientVerify* est disponible dans les journaux d’accès de votre instance Application Gateway. 

### <a name="sslclientverify-is-none"></a>SslClientVerify indique NONE

#### <a name="problem"></a>Problème 

La propriété *sslClientVerify* indique « NONE » dans vos journaux d’accès. 

#### <a name="solution"></a>Solution 

Cela se produit si le client n’envoie pas de certificat client lors de l’envoi d’une requête à Application Gateway. Cela peut se produire si le client qui envoie la requête à Application Gateway n’est pas correctement configuré pour utiliser des certificats clients. Pour vérifier que la configuration de l’authentification du client sur Application Gateway fonctionne comme prévu, vous pouvez utiliser la commande OpenSSL suivante :

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

L'indicateur `-cert` correspond au certificat feuille et l’indicateur `-key` au fichier de clé privée du client. 

Pour plus d’informations sur l’utilisation de la commande OpenSSL `s_client`, consultez la [page du manuel](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html) correspondante.

### <a name="sslclientverify-is-failed"></a>SslClientVerify indique FAILED

#### <a name="problem"></a>Problème

La propriété *sslClientVerify* indique « FAILED » dans vos journaux d’accès. 

#### <a name="solution"></a>Solution

Les défaillances peuvent avoir plusieurs causes possibles dans les journaux d’accès. Vous trouverez ci-dessous la liste des causes de défaillance les plus courantes :
* **Impossible d’accéder au certificat d’émetteur :** le certificat d’émetteur du certificat client est introuvable. Cela signifie généralement que la chaîne de certificats d’autorité de certification clients approuvés n’est pas terminée sur Application Gateway. Vérifiez que la chaîne de certificats d’autorité de certification cliens approuvés chargée sur Application Gateway est terminée.  
* **Impossible d’accéder au certificat d’émetteur local :** comme pour le certificat d’émetteur, le certificat d’émetteur du certificat client est introuvable. Cela signifie généralement que la chaîne de certificats d’autorité de certification clients approuvés n’est pas terminée sur Application Gateway. Vérifiez que la chaîne de certificats d’autorité de certification cliens approuvés chargée sur Application Gateway est terminée.
* **Impossible de vérifier le premier certificat :** impossible de vérifier le certificat client. Cette erreur se produit lorsque le client présente uniquement le certificat feuille et que son émetteur n’est pas approuvé. Vérifiez que la chaîne de certificats d’autorité de certification cliens approuvés chargée sur Application Gateway est terminée.
* **Impossible de vérifier l’émetteur du certificat client :** cette erreur se produit lorsque la configuration *VerifyClientCertIssuerDN* est définie sur True. Cela se produit généralement lorsque le nom unique de l’émetteur du certificat client ne correspond pas à la propriété *ClientCertificateIssuerDN* extraite de la chaîne de certificats d’autorité de certification clients approuvés chargée par le client. Pour plus d’informations sur la manière dont Application Gateway extrait la propriété *ClientCertificateIssuerDN*, consultez [Extraction par Application Gateway du nom unique de l’émetteur](./mutual-authentication-overview.md#verify-client-certificate-dn). Nous vous recommandons de vérifier que vous chargez une chaîne de certificats par fichier vers Application Gateway. 

Pour plus d’informations sur l’extraction de toute la chaîne de certificats d’autorité de certification clients approuvés à charger vers Application Gateway, consultez [Extraire des chaînes de certificats d’autorité de certification clients approuvés](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Résolution des codes d’erreur
Si vous constatez l’un des codes d’erreur suivants, vous trouverez ci-dessous plusieurs solutions recommandées pour vous aider à résoudre les problèmes qui en découlent. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Code d’erreur : ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Cause

Certaines données de certificat sont manquantes. Le certificat chargé correspond peut-être à un fichier vide, sans aucune donnée de certificat. 

#### <a name="solution"></a>Solution

Vérifiez qu’aucune donnée ne manque dans le fichier de certificat chargé. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Code d’erreur : ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Cause

La chaîne de certificats contient une clé privée. La chaîne de certificats ne doit contenir aucune clé privée. 

#### <a name="solution"></a>Solution

Vérifiez la chaîne de certificats chargée et supprimez la clé privée qu’elle contient. Rechargez la chaîne sans la clé privée. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Code d’erreur : ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Cause

Ce code d’erreur peut avoir deux causes possibles.
1. L’analyse a échoué en raison de la non-présentation de la chaîne au format qui convient. Application Gateway attend une chaîne de certificats au format PEM, ainsi que des données de certificat individuel délimitées. 
2. L’analyseur n’a rien trouvé à analyser. Le fichier chargé contient peut-être uniquement des délimiteurs, et aucune donnée de certificat. 

#### <a name="solution"></a>Solution

En fonction de la cause de cette erreur, deux solutions sont possibles. 
* Vérifiez que la chaîne de certificats chargée est au format qui convient (PEM) et que les données de certificat ont été correctement délimitées. 
* Vérifiez que le fichier de certificat chargé contient les données de certificat en plus des délimiteurs. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Code d’erreur : ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Cause

Le certificat chargé contient uniquement un certificat feuille, sans certificat d’autorité de certification. Le chargement d’une chaîne de certificats avec des certificats d’autorité de certification et un certificat feuille est acceptable dans la mesure où le certificat feuille sera simplement ignoré, mais un certificat doit avoir une autorité de certification. 

#### <a name="solution"></a>Solution 

Vérifiez que la chaîne de certificats téléchargée ne contient pas uniquement le certificat feuille. L’extension *BasicConstraintsOid* = "2.5.29.19" doit être présente et indiquer que l’objet peut faire office d’autorité de certification.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Code d’erreur : ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Cause

La chaîne de certificats contient plusieurs certificats d’autorité de certification racine *ou* ne contient aucun certificat d’autorité de certification racine. 

#### <a name="solution"></a>Solution

Les certificats chargés doivent contenir un seul certificat d’autorité de certification racine (et plusieurs certificats d’autorité de certification intermédiaires, si besoin).


