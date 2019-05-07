---
title: Configurer le chiffrement SSL et l’authentification pour Apache Kafka dans Azure HDInsight
description: Configurer le chiffrement SSL pour la communication entre les clients Kafka et des répartiteurs Kafka, ainsi qu’entre des répartiteurs Kafka. Configurer l’authentification SSL de clients.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: e526908f5ba9feea53b1c1abebbbfc1bd9a51c54
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147967"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurer le chiffrement Secure Sockets Layer (SSL) et l’authentification pour Apache Kafka dans Azure HDInsight

Cet article vous montre comment configurer le chiffrement SSL entre les clients Apache Kafka et des courtiers d’Apache Kafka. Il montre également comment configurer l’authentification des clients (parfois appelée SSL bidirectionnel).

> [!Important]
> Il existe deux clients que vous pouvez utiliser pour les applications de Kafka : un client Java et un client de la console. Seul le client Java `ProducerConsumer.java` peuvent utiliser SSL pour la production et la consommation. Le client de producteur de console `console-producer.sh` ne fonctionne pas avec SSL.

## <a name="apache-kafka-broker-setup"></a>Programme d’installation Apache Kafka Broker

Le programme d’installation du broker Kafka SSL utilisera quatre machines virtuelles du cluster HDInsight de la façon suivante :

* nœud principal 0 - autorité de certification (CA)
* répartiteurs de nœud Worker 0, 1 et 2 :

> [!Note] 
> Ce guide utilise des certificats auto-signés. Toutefois, la solution la plus sécurisée consiste à utiliser des certificats émis par des autorités de certification.

Le résumé du processus de configuration de service broker est comme suit :

1. Les étapes suivantes sont répétés sur chacun des nœuds de trois travail :

    1. Générer un certificat.
    1. Créer un demande de signature de certificat.
    1. Envoyer la demande à l’autorité de certification (CA) de signature de certificat.
    1. Connectez-vous à l’autorité de certification et signer la demande.
    1. SCP du certificat signé au nœud de travail.
    1. SCP le certificat public de l’autorité de certification au nœud de travail.

1. Une fois que tous les certificats, placés les certificats dans le magasin de certificats.
1. Accédez à Ambari et modifier les configurations.

Utilisez les instructions détaillées ci-dessous pour terminer la configuration de service broker :

> [!Important]
> Dans les extraits de code suivants wnX est une abréviation pour l’un des nœuds de trois travail et doit être remplacé par `wn0`, `wn1` ou `wn2` selon le cas. `WorkerNode0_Name` et `HeadNode0_Name` doit être remplacée par les noms des ordinateurs respectifs, tels que `wn0-abcxyz` ou `hn0-abcxyz`.

1. Effectuer la configuration initiale sur le nœud principal 0, qui remplira le rôle de l’autorité de certification (AC) pour HDInsight.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export SRVPASS=MyServerPassword123
    ```

1. Effectuer la même configuration initiale sur chacun des répartiteurs (nœuds worker 0, 1 et 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export MyServerPassword123=MyServerPassword123
    ```

1. Sur chacun des nœuds de travail, exécutez les étapes suivantes à l’aide de l’extrait de code ci-dessous.
    1. Créer un magasin de clés et le remplir avec un nouveau certificat privé.
    1. Créer une demande de signature de certificat.
    1. SCP la demande de signature de certificat à l’autorité de certification (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Passez à l’ordinateur d’autorité de certification et signer tous le certificat reçu, demandes de signature :

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envoyer les certificats auto-signés sur les nœuds de travail à partir de l’autorité de certification (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envoyez le certificat public de l’autorité de certification à chaque nœud de travail.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Sur chaque nœud de travail, ajoutez le certificat public d’autorités de certification au magasin de clés et truststore. Puis ajoutez le certificat signé du nœud de travail vers le magasin de clés

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Mettre à jour la configuration Kafka pour utiliser le protocole SSL et redémarrer les répartiteurs

Vous disposez maintenant configurer chaque répartiteur Kafka avec un magasin de clés et le truststore et importé les certificats corrects. Nous allons maintenant modifier les propriétés de configuration Kafka associées à l’aide d’Ambari, puis redémarrer les répartiteurs Kafka.

Pour terminer la modification de configuration, procédez comme suit :

1. Connectez-vous au portail Azure, puis sélectionnez votre cluster Apache Kafka Azure HDInsight.
1. Accédez à l’interface utilisateur d’Ambari en cliquant sur **Accueil Ambari** sous **Tableaux de bord du cluster**.
1. Sous **Kafka Broker** (Répartiteur Kafka), définissez la propriété **listeners** sur `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. Sous **Advanced kafka-broker** (Répartiteurs Kafka avancés), définissez la propriété **security.inter.broker.protocol** sur `SSL`

    ![Modification des propriétés de configuration SSL Kafka dans Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sous **Custom kafka-broker** (Répartiteur Kafka personnalisé), définissez la propriété **ssl.client.auth** sur `required`. Cette étape est uniquement requise si vous configurez l’authentification et le chiffrement.

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

Procédez comme suit pour terminer l’installation du client :

1. Connectez-vous à l’ordinateur client (hn1).
1. Exportez le mot de passe du client. Remplacez `<client_password>` par le vrai mot de passe administrateur de la machine cliente Kafka.
1. Créez un magasin de clés Java afin d’obtenir un certificat auto-signé pour le répartiteur. Ensuite, copiez le certificat sur la machine virtuelle où l’autorité de certification est exécutée.
1. Basculez vers l’ordinateur d’autorité de certification (hn0) pour signer le certificat client.
1. Accédez à la machine cliente (hn1), puis au dossier `~/ssl`. Copiez le certificat auto-signé sur la machine cliente.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Pour finir, affichez le fichier `client-ssl-auth.properties` avec la commande `cat client-ssl-auth.properties`. Il doit contenir les lignes suivantes :

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Configuration du client (sans authentification)

Si vous n’avez pas besoin d’authentification, les étapes pour configurer uniquement le chiffrement SSL sont :

1. Connectez-vous à la machine cliente (hn1) et accédez au dossier `~/ssl`
1. Exportez le mot de passe du client. Remplacez `<client_password>` par le vrai mot de passe administrateur de la machine cliente Kafka.
1. Copiez le certificat auto-signé de la machine d’autorité de certification (wn0) sur la machine cliente.
1. Importer le certificat d’autorité de certification dans le magasin d’approbations
1. Importer le certificat d’autorité de certification dans le magasin de clés

Les étapes sont présentées dans l’extrait de code suivant.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Pour finir, affichez le fichier `client-ssl-auth.properties` avec la commande `cat client-ssl-auth.properties`. Il doit contenir les lignes suivantes :

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Apache Kafka sur HDInsight](apache-kafka-introduction.md)
