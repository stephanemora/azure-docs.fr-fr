---
title: Connectivité SSL/TLS – Azure Database for MariaDB
description: Informations de configuration d’Azure Database for MariaDB et des applications associées afin d’utiliser correctement les connexions SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 5072710378d0a179b3b96ae9b698e9a92d81cf44
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290238"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>Connectivité SSL/TLS dans Azure Database for MariaDB
Azure Database for MariaDB prend en charge la connexion de votre serveur de base de données aux applications clientes via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="default-settings"></a>Paramètres par défaut
Par défaut, le service de base de données doit être configuré pour exiger des connexions SSL lors de la connexion à MariaDB.  Nous vous recommandons d’éviter de désactiver l’option SSL dans la mesure du possible.

Quand vous provisionnez un nouveau serveur Azure Database for MariaDB par le biais du portail Azure et de l’interface CLI, l’application de connexions SSL est activée par défaut.

Dans certains cas, les applications nécessitent un fichier de certificat local généré à partir du fichier de certificat d’une autorité de certification (AC) approuvée pour se connecter en toute sécurité. Actuellement, les clients **ne peuvent utiliser que** le certificat prédéfini pour se connecter à un serveur Azure Database for MariaDB situé à l’adresse https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. 

De même, les liens suivants pointent vers les certificats pour les serveurs des clouds souverains : [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Chine](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) et [Azure Allemagne](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Les chaînes de connexion pour les différents langages de programmation sont affichées dans le portail Azure. Ces chaînes de connexion incluent les paramètres SSL requis pour se connecter à votre base de données. Dans le portail Azure, sélectionnez votre serveur. Sous l’en-tête **Paramètres**, sélectionnez les **Chaînes de connexion**. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

Pour découvrir comment activer ou désactiver la connexion SSL pour le développement d’applications, consultez la page [Guide pratique : configurer SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Imposition du protocole TLS dans Azure Database for MariaDB

Azure Database for MariaDB prend en charge le chiffrement pour les clients qui se connectent à votre serveur de base de données à l’aide du protocole TLS (Transport Layer Security). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité.

### <a name="tls-settings"></a>Paramètres TLS

Azure Database for MariaDB offre la possibilité d’imposer la version du protocole TLS pour les connexions clientes. Pour imposer la version du protocole TLS, utilisez le paramètre d’option **Version TLS minimale**. Les valeurs suivantes sont autorisées pour ce paramètre d’option :

|  Paramètre TLS minimal             | Version TLS du client prise en charge                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (par défaut) | Aucun protocole TLS requis                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 et versions ultérieures         |
| TLS1_1                           | TLS 1.1, TLS 1.2 et versions ultérieures              |
| TLS1_2                           | TLS versions 1.2 et ultérieures                  |


Par exemple, la définition de la valeur du paramètre de version minimale du protocole TLS sur TLS 1.0 signifie que votre serveur autorise les connexions en provenance des clients utilisant les protocoles TLS 1.0, 1.1 et 1.2+. Si vous choisissez la valeur 1.2, en revanche, cela signifie que vous autorisez uniquement les connexions en provenance des clients utilisant TLS 1.2+ et que toutes les connexions à l’aide des protocoles TLS 1.0 et TLS 1.1 sont rejetées.

> [!Note] 
> Par défaut, Azure Database for MariaDB n’applique aucune version TLS minimale (le paramètre `TLSEnforcementDisabled`).
>
> Après avoir appliqué une version TLS minimale, vous ne pouvez plus la désactiver.

Pour découvrir comment définir le paramètre TLS pour Azure Database for MariaDB, consultez le [guide pratique pour configurer le paramètre TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [règles de pare-feu du serveur](concepts-firewall-rules.md)
- Découvrez comment [configurer le protocole SSL](howto-configure-ssl.md)
- Découvrez comment [configurer le protocole TLS](howto-tls-configurations.md)
