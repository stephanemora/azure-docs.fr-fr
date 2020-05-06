---
title: TLS – Azure Database pour PostgreSQL – Serveur unique
description: Instructions et informations sur la façon de configurer la connectivité TSL pour Azure Database pour PostgreSQL – Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141744"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurer la connectivité TLS dans Azure Database pour PostgreSQL – Serveur unique

Azure Database pour PostgreSQL préfère connecter vos applications clientes au service PostgreSQL en utilisant le protocole TLS (Transport Layer Security), anciennement appelé SSL (Secure Sockets Layer). L’application de connexions TLS entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application

Par défaut, le service de base de données PostgreSQL est configuré de façon à exiger une connexion TLS. Vous pouvez désactiver l’exigence d’utilisation du protocole TLS si votre application cliente ne prend pas en charge la connectivité TLS.

## <a name="enforcing-tls-connections"></a>Appliquer les connexions TLS

Pour tous les serveurs Azure Database pour PostgreSQL approvisionnés via le Portail Azure et Azure CLI, l’application de connexions TLS est activée par défaut. 

De même, les chaînes de connexion prédéfinies dans les paramètres « Chaînes de connexion » du serveur sur le Portail Azure incluent les paramètres requis par les langages courants pour se connecter au serveur de base de données via TLS. Le paramètre TLS varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

## <a name="configure-enforcement-of-tls"></a>Configurer l’application du protocole TLS

Vous pouvez désactiver l’application de la connectivité protocole TLS. Microsoft Azure recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Accédez à votre serveur de base de données Azure pour PostgreSQL et cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**. Ensuite, cliquez sur **Enregistrer**.

![Sécurité de connexion – Désactiver l’application du protocole TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Vous pouvez vérifier le paramètre en consultant la page **Vue d’ensemble** pour voir si l’indicateur d **’état de d’application SSL** est affiché.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

Vous pouvez activer ou désactiver le paramètre **ssl-mise en œuvre** en utilisant respectivement les valeurs `Enabled` ou `Disabled` dans Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions TLS

Certaines infrastructures d’applications courantes qui utilisent PostgreSQL pour leurs services de base de données n’activent pas le protocole TLS par défaut lors de l’installation. Si votre serveur PostgreSQL applique des connexions TLS, mais que l’application n’est pas configurée pour cela, cette dernière risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Applications nécessitant la vérification du certificat pour la connectivité TLS

Dans certains cas, les applications nécessitent un fichier de certificat local généré à partir du fichier de certificat (.cer) d’une autorité de certification (AC) approuvée pour se connecter en toute sécurité. Vous trouverez le certificat permettant la connexion à un serveur Azure Database pour PostgreSQL ici : https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Télécharger le fichier du certificat et enregistrez-le à l’emplacement par défaut.

### <a name="connect-using-psql"></a>Connexion via psql

L’exemple suivant montre comment vous connecter à votre serveur PostgreSQL à l’aide de l’utilitaire de ligne de commande psql. Utilisez le paramètre de chaîne de connexion `sslmode=verify-full` pour appliquer la vérification du certificat TLS/SSL. Passez le chemin d’accès du fichier de certificat local au paramètre `sslrootcert`.

La commande suivante est un exemple de chaîne de connexion pqsl :

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Vérifiez que la valeur passée à `sslrootcert` correspond au chemin de fichier du certificat que vous avez enregistré.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les différentes options de connectivité d’application de la rubrique [Bibliothèques de connexions d’Azure Database pour PostgreSQL](concepts-connection-libraries.md).
