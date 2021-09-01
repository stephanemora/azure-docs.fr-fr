---
title: Connectivité via SCRAM dans Azure Database pour PostgreSQL - Serveur flexible
description: Instructions et informations sur la configuration et la connexion à l’aide du protocole SCRAM dans Azure Database pour PostgreSQL - Serveur flexible.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 7fc67ab7162c7fe62df226fd39aefcd350bbdf82
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112897243"
---
# <a name="scram-authentication-in-azure-database-for-postgresql---flexible-server"></a>Authentification SCRAM dans Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en préversion

Salted Challenge Response Authentication Mechanism (SCRAM) est un protocole d’authentification mutuelle basé sur mot de passe. Il s’agit d’un modèle de stimulation-réponse qui ajoute plusieurs niveaux de sécurité et empêche la détection de mot de passe sur des connexions non approuvées. SCRAM prend en charge le stockage des mots de passe sur le serveur dans un format haché par chiffrement qui fournit une sécurité avancée. 

Pour accéder au serveur de base de données PostgreSQL à l’aide de la méthode d’authentification SCRAM, vos bibliothèques clientes doivent prendre en charge SCRAM.  Consultez la [liste des pilotes](https://wiki.postgresql.org/wiki/List_of_drivers) qui prennent en charge SCRAM.

## <a name="configuring-scram-authentication"></a>Configuration de l’authentification SCRAM

1. Remplacez password_encryption par SCRAM-SHA-256. Actuellement, PostgreSQL prend en charge uniquement SCRAM avec SHA-256.
        :::image type="content" source="./media/how-to-configure-scram/1-password-encryption.png" alt-text="Activer le chiffrement de mot de passe SCRAM"::: 
2. Autorisez SCRAM-SHA-256 comme méthode d’authentification.
        :::image type="content" source="./media/how-to-configure-scram/2-auth-method.png" alt-text="Choisissez la méthode d’authentification"::: 
    >[!Important]
    > Vous pouvez choisir d’appliquer l’authentification SCRAM uniquement en sélectionnant seulement la méthode SCRAM-SHA-256. En procédant ainsi, les utilisateurs disposant d’une authentification MD5 ne peuvent plus se connecter au serveur. Par conséquent, avant d’appliquer SCRAM, il est recommandé d’avoir à la fois MD5 et SCRAM-SHA-256 comme méthodes d’authentification jusqu’à ce que vous ayez mis à jour tous les mots de passe utilisateur vers SCRAM-SHA-256. Vous pouvez vérifier le type d’authentification pour les utilisateurs à l’aide de la requête mentionnée à l’étape 7.
3. Enregistrez les modifications. Il s’agit de propriétés dynamiques qui ne nécessitent pas de redémarrage du serveur.
4. À partir de votre client Postgres, connectez-vous au serveur Postgres. Par exemple,
   
    ```bash
    psql "host=myPGServer.postgres.database.azure.com port=5432 dbname=postgres user=myDemoUser password=MyPassword sslmode=require"

    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 12.6)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    ```

5. Vérifiez le chiffrement du mot de passe.
   
    ```SQL
    postgres=> show password_encryption;
     password_encryption
    ---------------------
    scram-sha-256
    (1 row)
    ```

6. Vous pouvez ensuite mettre à jour le mot de passe pour les utilisateurs.

    ```SQL
    postgres=> \password myDemoUser
    Enter new password:
    Enter it again:
    postgres=>
    ```

7. Vous pouvez vérifier les types d’authentification utilisateur à l’aide de la fonction `azure_roles_authtype()`. 

    ``` SQL
    postgres=> SELECT * from azure_roles_authtype();
            rolename          | authtype
    ---------------------------+-----------
    azuresu                   | NOLOGIN
    pg_monitor                | NOLOGIN
    pg_read_all_settings      | NOLOGIN
    pg_read_all_stats         | NOLOGIN
    pg_stat_scan_tables       | NOLOGIN
    pg_read_server_files      | NOLOGIN
    pg_write_server_files     | NOLOGIN
    pg_execute_server_program | NOLOGIN
    pg_signal_backend         | NOLOGIN
    replication               | NOLOGIN
    myDemoUser                | SCRAM-256
    azure_pg_admin            | NOLOGIN
    srtest                    | SCRAM-256
    sr_md5                    | MD5
    (14 rows)
    ```

8. Vous pouvez alors vous connecter à partir du client qui prend en charge l’authentification SCRAM sur votre serveur.

## <a name="next-steps"></a>Étapes suivantes
- [Créer et gérer un réseau virtuel Azure Database pour PostgreSQL - Serveur flexible à l’aide de Azure CLI](./how-to-manage-virtual-network-cli.md).
- En savoir plus sur la [mise en réseau dans Azure Database pour PostgreSQL - Serveur flexible](./concepts-networking.md).
- En savoir plus sur les [règles de pare-feu pour Azure Database pour PostgreSQL - Serveur flexible](./concepts-networking.md#public-access-allowed-ip-addresses).
