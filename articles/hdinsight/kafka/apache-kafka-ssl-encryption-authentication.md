---
title: Configurer le chiffrement et l’authentification SSL pour Apache Kafka dans Azure HDInsight
description: Configurez le chiffrement SSL pour la communication entre clients Kafka et répartiteurs Kafka, ainsi qu’entre répartiteurs Kafka. Configurez l’authentification SSL des clients.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355101"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurer le chiffrement et l’authentification SSL pour Apache Kafka dans Azure HDInsight

Cet article décrit comment configurer le chiffrement SSL entre des clients Apache Kafka et des répartiteurs Apache Kafka, ainsi qu’entre répartiteurs Apache Kafka. Il fournit également les étapes nécessaires pour configurer l’authentification des clients (parfois appelée « SSL bidirectionnel »).

## <a name="server-setup"></a>Configuration du serveur

La première étape consiste à configurer le magasin de clés et le magasin d’approbations dans les répartiteurs Kafka, puis à importer l’autorité de certification et les certificats de répartiteur dans ces magasins.

> [!Note] 
> Ce guide utilise des certificats auto-signés. Toutefois, la solution la plus sécurisée consiste à utiliser des certificats émis par des autorités de certification.

1. Créez un dossier nommé SSL, puis exportez le mot de passe du serveur sous la forme d’une variable d’environnement. Dans la suite de ce guide, remplacez `<server_password>` par le vrai mot de passe administrateur du serveur.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Ensuite, créez un magasins de clés Java (kafka.server.keystore.jks) et un certificat d’autorité de certification.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Ensuite, créez une demande de signature pour obtenir le certificat créé à l’étape précédente, signé par l’autorité de certification.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Maintenant, envoyez la demande de signature à l’autorité de certification pour faire signer ce certificat. Étant donné que nous utilisons un certificat auto-signé, nous signons le certificat avec notre autorité de certification à l’aide de la commande `openssl`.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Créez un magasin d’approbations, puis importez le certificat de l’autorité de certification.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. Importez le certificat d’autorité de certification public dans le magasin de clés.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. Importez le certificat signé dans le magasin de clés.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

L’importation du certificat signé dans le magasin de clés correspond à la dernière étape de configuration du magasin d’approbations et du magasins de clés d’un répartiteur Kafka.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Mettre à jour la configuration pour utiliser le protocole SSL et redémarrer les répartiteurs

Nous avons configuré chaque répartiteur Kafka avec un magasin de clés et un magasin d’approbations, et avons importé les certificats nécessaires.  Nous allons maintenant modifier les propriétés de configuration Kafka associées à l’aide d’Ambari, puis redémarrer les répartiteurs Kafka.

1. Connectez-vous au portail Azure, puis sélectionnez votre cluster Apache Kafka Azure HDInsight.
1. Accédez à l’interface utilisateur d’Ambari en cliquant sur **Accueil Ambari** sous **Tableaux de bord du cluster**.
1. Sous **Kafka Broker** (Répartiteur Kafka), définissez la propriété **listeners** sur `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. Sous **Advanced kafka-broker** (Répartiteurs Kafka avancés), définissez la propriété **security.inter.broker.protocol** sur `SSL`

    ![Modification des propriétés de configuration SSL Kafka dans Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sous **Custom kafka-broker** (Répartiteur Kafka personnalisé), définissez la propriété **ssl.client.auth** sur `required`. Cette étape n’est nécessaire que si vous configurez à la fois l’authentification et le chiffrement.

    ![Modification des propriétés de configuration SSL Kafka dans Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Ajoutez des propriétés de configuration au fichier `server.properties` Kafka pour afficher les adresses IP plutôt que le nom de domaine complet (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Pour être sûr que les modifications précédentes ont bien été apportées, vous pouvez, si vous le souhaitez, vérifier que les lignes suivantes sont présentes dans le fichier `server.properties` Kafka.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Redémarrez tous les répartiteurs Kafka.

## <a name="client-setup-with-authentication"></a>Configuration du client (avec authentification)

> [!Note]
> Les étapes suivantes ne sont nécessaires que si vous installez à la fois le chiffrement **et** l’authentification SSL. Si vous configurez uniquement le chiffrement, passez à l’étape [Configuration du client sans authentification](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

1. Exportez le mot de passe du client. Remplacez `<client_password>` par le vrai mot de passe administrateur de la machine cliente Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Créez un magasin de clés Java afin d’obtenir un certificat auto-signé pour le répartiteur. Ensuite, copiez le certificat sur la machine virtuelle où l’autorité de certification est exécutée.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Basculez vers la machine d’autorité de certification (wn0) pour signer le certificat client.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Accédez à la machine cliente (hn1), puis au dossier `~/ssl`. Copiez le certificat auto-signé sur la machine cliente.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Affichez le fichier `client-ssl-auth.properties` avec la commande `$cat client-ssl-auth.properties`. Il doit contenir les lignes suivantes :

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Configuration du client (sans authentification)

1. Exportez le mot de passe du client. Remplacez `<client_password>` par le vrai mot de passe administrateur de la machine cliente Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Accédez à la machine cliente (hn1), puis au dossier `~/ssl`. Copiez le certificat auto-signé sur la machine cliente.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Affichez le fichier `client-ssl-auth.properties` avec la commande `$cat client-ssl-auth.properties`. Il doit contenir les lignes suivantes :

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Apache Kafka sur HDInsight](/../azure/hdinsight/kafka/apache-kafka-introduction)