---
title: Décodage logique – Azure Database pour PostgreSQL – Serveur unique
description: Décrit le décodage logique et wal2json pour la capture des changements de données dans Azure Database pour PostgreSQL – Serveur unique
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522573"
---
# <a name="logical-decoding"></a>Décodage logique
 
Le [décodage logique dans PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) vous permet de diffuser en continu des changements de données vers des contrôles serveur consommateurs externes. Le décodage logique est couramment utilisé pour la diffusion en continu d’événements et les scénarios de capture des changements de données.

Le décodage logique utilise un plug-in de sortie pour convertir les journaux d’écriture anticipée (WAL, Write Ahead Log) de Postgres dans un format lisible. Azure Database pour PostgreSQL fournit deux plug-ins de sortie : [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) et [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Le décodage logique est en préversion publique sur Azure Database pour PostgreSQL – Serveur unique.


## <a name="set-up-your-server"></a>Configurer votre serveur
Pour commencer à utiliser le décodage logique, autorisez votre serveur à enregistrer et diffuser en continu le WAL. 

1. Définissez azure.replication_support sur `logical` à l’aide d’Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Si vous utilisez des réplicas en lecture, azure.replication_support défini sur `logical` permet également l’exécution des réplicas. Si vous arrêtez d’utiliser le décodage logique, redéfinissez le paramètre sur `replica`. 


2. Redémarrez le serveur pour appliquer les modifications.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Démarrer le décodage logique

Le décodage logique peut être utilisé via un protocole de diffusion en continu ou une interface SQL. Les deux méthodes utilisent des [emplacements de réplication](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Un emplacement représente le flux de modifications d’une base de données unique.

L’utilisation d’un emplacement de réplication nécessite des privilèges de réplication Postgres. À ce stade, le privilège de réplication est uniquement disponible pour l’utilisateur administrateur du serveur. 

### <a name="streaming-protocol"></a>Protocole de diffusion en continu
Il est souvent préférable de consommer les modifications à l’aide du protocole de diffusion en continu. Vous pouvez créer votre propre connecteur ou contrôle serveur consommateur, ou utiliser un outil comme [Debezium](https://debezium.io/). 

Consultez la documentation wal2json pour obtenir [un exemple utilisant le protocole de diffusion en continu avec pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interface SQL
Dans l’exemple ci-dessous, nous utilisons l’interface SQL avec le plug-in wal2json.
 
1. Créez un emplacement.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Émettez des commandes SQL. Par exemple :
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Consommez les modifications.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   La sortie se présente ainsi :
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Supprimez l’emplacement une fois que vous avez fini de l’utiliser.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Surveillance des emplacements

Vous devez surveiller le décodage logique. Tout emplacement de réplication non utilisé doit être supprimé. Les emplacements sont bloqués dans les journaux WAL Postgres et les catalogues système appropriés jusqu’à ce que les modifications aient été lues par un contrôle serveur consommateur. Si votre contrôle serveur consommateur échoue ou n’a pas été correctement configuré, les journaux non consommés s’accumulent et remplissent votre stockage. En outre, les journaux non consommés augmentent le risque d’un bouclage des ID de transaction. Les deux situations peuvent entraîner l’indisponibilité du serveur. Par conséquent, il est essentiel que les emplacements de réplication logique soient consommés en permanence. Si un emplacement de réplication logique n’est plus utilisé, supprimez-le immédiatement.

La colonne « active » de l’affichage pg_replication_slots indique si un contrôle serveur consommateur est connecté à un emplacement.
```SQL
SELECT * FROM pg_replication_slots;
```

[Définissez des alertes](howto-alert-on-metric.md) sur les mesures *Stockage utilisé* et *Décalage maximal entre les réplicas* pour vous avertir lorsque les valeurs augmentent au-delà des seuils normaux. 

> [!IMPORTANT]
> Vous devez supprimer les emplacements de réplication non utilisés. Si vous ne le faites pas, cela peut entraîner l’indisponibilité du serveur.

## <a name="how-to-drop-a-slot"></a>Procédure de suppression d’un emplacement
Si vous ne consommez pas activement un emplacement de réplication, vous devez le supprimer.

Pour supprimer un emplacement de réplication appelé `test_slot` à l’aide de SQL :
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Si vous arrêtez d’utiliser le décodage logique, redéfinissez azure.replication_support sur `replica` ou `off`. Les détails du WAL conservés par `logical` sont plus détaillés et doivent être désactivés lorsque le décodage logique n’est pas utilisé. 

 
## <a name="next-steps"></a>Étapes suivantes

* Consultez la documentation Postgres pour [en savoir plus sur le décodage logique](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Si vous avez des questions sur le décodage logique, contactez [notre équipe](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
* Découvrir plus en détail les [réplicas en lecture](concepts-read-replicas.md).

