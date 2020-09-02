---
title: Déployer Azure SQL Edge (préversion) à l’aide du portail Azure
description: Découvrez comment déployer Azure SQL Edge (préversion) à l’aide du portail Azure
keywords: déployer SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816862"
---
# <a name="deploy-azure-sql-edge-preview"></a>Déployer Azure SQL Edge (préversion) 

Azure SQL Edge (préversion) est un moteur de base de données relationnelle optimisé pour les déploiements IoT et Azure IoT Edge. Il offre des fonctionnalités permettant de créer une couche de traitement et de stockage des données hautes performances pour les solutions et applications IoT. Ce guide de démarrage rapide vous montre comment prendre en main la création d’un module Azure SQL Edge via Azure IoT Edge à l’aide du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).
* Connectez-vous au [portail Azure](https://portal.azure.com/).
* Créez une ressource [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Inscrivez un [appareil IoT Edge à partir du Portail Azure](../iot-edge/how-to-register-device-portal.md).
* Préparez l’appareil IoT Edge pour [déployer un module IoT Edge à partir du Portail Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Pour déployer une machine virtuelle Linux Azure en tant qu’appareil IoT Edge, consultez ce [guide de démarrage rapide](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Déployer le module SQL Edge à partir de la Place de marché Azure

La Place de marché Azure est un marché d’applications et de services en ligne qui vous permet de naviguer au milieu d’un large éventail d’applications et de solutions d’entreprise certifiées et optimisées pour s’exécuter sur Azure, notamment les [modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Edge peut être déployé sur un périphérique via la Place de marché.

1. Recherchez le module Azure SQL Edge sur la Place de marché Azure.<br><br>

   ![SQL Edge sur la Place de marché](media/deploy-portal/find-offer-marketplace.png)

2. Sélectionnez l’abonnement logiciel qui correspond le mieux à vos besoins, puis cliquez sur **Créer**. <br><br>

   ![Sélectionner l’abonnement logiciel approprié](media/deploy-portal/pick-correct-plan.png)

3. Sur la page des appareils cibles pour le module IoT Edge, spécifiez les informations suivantes, puis cliquez sur **Créer**

   |**Champ**  |**Description**  |
   |---------|---------|
   |Abonnement  |  L’abonnement Azure dans lequel l’IoT Hub a été créé |
   |IoT Hub   |  Nom de l’IoT Hub où l’appareil IoT Edge est inscrit. Sélectionnez ensuite l’option « Déployer sur un appareil »|
   |Nom de l’appareil IoT Edge  |  Nom de l’appareil IoT Edge sur lequel SQL Edge sera déployé |

4. Sur la page **Définir des modules**, accédez à la section sur les modules de déploiement, puis cliquez sur **Configurer** en regard du module SQL Edge. 

5. Dans le volet **Modules personnalisés IoT Edge**, spécifiez les valeurs souhaitées pour les variables d’environnement et/ou personnalisez les options de création et les propriétés souhaitées pour le module. Pour obtenir la liste complète des variables d’environnement prises en charge, consultez [Configure SQL Server settings with environment variables on Linux](/sql/linux/sql-server-linux-configure-environment-variables/) (Configurer les paramètres SQL Server avec les variables d’environnement sur Linux).

   |**Paramètre**  |**Description**|
   |---------|---------|
   | Nom | Nom du module. |
   |SA_PASSWORD  | Spécifiez un mot de passe fort pour le compte administrateur SQL Edge. |
   |MSSQL_LCID   | Définit l’ID de langue à utiliser pour SQL Server. Par exemple, 1036 correspond au français. |
   |MSSQL_COLLATION | Définit le classement par défaut pour SQL Server. Ce paramètre remplace le mappage par défaut de l’ID de langue (LCID) par le classement. |

   > [!NOTE]
   > Ne modifiez pas ou ne mettez pas à jour **l’URI d’image** ou les paramètres **ACCEPT_EULA** sur le module.

6. Dans le volet **Modules personnalisés IoT Edge**, mettez à jour la valeur souhaitée des options de création de conteneur pour le **Port hôte**. Si vous devez déployer plusieurs modules SQL DB Edge, veillez à mettre à jour l’option Mounts pour créer une paire source et cible pour le volume persistant. Pour plus d’informations sur les montages et le volume, consultez [Utiliser des volumes](https://docs.docker.com/storage/volumes/) sur la documentation Docker. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. Dans le volet **Modules personnalisés IoT Edge**, mettez à jour *Set module twin’s desired properties* (Définir les propriétés souhaitées du jumeau de module) pour inclure l’emplacement du package SQL et les informations relatives au travail Stream Analytics. Ces deux champs sont facultatifs et doivent être utilisés si vous souhaitez déployer le module SQL Edge avec une base de données et un travail de diffusion en continu.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Dans le volet **Modules personnalisés IoT Edge**, définissez *Stratégie de redémarrage* sur « toujours » et *Statut souhaité* sur « en cours d’exécution ».
9. Dans le volet **Modules personnalisés IoT Edge**, cliquez sur **Enregistrer**.
10. Sur la page **Définir des modules**, cliquez sur **Suivant**.
11. Dans la section **Specify Route (optional)** (Spécifier l’itinéraire (facultatif)) de la page **Définir des modules**, spécifiez les itinéraires pour la communication module-module ou module-IoT Edge Hub ; consultez [Découvrez comment déployer des modules et établir des itinéraires dans IoT Edge](../iot-edge/module-composition.md).
12. Cliquez sur **Suivant**.
13. Cliquez sur **Envoyer**.

## <a name="connect-to-azure-sql-edge"></a>Se connecter à Azure SQL Edge

La procédure suivante utilise l’outil en ligne de commande Azure SQL Edge, **sqlcmd**, dans le conteneur pour se connecter à Azure SQL Edge.

> [!NOTE]
> L’outil sqlcmd n’est pas disponible dans la version ARM64 des conteneurs SQL Edge.

1. Utilisez la commande `docker exec -it` pour démarrer un interpréteur de commandes bash interactif dans votre conteneur en cours d’exécution. Dans l’exemple suivant, `azuresqledge` est le nom spécifié par le paramètre `Name` de votre module IoT Edge.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Une fois dans le conteneur, connectez-vous localement avec sqlcmd. Sqlcmd n’est pas dans le chemin par défaut, vous devez spécifier le chemin complet.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Vous pouvez omettre le mot de passe sur la ligne de commande pour être invité à l’entrer.

3. Si l’opération réussit, vous devez accéder à une invite de commandes **sqlcmd** : `1>`.

## <a name="create-and-query-data"></a>Créer et interroger des données

Les sections suivantes vous guident lors de l’utilisation de **sqlcmd** et Transact-SQL pour créer une base de données, ajouter des données et exécuter une requête simple.

### <a name="create-a-new-database"></a>Créer une base de données

La procédure suivante crée une base de données nommée `TestDB`.

1. À partir de l’invite de commandes **sqlcmd**, collez la commande Transact-SQL suivante pour créer une base de données de test :

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Sur la ligne suivante, écrivez une requête pour retourner le nom de toutes les bases de données sur votre serveur :

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Insertion des données

Créez ensuite une table, `Inventory`, et insérez deux nouvelles lignes.

1. À partir de l’invite de commandes **sqlcmd**, basculez le contexte vers la nouvelle base de données `TestDB` :

   ```sql
   USE TestDB
   ```

2. Créez une table nommée `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Insérez des données dans la nouvelle table :

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Tapez `GO` pour exécuter les commandes précédentes :

   ```sql
   GO
   ```

### <a name="select-data"></a>Sélectionner les données

Exécutez maintenant une requête pour retourner des données de la table `Inventory`.

1. Dans l’invite de commandes **sqlcmd**, entrez une requête qui retourne les lignes de la table `Inventory` dont la quantité est supérieure à 152 :

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Exécutez la commande :

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Quitter l’invite de commandes sqlcmd

1. Pour mettre fin à votre session **sqlcmd**, tapez `QUIT` :

   ```sql
   QUIT
   ```

2. Pour quitter l’invite de commandes interactive dans votre conteneur, tapez `exit`. Le conteneur continue de s’exécuter une fois que vous avez quitté l’interpréteur de commandes bash interactif.

## <a name="connect-from-outside-the-container"></a> Se connecter en dehors du conteneur

Vous pouvez vous connecter et exécuter des requêtes SQL sur votre instance Azure SQL Edge à partir de n’importe quel outil externe Linux, Windows ou macOS qui prend en charge les connexions SQL. Pour plus d’informations sur la connexion à un conteneur SQL Edge depuis l’extérieur, consultez [Connecter et interroger Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/connect).

Dans ce guide de démarrage rapide, vous avez déployé un module SQL Edge sur un appareil IoT Edge. 

## <a name="next-steps"></a>Étapes suivantes

- [Machine Learning et intelligence artificielle avec ONNX dans SQL Edge](onnx-overview.md).
- [Création d’une solution IoT de bout en bout avec SQL Edge à l’aide d’IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de données dans Azure SQL Edge](stream-data.md)
