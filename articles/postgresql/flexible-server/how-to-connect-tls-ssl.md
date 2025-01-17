---
title: Connectivité chiffrée via TLS/SSL dans Azure Database pour PostgreSQL - Serveur flexible
description: Instructions et informations sur la connexion à l’aide du protocole TLS/SSL dans Azure Database pour PostgreSQL - Serveur flexible.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b547dfb51771c826f163972408f053d9320d8b1e
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067228"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Connectivité chiffrée à l’aide du protocole TLS dans Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en préversion

Azure Database pour PostgreSQL - Serveur flexible préfère connecter vos applications clientes au service PostgreSQL en utilisant le protocole TLS (Transport Layer Security), anciennement appelé SSL (Secure Sockets Layer). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité.

Azure Database pour PostgreSQL - Serveur flexible prend uniquement en charge les connexions chiffrées à l’aide du protocole TLS 1.2+ et toutes les connexions entrantes qui utilisent les protocoles TLS 1.0 et TLS 1.1 sont refusées. Pour tous les serveurs flexibles, l’application des connexions TLS est activée. 

>[!Note]
> Par défaut, la connectivité sécurisée entre le client et le serveur est appliquée. Si vous souhaitez désactiver TLS/SSL pour la connexion au serveur flexible, vous pouvez modifier le paramètre *require_secure_transport* du serveur sur *OFF*. Vous pouvez également définir la version TLS en configurant les paramètres de serveur *ssl_min_protocol_version* et *ssl_max_protocol_version*.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Applications nécessitant la vérification du certificat pour la connectivité TSL/SSL
Dans certains cas, les applications nécessitent un fichier de certificat local généré à partir du fichier de certificat d’une autorité de certification (AC) approuvée pour se connecter en toute sécurité. Azure Database pour PostgreSQL - Serveur flexible utilise l’*autorité de certification racine globale DigiCert*. Téléchargez ce certificat nécessaire pour communiquer par le biais du protocole SSL auprès de l’[autorité de certification racine globale DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) et enregistrez le fichier du certificat à l’emplacement qui vous convient. Par exemple, ce tutoriel utilise `c:\ssl`.


### <a name="connect-using-psql"></a>Connexion via psql
Si vous avez créé votre serveur flexible avec l’option *Accès privé (intégration au réseau virtuel)* , vous devez vous connecter à votre serveur à partir d’une ressource qui se trouve au sein du même réseau virtuel que votre serveur. Vous pouvez créer une machine virtuelle et l’ajouter au réseau virtuel créé avec votre serveur flexible.

Si vous avez créé votre serveur flexible avec l’option *Accès public (adresses IP autorisées)* , vous pouvez ajouter votre adresse IP locale à la liste des règles de pare-feu sur votre serveur.

L’exemple suivant montre comment vous connecter à votre serveur à l’aide de l’interface de ligne de commande psql. Utilisez le paramètre de chaîne de connexion `sslmode=verify-full` pour appliquer la vérification du certificat TLS/SSL. Passez le chemin d’accès du fichier de certificat local au paramètre `sslrootcert`.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Vérifiez que la valeur transmise à *sslrootcert* correspond au chemin d'accès du certificat que vous avez enregistré.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions TLS

Certaines infrastructures d’applications courantes qui utilisent PostgreSQL pour leurs services de base de données n’activent pas le protocole TLS par défaut lors de l’installation. Votre serveur PostgreSQL applique des connexions TLS, mais si l’application n’est pas configurée pour cela, cette dernière risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions TLS.

## <a name="next-steps"></a>Étapes suivantes
- [Créer et gérer un réseau virtuel Azure Database pour PostgreSQL - Serveur flexible à l’aide de Azure CLI](./how-to-manage-virtual-network-cli.md).
- En savoir plus sur la [mise en réseau dans Azure Database pour PostgreSQL - Serveur flexible](./concepts-networking.md).
- En savoir plus sur les [règles de pare-feu pour Azure Database pour PostgreSQL - Serveur flexible](./concepts-networking.md#public-access-allowed-ip-addresses).
