---
title: Obtenir des points de terminaison de connexion et former des chaînes de connexion pour votre groupe de serveurs Arc enabled PostgreSQL Hyperscale
titleSuffix: Azure Arc-enabled data services
description: Obtenir des points de terminaison de connexion et former des chaînes de connexion pour votre groupe de serveurs Arc enabled PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 964b7fcca00afb91a457203d2ed53b885a254d5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524497"
---
# <a name="get-connection-endpoints-and-form-the-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Obtenir des points de terminaison de connexion et former des chaînes de connexion pour votre groupe de serveurs Arc enabled PostgreSQL Hyperscale

Cet article explique comment récupérer les points de terminaison de connexion pour votre groupe de serveurs et comment former les chaînes de connexion que vous pourrez utiliser avec vos applications et/ou vos outils.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Obtenez les points de terminaison de connexion :

Exécutez la commande suivante :
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Par exemple :
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```

Elle affiche la liste des points de terminaison : le point de terminaison PostgreSQL que vous utilisez pour connecter votre application et utiliser la base de données, les points de terminaison Kibana et Grafana pour l’analytique et la surveillance des journaux. Par exemple : 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Utilisez ces points de terminaison pour :
- Former vos chaînes de connexion et vous connecter avec vos outils clients ou vos applications clientes
- Accéder aux tableaux de bord Grafana et Kibana depuis votre navigateur

Par exemple, vous pouvez utiliser le point de terminaison nommé _Instance PostgreSQL_ pour vous connecter avec psql à votre groupe de serveurs. Exemple :
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Le mot de passe de l’utilisateur _postgres_ indiqué dans le point de terminaison nommé « _Instance PostgreSQL_ » est le mot de passe que vous avez choisi lors du déploiement du groupe de serveurs.
> _ERREUR : (401)_ 
> _Raison : Non autorisé_
> _En-têtes de réponse HTTP : HTTPHeaderDict({'Date': 'Sun, 06 Sep 2020 16:58:38 GMT', 'Content-Length': '0', 'WWW-Authenticate': '_ 
> _Domaine de base="Login_ credentials required", Erreur de porteur="invalid_token", error_description="The token is expired"'})_ Quand ceci se produit, vous devez vous reconnecter avec azdata comme expliqué ci-dessus.

## <a name="from-cli-with-kubectl"></a>Depuis l’interface CLI avec kubectl
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

Ces commandes produisent une sortie similaire à celle-ci. Vous pouvez utiliser ces informations pour former vos chaînes de connexion :
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 

## <a name="form-connection-strings"></a>Formez des chaînes de connexion :
Utilisez le tableau de modèles de chaînes de connexion ci-dessous pour votre groupe de serveurs. Vous pouvez ensuite les copier/coller et les personnaliser en fonction des besoins :

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [effectuer un scale-out (ajout de nœuds Worker)](scale-out-in-postgresql-hyperscale-server-group.md) de votre groupe de serveurs
- Découvrez comment [effectuer un scale-up ou un scale-down (augmentation/diminution de la mémoire/des vCores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) de votre groupe de serveurs


