---
title: SSL - Azure Database pour PostgreSQL - Serveur unique
description: Instructions et informations sur la façon de configurer la connectivité SSL pour Azure Database pour PostgreSQL - Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476998"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurer la connectivité SSL dans Azure Database pour PostgreSQL - Serveur unique

La base de données Azure pour PostgreSQL préfère connecter vos applications clientes au service PostgreSQL via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

Par défaut, le service de base de données PostgreSQL est configuré de façon à exiger une connexion SSL. Vous pouvez choisir de désactiver l’utilisation du protocole SSL si votre application cliente ne prend pas en charge ce type de connectivité.

## <a name="enforcing-ssl-connections"></a>Appliquer les connexions SSL

Pour tous les serveurs de base de données Azure pour PostgreSQL approvisionnés par le biais du Portail Azure et d’Azure CLI, l’application de connexions SSL est activée par défaut. 

De même, les chaînes de connexion prédéfinies dans les paramètres « Chaînes de connexion » du serveur sur le Portail Azure incluent les paramètres requis par les langages courants pour se connecter au serveur de base de données via SSL. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

## <a name="configure-enforcement-of-ssl"></a>Configuration de l’application du protocole SSL

Si vous le souhaitez, vous pouvez désactiver l’application de la connectivité SSL. Microsoft Azure recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Accédez à votre serveur de base de données Azure pour PostgreSQL et cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**. Ensuite, cliquez sur **Enregistrer**.

![Sécurité de connexion - Désactiver l’application de la connexion SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Vous pouvez vérifier le paramètre en consultant la page **Vue d’ensemble** pour voir si l’indicateur d **’état de d’application SSL** est affiché.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

Vous pouvez activer ou désactiver le paramètre **ssl-mise en œuvre** en utilisant respectivement les valeurs `Enabled` ou `Disabled` dans Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions SSL

Certaines infrastructures d’applications courantes qui utilisent PostgreSQL pour leurs services de base de données n’activent pas le protocole SSL par défaut lors de l’installation. Si votre serveur PostgreSQL applique des connexions SSL, mais que l’application n’est pas configurée pour cela, cette dernière risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applications nécessitant la vérification du certificat pour la connectivité SSL

Dans certains cas, les applications nécessitent un fichier de certificat local généré à partir du fichier de certificat (.cer) d’une autorité de certification (AC) approuvée pour se connecter en toute sécurité. Vous trouverez le certificat permettant la connexion à un serveur Azure Database pour PostgreSQL ici : https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Télécharger le fichier du certificat et enregistrez-le à l’emplacement par défaut.

### <a name="connect-using-psql"></a>Connexion via psql

L’exemple suivant montre comment vous connecter à votre serveur PostgreSQL à l’aide de l’utilitaire de ligne de commande psql. Utilisez le paramètre de chaîne de connexion `sslmode=verify-full` pour appliquer la vérification du certificat SSL. Passez le chemin d’accès du fichier de certificat local au paramètre `sslrootcert`.

La commande suivante est un exemple de chaîne de connexion pqsl :

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Vérifiez que la valeur passée à `sslrootcert` correspond au chemin de fichier du certificat que vous avez enregistré.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les différentes options de connectivité d’application de la rubrique [Bibliothèques de connexions d’Azure Database pour PostgreSQL](concepts-connection-libraries.md).
