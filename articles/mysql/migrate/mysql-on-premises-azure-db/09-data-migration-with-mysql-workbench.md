---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Migration des données avec MySQL Workbench
description: Suivez toutes les étapes du guide d’installation pour créer un environnement permettant de prendre en charge les étapes suivantes.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: 485a377decee390701cb43a99bd47e96f29f1f55
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082754"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-data-migration-with-mysql-workbench"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Migration des données avec MySQL Workbench

## <a name="prerequisites"></a>Prérequis

[Migration des données](08-data-migration.md)

## <a name="setup"></a>Programme d’installation

Suivez toutes les étapes du guide d’installation pour créer un environnement permettant de prendre en charge les étapes suivantes.

## <a name="configuring-server-parameters-source"></a>Configuration des paramètres du serveur (Source)

Selon le type de migration que vous avez choisi (hors connexion ou en ligne), vous devez évaluer si vous allez modifier les paramètres du serveur pour prendre en charge une sortie rapide des données. Si vous effectuez une migration en ligne, il n’est pas nécessaire de modifier les paramètres du serveur, car vous risquez d’effectuer une réplication `binlog` et une synchronisation automatique des données. Toutefois, si vous effectuez une migration hors connexion, une fois que vous avez arrêté le trafic de l’application, vous pouvez modifier les paramètres du serveur pour passer de la prise en charge de la charge de travail à la prise en charge de l’exportation.

## <a name="configuring-server-parameters-target"></a>Configuration des paramètres du serveur (Cible)

Passez en revue les paramètres du serveur avant de commencer le processus d’importation dans Azure Database pour MySQL. Les paramètres du serveur peuvent être récupérés et définis à l’aide du [portail Azure](/azure/mysql/howto-server-parameters) ou en appelant les [cmdlets Azure PowerShell pour MySQL](/azure/mysql/howto-configure-server-parameters-using-powershell) afin d’effectuer les modifications.

Exécutez le script PowerShell suivant pour obtenir tous les paramètres :

```
\[Net.ServicePointManager\]::SecurityProtocol = \[Net.SecurityProtocolType\]::Tls
12

Install-Module -Name Az.MySql
Connect-AzAccount
$rgName = "{RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";
Get-AzMySqlConfiguration -ResourceGroupName $rgName -ServerName $serverName
```

- Pour faire de même avec l’outil mysql, téléchargez la [certification racine de l’autorité de certification](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) sur c:\\temp (créez ce répertoire).

    > [!NOTE]
    > Le certificat est susceptible d’être modifié. Pour obtenir les informations les plus récentes concernant le certificat, consultez [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à Azure Database pour MySQL](/azure/mysql/howto-configure-ssl).
    
- Exécutez le script suivant dans une invite de commandes en veillant à mettre à jour les jetons :

```
mysql --host {servername}.mysql.database.azure.com --database mysql --user 
{u sername}@{servername} -p --ssl-ca=c:\\temp\\BaltimoreCyberTrustRoot.crt.cer
-A -e "SHOW GLOBAL VARIABLES;" \> c:\\temp\\settings\_azure.txt
```

Dans le nouveau fichier \`settings\_azure.txt\`, vous pouvez voir les paramètres par défaut du serveur Azure Database pour MySQL, comme indiqué dans l’[annexe](15-appendix.md#environment-setup).

Pour prendre en charge la migration, définissez les paramètres de l’instance MySQL cible de façon à permettre une entrée plus rapide. Les paramètres de serveur suivants doivent être définis avant de commencer la migration des données :

- `max\_allowed\_packet` : Définissez le paramètre sur `1073741824` (autrement dit, 1 Go) ou la plus grande taille d’une ligne dans la base de données afin d’éviter tout problème de dépassement dû à de longues lignes. Pensez à ajuster ce paramètre s’il existe des lignes BLOB volumineuses qui doivent être extraites (ou lues).

- `innodb\_buffer\_pool\_size` : Effectuez un scale-up du serveur vers la SKU 32 vCores à mémoire optimisée à partir du niveau tarifaire du portail pendant la migration afin d’augmenter la valeur d’innodb\_buffer\_pool\_size. La valeur d’innodb\_buffer\_pool\_size peut être augmentée uniquement en effectuant un scale-up du calcul pour le serveur Azure Database pour MySQL. Consultez [Paramètres de serveur dans Azure Database pour MySQL](/azure/mysql/concepts-server-parameters#innodb_buffer_pool_size) pour connaître la valeur maximale du niveau. La valeur maximale dans un système 32 vCores à mémoire optimisée est `132070244352`.

- `innodb\_io\_capacity` & `innodb\_io\_capacity\_max` : Remplacez le paramètre par `9000` pour améliorer l’utilisation des E/S et optimiser la vitesse de migration.

- `max\_connections` : Si vous utilisez un outil qui génère plusieurs threads pour augmenter le débit, augmentez le nombre de connexions pour prendre en charge cet outil. La valeur par défaut est `151` et la valeur maximale `5000`.

    > [!NOTE]
    > Soyez vigilant lorsque vous effectuez une mise à l’échelle. Certaines opérations ne peuvent pas être annulées, comme la mise à l’échelle du stockage.
    
Ces paramètres peuvent être mis à jour à l’aide des cmdlets Azure PowerShell ci-dessous :

```
Install-Module -Name Az.MySql
$rgName = " {RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";

Select-AzSubscription -Subscription "{SUBSCRIPTION\_ID}"
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName
$rgna me -ServerName $serverName -Value 1073741824  
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName 
$rgna me -ServerName $serverName -Value 9000
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 9000

\#required if You've functions

Update-AzMySqlConfiguration -Name log\_bin\_trust\_function\_creators 
-ResourceGr oupName $rgname -ServerName $serverName -Value ON
```
## <a name="data"></a>Données

### <a name="tool-choice"></a>Choix de l’outil

Une fois que les objets de base de données et les utilisateurs du système source ont été migrés, la migration peut commencer. Les bases de données fonctionnant sur MySQL version 8.0 ne peuvent pas utiliser Azure DMS pour migrer la charge de travail. Les utilisateurs de la migration doivent plutôt utiliser MySQL Workbench.

### <a name="manual-import-and-export-steps"></a>Étapes d’importation et d’exportation manuelles

- Ouvrez MySQL Workbench et connectez-vous en tant qu’utilisateur racine de la base de données locale.

- Sous \*\*Gestion\*\*, sélectionnez \*\*Exportation de données\*\*. Sélectionnez le schéma **reg\_app**.

- Dans **Objets à exporter**, sélectionnez **Vider les procédures stockées et les fonctions**, **Vider les événements** et **Vider les déclencheurs**.

- Sous **Options d’exportation**, sélectionnez **Exporter vers un fichier autonome**.

- Cochez également la case **Inclure la création de schéma**. Reportez-vous à l’image ci-dessous pour observer la configuration correcte de mysqldump.

    ![Inclure la création de schéma](./media/image6.jpg)

    **Test**

- Si l’une de ces options n’est pas disponible, elle est probablement cachée par le volet Sortie. Il suffit de modifier la disposition de l’éditeur.

    ![Disposition de l’éditeur](./media/image7.jpg)

    **Test**

- Sélectionnez l’onglet **Progression de l’exportation**.

- Sélectionnez **Démarrer l’exportation**. Notez que MySQL Workbench envoie des appels à l’outil `mysqldump`.

- Ouvrez le script d’exportation nouvellement créé.

- Recherchez toutes les instructions `DEFINER` et modifiez-les pour un utilisateur valide ou supprimez-les entièrement.

> [!NOTE]
> Pour ce faire, vous pouvez transmettre `--skip-definer` dans la commande mysqldump. Cette option n’est pas disponible dans MySQL Workbench ; les lignes doivent donc être supprimées manuellement dans les commandes d’exportation. Bien que nous indiquions ici quatre éléments à supprimer, d’autres éléments peuvent échouer lors de la migration d’une version de MySQL à une autre (par exemple, les nouveaux mots réservés).

- Recherchez les instructions `SET GLOBAL` et modifiez-les pour un utilisateur valide ou supprimez-les entièrement.

- Assurez-vous que `sql\_mode` n’est pas défini sur `NO\_AUTO\_CREATE\_USER`.

- Supprimez la fonction `hello\_world`.

- Dans MySQL Workbench, créez une nouvelle connexion à Azure Database pour MySQL.

    - Pour « Nom d’hôte », entrez le DNS complet du serveur (par exemple : `servername.mysql.database.azure.com`).

    - Entrez le nom d’utilisateur (par exemple : `sqlroot@servername`).

    - Sélectionnez l’onglet **SSL**.

    - Pour le fichier de l’autorité de certification SSL, accédez au fichier de clé **BaltimoreCyberTrustRoot.crt.cer**.

    - Sélectionnez **Tester la connexion** et vérifiez que la connexion s’établit correctement.

    - Sélectionnez **OK**.

        ![Boîte de dialogue de connexion MySQL](./media/image8.jpg)

        **La boîte de dialogue de connexion MySQL s’affiche.**

- Sélectionnez **Fichier-\>Ouvrir un script SQL**.

- Accédez au fichier dump et sélectionnez **Ouvrir**.

- Sélectionnez **Exécuter**.

## <a name="update-applications-to-support-ssl"></a>Mettre à jour les applications pour prendre en charge SSL

- Basculez sur l’API du serveur Java dans Visual Studio Code.

- Ouvrez le fichier **launch.json**.

- Mettez à jour **DB\_CONNECTION\_URL** sur `jdbc:mysql://serverDNSname:3306/reg\_app?useUnicode=true\&useJDBCCompliantT imezoneShift=true\&useLegacyDatetimeCode=false\&serverTimezone=UTC\&verifySe rverCertificate=true\&useSSL=true\&requireSSL=true\&noAccessToProcedureBodie s=true.`. Prenez note des paramètres SSL supplémentaires.

- Mettez à jour **DB\_USER\_NAME** sur **conferenceuser@servername** .

- Démarrez la configuration de débogage et assurez-vous que l’application fonctionne localement avec la nouvelle base de données.

## <a name="revert-server-parameters"></a>Restaurer les paramètres du serveur

Les paramètres suivants peuvent être modifiés sur l’instance cible d’Azure Database pour MySQL. Ces paramètres peuvent être définis via le portail Azure ou en utilisant les [cmdlets Azure PowerShell pour MySQL](/azure/mysql/howto-configure-server-parameters-using-powershell).

```
$rgName = "YourRGName";
$serverName = "servername";
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName 
$rgna me -ServerName $serverName -Value 536870912
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName $rgna 
me -ServerName $serverName -Value 200
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 2000
```
## <a name="change-connection-string-for-the-java-api"></a>Modifier la chaîne de connexion pour l’API Java

- Utilisez les commandes suivantes pour modifier la chaîne de connexion pour l’API Java App Service.

```
$rgName = "YourRGName"; 
$app_name = "servername";
az webapp config appsettings set -g $rgName -n $app_name 
--settings DB_CONNECTION_URL={DB_CONNECTION_URL}
```

> [!NOTE]
> N’oubliez pas que vous pouvez utiliser le portail pour définir la chaîne de connexion.

- Redémarrez l’API App Service.

```
az webapp restart -g $rgName -n $app\_name
```
Vous avez terminé une migration d’un emplacement local vers Azure Database pour MySQL \!  


> [!div class="nextstepaction"]
> [Gestion post-migration](./10-post-migration-management.md)