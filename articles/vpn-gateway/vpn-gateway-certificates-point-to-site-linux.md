---
title: 'Générer et exporter des certificats pour les connexions de point à site : Linux : CLI : Azure | Microsoft Docs'
description: Créez un certificat racine auto-signé, exportez la clé publique et générez des certificats clients à l’aide de l’interface de ligne de commande Linux (strongSwan).
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305724"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Générer et exporter des certificats pour les connexions de point à site à l’aide de l’interface de ligne de commande Linux (strongSwan)

Les connexions de point à site utilisent des certificats pour l’authentification. Cet article vous explique comment créer un certificat racine auto-signé et générer des certificats clients à l’aide de l’interface de ligne de commande Linux et de strongSwan. Pour des instructions sur d’autres certificats, voir les articles [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

> [!NOTE]
> Les étapes décrites dans cet article nécessitent strongSwan.
>

La configuration de l’ordinateur utilisée pour les étapes de cet article était la suivante :

| | |
|---|---|
|**Ordinateur**| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
|**Dépendances**| apt-get install strongswan-ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan-standard-plugins |

## <a name="install-strongswan"></a>Installer strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

Pour plus d’informations sur l’installation de strongSwan à l’aide de l’interface utilisateur graphique, consultez les étapes de l’article [Configuration du client](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-keys-and-certificate"></a>Générer des clés et un certificat

1. Générez le certificat d’autorité de certification.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Imprimez le certificat d’autorité de certification au format base64. C’est le format pris en charge par Azure. Vous le téléchargerez plus tard vers Azure dans le cadre de votre configuration P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. Générez le certificat de l’utilisateur.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Générez un fichier p12 contenant le certificat de l’utilisateur. Ce fichier sera utilisé dans les étapes suivantes lorsque vous traiterez les [fichiers de configuration du client](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>Étapes suivantes

Passez à votre configuration point à site pour [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).