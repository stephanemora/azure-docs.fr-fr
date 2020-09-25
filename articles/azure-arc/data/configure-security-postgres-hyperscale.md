---
title: Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc
description: Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b166348031e9f72e8005e866a198855db9c01a9c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930201"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc

Ce document décrit différents aspects liés à la sécurité de votre groupe de serveurs :
- Chiffrement au repos
- User Management
   - Perspectives générales
   - Modifier le mot de passe de l’utilisateur administratif _postgres_

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Chiffrement au repos
Vous pouvez implémenter un chiffrement au repos en chiffrant les disques sur lesquels vous stockez vos bases de données et/ou en utilisant des fonctions de base de données pour chiffrer les données que vous insérez ou mettez à jour.

### <a name="hardware-linux-host-volume-encryption"></a>Matériel : Chiffrement du volume hôte Linux
Implémentez un chiffrement des données système pour sécuriser les données résidant sur les disques utilisés par votre configuration de Data Services activés par Azure Arc. Pour en savoir plus, consultez la rubrique suivante :
- [Chiffrement des données au repos](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) sur Linux en général 
- Chiffrement de disque avec la commande de chiffrement LUKS `cryptsetup` (Linux) (spécifiquement https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) ) Les Data Services activés par Azure Arc s’exécutant sur l’infrastructure physique que vous fournissez, vous êtes en charge de la sécurisation de l’infrastructure.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Logiciels : Utiliser l’extension de `pgcrypto` PostgreSQL dans votre groupe de serveurs
En plus de chiffrer les disques utilisés pour héberger votre installation d’Azure Arc, vous pouvez configurer votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc pour exposer des mécanismes que vos applications peuvent utiliser pour chiffrer les données figurant dans vos bases de données. L’extension `pgcrypto` fait partie des extensions `contrib` de Postgres et est disponible dans votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc. Vous trouverez plus d’informations sur l’extension `pgcrypto` [ici](https://www.postgresql.org/docs/current/pgcrypto.html).
En résumé, les commandes suivantes vous permettent d’activer l’extension, de la créer et de l’utiliser :


#### <a name="create-the-pgcrypto-extension"></a>Créer l’extension `pgcrypto`
Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :
```console
CREATE EXTENSION pgcrypto;
```

> Vous trouverez [ici](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) des détails sur la façon de vous connecter.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Vérifier la liste des extensions prêtes à l’emploi dans votre groupe de serveurs
Vous pouvez vérifier que l’extension de `pgcrypto` est prête à l’emploi en affichant la liste des extensions disponibles dans votre groupe de serveurs.
Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :
```console
select * from pg_extension;
```
Consultez `pgcrypto` si vous l’avez créée et activée avec les commandes indiquées ci-dessus.

#### <a name="use-the-pgcrypto-extension"></a>Utiliser l’extension `pgcrypto`
Vous pouvez maintenant ajuster le code de vos applications afin qu’elles utilisent tout fonction offerte par `pgcrypto`:
- fonctions de hachage générales ;
- fonctions de hachage de mot de passe ;
- fonctions de chiffrement PGP ;
- Fonctions de chiffrement brut ;
- fonctions de données aléatoires.

Par exemple, pour générer des valeurs de hachage. Exécutez la commande suivante :

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Retourne le hachage suivant :

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Ou, par exemple :

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

Retourne le hachage suivant :

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Ou, par exemple, pour stocker des données chiffrées telles qu’un mot de passe :

Supposons que mon application stocke les secrets dans le tableau suivant :

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

Et que je chiffre leur mot de passe lors de la création d’un utilisateur :

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Maintenant, je vois que mon mot de passe est chiffré :

```console
select * from mysecrets;
```

Sortie :

- USERid : 1
- USERname : Moi
- USERpassword : $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31

Lorsque je me connecte avec mon application et entre un mot de passe, l’application recherche dans la table `mysecrets` et retourne le nom de l’utilisateur s’il existe une correspondance entre le mot de passe fourni à l’application et les mots de passe stockés dans la table. Exemple :

- J’entre un mot de passe incorrect :
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Sortie 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- J’entre le mot de passe correct :

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Sortie :

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Ce petit exemple montre que vous pouvez chiffrer des données au repos (stocker des données chiffrées) dans PostgreSQL Hyperscale activé par Azure Arc à l’aide de l’extension `pgcrypto` Postgres, et que vos applications peuvent utiliser les fonctions offertes par `pgcrypto` pour manipuler ces données chiffrées.

## <a name="user-management"></a>User Management
### <a name="general-perspectives"></a>Perspectives générales
Vous pouvez utiliser la méthode Postgres standard pour créer des utilisateurs ou des rôles. Toutefois, si vous procédez de la sorte, ces artefacts seront disponibles uniquement sur le rôle coordinateur. Pendant la période de préversion, ces utilisateurs/rôles ne pourront pas encore accéder aux données distribuées en dehors du nœud coordinateur et sur les nœuds Worker de votre groupe de serveurs. En effet, dans la préversion, la définition d’utilisateur n’est pas répliquée sur les nœuds Worker.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Modifier le mot de passe de l’utilisateur administratif _postgres_
PostgreSQL Hyperscale avec Azure Arc est fourni avec l’utilisateur administratif Postgres standard, _postgres_, pour lequel vous définissez le mot de passe lors de la création de votre groupe de serveurs.
Le format général de la commande pour modifier le mot de passe est le suivant :
```console
azdata arc postgres server edit --name <server group name> --admin-password <new password>
```
Le mot de passe sera défini sur la valeur de la variable d’environnement de la **session** AZDATA_PASSWORD s’il existe. Si ce n’est pas le cas, l’utilisateur sera invité à entrer une valeur.
Pour vérifier si la variable d’environnement de la session AZDATA_PASSWORD existe et/ou la valeur sur laquelle elle est définie, exécutez la commande suivante :
```console
printenv AZDATA_PASSWORD
```
Vous souhaiterez peut-être supprimer sa valeur si vous préférez être invité à entrer un nouveau mot de passe.


## <a name="next-steps"></a>Étapes suivantes
- Lisez les détails sur l’extension `pgcrypto` [ici](https://www.postgresql.org/docs/current/pgcrypto.html).
- Lisez les détails sur l’utilisation des extensions Postgres [ici](using-extensions-in-postgresql-hyperscale-server-group.md).

