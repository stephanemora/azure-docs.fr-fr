---
title: Connectivité chiffrée avec le protocole TLS/SSL dans Azure Database pour MySQL - Serveur flexible
description: Instructions et informations sur la connexion avec le protocole TLS/SSL dans Azure Database pour MySQL - Serveur flexible.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 399cf8087d39f78184cfdae4b9f0e34efecaea66
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491589"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Se connecter à Azure Database pour MySQL - Serveur flexible en utilisant des connexions chiffrées

> [!IMPORTANT]
> Le serveur flexible Azure Database pour MySQL est actuellement en préversion publique.

Le serveur flexible Azure Database pour MySQL prend en charge la connexion de vos applications clientes au serveur MySQL en utilisant le protocole SSL (Secure Sockets Layer) avec le protocole TLS (Transport Layer Security). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité.

Le serveur flexible Azure Database pour MySQL prend en charge par défaut les connexions chiffrées à l’aide du protocole TLS 1.2 (Transport Layer Security), donc toutes les connexions entrantes qui utilisent les protocoles TLS 1.0 et TLS 1.1 sont refusées par défaut. Vous pouvez modifier la configuration de la mise en œuvre de la connexion chiffrée ou de la version TLS sur votre serveur flexible, tel qu’abordé dans cet article. 

Voici les différentes configurations des paramètres SSL et TLS possibles pour votre serveur flexible :

| Scénario   | Paramètres du serveur      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|Désactiver SSL (connexions chiffrées) | require_secure_transport = OFF |Si votre application héritée ne prend pas en charge les connexions chiffrées à MySQL Server, vous pouvez désactiver l’application des connexions chiffrées à votre serveur flexible en définissant require_secure_transport = OFF.|
|Appliquer le protocole SSL avec une version TLS < 1.2 | require_secure_transport = ON et tls_version = TLSV1 ou TLSV1.1| Si votre application héritée prend en charge les connexions chiffrées mais requiert la version TLS < 1.2, vous pouvez activer les connexions chiffrées, mais configurer votre serveur flexible pour autoriser les connexions avec la version TLS (v1.0 ou v1.1) prises en charge par votre application|
|Appliquer le protocole SSL avec la version TLS version = 1.2 (configuration par défaut)|require_secure_transport = ON et tls_version = TLSV1.2| Il s’agit de la configuration par défaut recommandée pour le serveur flexible.|
|Appliquer le protocole SSL avec une version TLS = 1.3 (pris en charge avec MySQL v8.0 et versions ultérieures)| require_secure_transport = ON et tls_version = TLSV1.3| Cette fonctionnalité est utile et recommandée pour le développement de nouvelles applications|

> [!Note]
> Les modifications apportées au chiffrement SSL sur le serveur flexible ne sont pas prises en charge. Les suites de chiffrement FIPS sont appliquées par défaut quand tls_version est défini sur TLS version 1.2. Pour les versions TLS autres que la version 1.2, le chiffrement SSL est défini sur les paramètres par défaut qui sont fournis avec l’installation de MySQL Community.

Dans cet article, vous allez apprendre à :
* Configurer votre serveur flexible 
  * Avec SSL désactivé 
  * Avec SSL appliqué à la version TLS < 1.2
* Connectez-vous à votre serveur flexible à l’aide de la ligne de commande mysql 
  * Avec les connexions chiffrées désactivées
  * Avec les connexions chiffrées activées
* Vérifier l’état du chiffrement pour votre connexion
* Connectez-vous à votre serveur flexible avec des connexions chiffrées à l’aide de différentes infrastructures d’application

## <a name="disable-ssl-on-your-flexible-server"></a>Désactiver SSL sur votre serveur flexible
Si votre application cliente ne prend pas en charge les connexions chiffrées, vous devez désactiver la mise en œuvre des connexions chiffrées sur votre serveur flexible. Pour désactiver la mise en application des connexions chiffrées, vous devez définir require_secure_transport paramètre Server sur OFF comme indiqué dans la capture d’écran et enregistrer la configuration du paramètre de serveur pour qu’elle prenne effet. require_secure_transport est un **paramètre de serveur dynamique** qui prend effet immédiatement et ne nécessite pas le redémarrage du serveur pour prendre effet.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Capture d’écran montrant comment désactiver SSL avec Azure Database pour serveur flexible MySQL.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Se connecter à l’aide du client de ligne de commande mysql avec SSL désactivé

L’exemple suivant montre comment vous connecter à votre serveur à l’aide de l’interface de ligne de commande mysql. Utilisez le paramètre de chaîne de connexion `--ssl-mode=DISABLED` pour désactiver la connexion TLS/SSL à partir du client mysql. Remplacez les valeurs par le nom et le mot de passe réels de votre serveur. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Il est important de noter que le fait de définir require_secure_transport sur OFF ne signifie pas que les connexions chiffrées ne seront pas prises en charge côté serveur. Si vous définissez require_secure_transport sur OFF sur un serveur flexible, mais que le client se connecte avec une connexion chiffrée, il est toujours accepté. La connexion suivante utilisant le client mysql à un serveur flexible configuré avec require_secure_transport=OFF fonctionne également comme indiqué ci-dessous.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

En résumé, le paramètre require_secure_transport=OFF assouplit l’application des connexions chiffrées sur le serveur flexible et autorise les connexions non chiffrées au serveur à partir du client en plus des connexions chiffrées.

## <a name="enforce-ssl-with-tls-version--12"></a>Appliquer le protocole SSL avec une version TLS < 1.2

Si votre application prend en charge les connexions à MySQL Server avec SSL, mais prend en charge la version TLS < 1.2, vous devez définir le paramètre de serveur versions TLS sur votre serveur flexible. Pour définir les versions TLS que vous souhaitez que votre serveur flexible prenne en charge, vous devez définir le paramètre de serveur tls_version sur TLSV1, TLSV1.1 ou TLSV1 et TLSV1.1, comme indiqué dans la capture d’écran et enregistrer la configuration du paramètre de serveur pour qu’il prenne effet. tls_version est un **paramètre serveur statique** qui nécessite un redémarrage du serveur pour que le paramètre prenne effet.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Capture d’écran montrant comment définir la version TLS pour un serveur flexible Azure Database pour MySQL.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Se connecter à l’aide du client de ligne de commande mysql avec le protocole TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Télécharger le certificat SSL public
Pour utiliser des connexions chiffrées avec vos applications clientes, vous devez télécharger le [certificat SSL public](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) qui est également disponible dans le panneau du réseau du portail Azure, comme indiqué dans la capture d’écran ci-dessous.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Capture d’écran montrant comment télécharger un certificat SSL public à partir du Portail Azure.":::

Téléchargez le fichier de certificat dans votre l’emplacement favori. Par exemple, ce didacticiel utilise `c:\ssl` ou `\var\www\html\bin` sur votre environnement local ou l’environnement client où votre application est hébergée. Cela permet aux applications de se connecter en toute sécurité à la base de données via SSL.

Si vous avez créé votre serveur flexible avec l’option *Accès privé (intégration au réseau virtuel)* , vous avez besoin de vous connecter à votre serveur à partir d’une ressource qui se trouve au sein du même réseau virtuel que votre serveur. Vous pouvez créer une machine virtuelle et l’ajouter au réseau virtuel créé avec votre serveur flexible.

Si vous avez créé votre serveur flexible avec l’option *Accès public (adresses IP autorisées)* , vous pouvez ajouter votre adresse IP locale à la liste des règles de pare-feu sur votre serveur.

Vous pouvez choisir [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) pour vous connecter au serveur à partir de votre environnement local. 

L’exemple suivant montre comment vous connecter à votre serveur à l’aide de l’interface de ligne de commande mysql. Utilisez le paramètre de chaîne de connexion `--ssl-mode=REQUIRED` pour appliquer la vérification du certificat TLS/SSL. Passez le chemin d’accès du fichier de certificat local au paramètre `--ssl-ca`. Remplacez les valeurs par le nom et le mot de passe réels de votre serveur. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Vérifiez que la valeur passée à `--ssl-ca` correspond au chemin de fichier du certificat que vous avez enregistré.

Si vous essayez de vous connecter à votre serveur avec des connexions non chiffrées, vous verrez une erreur indiquant que les connexions utilisant le transport non sécurisé sont interdites comme suit :

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Vérifier la connexion TLS/SSL

Exécutez la commande mysql **status** pour vérifier que vous êtes connecté à votre serveur MySQL à l’aide du protocole TLS/SSL :

```dos
mysql> status
```
Vérifiez que la connexion est chiffrée en examinant la sortie, qui doit indiquer : **SSL: Cipher in use is **. Cette suite de chiffrement est un exemple basé sur le client. Celle que vous voyez peut être différente.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Connectez-vous à votre serveur flexible avec des connexions chiffrées à l’aide de différentes infrastructures d’application

Les chaînes de connexion prédéfinies dans la page « Chaînes de connexion » disponible pour votre serveur dans le portail Azure incluent les paramètres nécessaires pour les langages courants afin de vous connecter au serveur de base de données à l’aide du protocole TLS/SSL. Le paramètre TLS/SSL varie en fonction du connecteur. Exemples : "useSSL=true", "sslmode=required" ou "ssl_verify_cert=true" et d’autres variantes.

Pour établir une connexion chiffrée à votre serveur flexible par le biais du protocole TLS/SSL à partir de votre application, reportez-vous aux exemples de code suivants :

### <a name="wordpress"></a>WordPress
Téléchargez le [certificat public SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) et ajoutez les lignes suivantes dans le fichier wp-config.php après la ligne ```// ** MySQL settings - You can get this info from your web host ** //```.

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (avec PDO)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (connecteur MySQL pour Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (connecteur MariaDB pour Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Étapes suivantes
- [Utilisation de MySQL Workbench pour vous connecter et interroger des données sur le serveur flexible Azure Database pour MySQL](./connect-workbench.md)
- [Utilisation de PHP pour vous connecter et interroger des données sur le serveur flexible Azure Database pour MySQL](./connect-php.md)
- [Création et gestion d’un réseau virtuel de serveur flexible Azure Database pour MySQL à l’aide d’Azure CLI](./how-to-manage-virtual-network-cli.md)
- En savoir plus sur la [mise en réseau sur le serveur flexible Azure Database pour MySQL](./concepts-networking.md)
- En savoir plus sur les [règles de pare-feu du serveur flexible Azure Database pour MySQL](./concepts-networking.md#public-access-allowed-ip-addresses)
