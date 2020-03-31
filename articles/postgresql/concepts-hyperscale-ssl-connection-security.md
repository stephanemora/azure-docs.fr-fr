---
title: SSL - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Instructions et informations de configuration pour Azure Database pour PostgreSQL (Hyperscale (Citus)) et les applications associées afin d’utiliser correctement les connexions SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973983"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurer SSL dans Azure Database pour PostgreSQL - Hyperscale (Citus)
Les connexions des applications clientes au nœud coordinateur Hyperscale (Citus) nécessitent le protocole SSL. L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="enforcing-ssl-connections"></a>Appliquer les connexions SSL
Pour tous les serveurs Azure Database pour PostgreSQL provisionnés par le biais du portail Azure, l’application de connexions SSL est activée par défaut. 

De même, les chaînes de connexion prédéfinies dans les paramètres « Chaînes de connexion » du serveur sur le Portail Azure incluent les paramètres requis par les langages courants pour se connecter au serveur de base de données via SSL. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions SSL
Certaines infrastructures d’applications courantes qui utilisent PostgreSQL pour leurs services de base de données n’activent pas le protocole SSL par défaut lors de l’installation. Si votre serveur PostgreSQL applique des connexions SSL, mais que l’application n’est pas configurée pour cela, cette dernière risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applications nécessitant la vérification du certificat pour la connectivité SSL
Dans certains cas, les applications nécessitent un fichier de certificat local généré à partir du fichier de certificat (.cer) d’une autorité de certification (AC) approuvée pour se connecter en toute sécurité. Vous trouverez le certificat permettant la connexion à une base de données Azure pour PostgreSQL - Hyperscale (Citus) sur https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Télécharger le fichier du certificat et enregistrez-le à l’emplacement par défaut.

### <a name="connect-using-psql"></a>Connexion via psql
L’exemple suivant montre comment se connecter au nœud coordinateur Hyperscale (Citus) à l’aide de l’utilitaire de ligne de commande psql. Utilisez le paramètre de chaîne de connexion `sslmode=verify-full` pour appliquer la vérification du certificat SSL. Passez le chemin d’accès du fichier de certificat local au paramètre `sslrootcert`.

Voici un exemple de chaîne de connexion pqsl :
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Vérifiez que la valeur passée à `sslrootcert` correspond au chemin de fichier du certificat que vous avez enregistré.

## <a name="next-steps"></a>Étapes suivantes
Renforcez la sécurité avec les [règles de pare-feu dans Azure Database pour PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
