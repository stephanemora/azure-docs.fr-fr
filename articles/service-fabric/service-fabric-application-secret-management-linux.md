---
title: Configurer un certificat de chiffrement et chiffrer des secrets sur clusters Linux Azure Service Fabric | Microsoft Docs
description: Découvrez comment configurer un certificat de chiffrement et chiffrer des secrets sur clusters Linux.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126985"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Configurer un certificat de chiffrement et chiffrer des secrets sur clusters Linux
Dans cet article, découvrez comment configurer un certificat de chiffrement et l’utiliser pour chiffrer des secrets sur clusters Linux. Pour les clusters Windows, consultez [Configurer un certificat de chiffrement et chiffrer des secrets sur Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Obtenir un certificat de chiffrement de données
Un certificat de chiffrement de données est utilisé exclusivement pour le chiffrement et le déchiffrement des [paramètres][parameters-link] du fichier Settings.xml d’un service et des [variables d'environnement][environment-variables-link] du fichier ServiceManifest.xml d’un service. Il n’est pas utilisé pour l’authentification ou la signature du texte chiffré. Le certificat doit répondre aux exigences suivantes :

* Le certificat doit contenir une clé privée.
* L’utilisation d’une clé de certificat doit inclure le chiffrement de données (10) et ne doit pas inclure l’authentification du serveur ou l’authentification du client.

  Par exemple, les commandes suivantes peuvent être utilisées pour générer le certificat nécessaire à l’aide d’OpenSSL :
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installation du certificat dans votre cluster
Le certificat doit être installé sur chaque nœud du cluster sous `/var/lib/sfcerts`. Le compte d’utilisateur sous lequel le service s’exécute (sfuser par défaut) **doit avoir un accès en lecture** pour le certificat installé (c’est-à-dire, `/var/lib/sfcerts/TestCert.pem` pour l’exemple actuel).

## <a name="encrypt-secrets"></a>Chiffrer des secrets
L’extrait de code suivant permet de chiffrer un secret. Cet extrait de code chiffre uniquement la valeur ; elle ne signe **pas** le texte chiffré. **Vous devez utiliser** le certificat de chiffrement qui est installé dans votre cluster afin de produire le texte chiffré pour les valeurs secrètes.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
La chaîne encodée en base 64 qui en résulte dans le fichier encrypted.txt contient à la fois le texte chiffré secret et plus d’informations sur le certificat qui a été utilisé pour le chiffrement. Vous pouvez vérifier sa validité en la déchiffrant avec OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [Spécifier des secrets chiffrés dans une application.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
