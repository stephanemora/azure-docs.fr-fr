---
title: Connectivité chiffrée avec le protocole TLS/SSL dans Azure Database pour MySQL - Serveur flexible
description: Instructions et informations sur la connexion avec le protocole TLS/SSL dans Azure Database pour MySQL - Serveur flexible.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: ce6150cf404f1ca68c93285a2f4a29a6373a55c0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110022"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-over-tls12ssl"></a>Se connecter à Azure Database pour MySQL – Serveur flexible sur TLS1.2/SSL

> [!IMPORTANT]
> Le serveur flexible Azure Database pour MySQL est actuellement en préversion publique.

Le serveur flexible Azure Database pour MySQL prend en charge la connexion de vos applications clientes au service MySQL en utilisant le protocole TLS (Transport Layer Security), anciennement SSL (Secure Sockets Layer). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité.

Le serveur flexible Azure Database pour MySQL prend uniquement en charge les connexions chiffrées à l’aide du protocole TLS (Transport Layer Security) 1.2, donc toutes les connexions entrantes qui utilisent les protocoles TLS 1.0 et TLS 1.1 sont refusées. Pour tous les serveurs flexibles, la mise en œuvre des connexions TLS est activée et il est impossible de désactiver le protocole TLS/SSL pour la connexion à un serveur flexible.

## <a name="download-the-public-ssl-certificate"></a>Télécharger le certificat SSL public
Pour utiliser avec vos applications, téléchargez le [certificat SSL public](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem).

Téléchargez le fichier de certificat dans votre l’emplacement favori. Par exemple, ce didacticiel utilise `c:\ssl` ou `\var\www\html\bin` sur votre environnement local ou l’environnement client où votre application est hébergée. Cela permet aux applications de se connecter en toute sécurité à la base de données via SSL. 

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Se connecter à l’aide du client de ligne de commande mysql avec le protocole TLS/SSL

Si vous avez créé votre serveur flexible avec l’option *Accès privé (intégration au réseau virtuel)* , vous avez besoin de vous connecter à votre serveur à partir d’une ressource qui se trouve au sein du même réseau virtuel que votre serveur. Vous pouvez créer une machine virtuelle et l’ajouter au réseau virtuel créé avec votre serveur flexible.

Si vous avez créé votre serveur flexible avec l’option *Accès public (adresses IP autorisées)* , vous pouvez ajouter votre adresse IP locale à la liste des règles de pare-feu sur votre serveur.

Vous pouvez choisir [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) pour vous connecter au serveur à partir de votre environnement local. 

L’exemple suivant montre comment vous connecter à votre serveur à l’aide de l’interface de ligne de commande mysql. Utilisez le paramètre de chaîne de connexion `--ssl-mode=REQUIRED` pour appliquer la vérification du certificat TLS/SSL. Passez le chemin d’accès du fichier de certificat local au paramètre `--ssl-ca`. Remplacez les valeurs par le nom et le mot de passe réels de votre serveur. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Vérifiez que la valeur passée à `--ssl-ca` correspond au chemin de fichier du certificat que vous avez enregistré.

### <a name="verify-the-tlsssl-connection"></a>Vérifier la connexion TLS/SSL

Exécutez la commande mysql **status** pour vérifier que vous êtes connecté à votre serveur MySQL à l’aide du protocole TLS/SSL :

```dos
mysql> status
```
Confirmez le chiffrement de la connexion en vérifiant la sortie, qui doit indiquer :  **SSL: Cipher in use is AES256-SHA** (SSL  : Le chiffrement utilisé est AES256-SHA). Cette suite de chiffrement est un exemple basé sur le client. Celle que vous voyez peut être différente.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions TLS

Certains frameworks d’applications courants qui utilisent MySQL pour leurs services de base de données n’activent pas le protocole TLS par défaut pendant l’installation. Votre serveur MySQL applique des connexions TLS, mais si l’application n’est pas configurée pour cela, cette dernière risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions TLS.

## <a name="sample-code"></a>Exemple de code
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
