---
title: Chiffrement et authentification SSL Apache Kafka – Azure HDInsight
description: Configurez le chiffrement SSL pour la communication entre des clients Kafka et des répartiteurs Kafka, ainsi qu’entre des répartiteurs Kafka. Configurez l’authentification SSL des clients.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 180b7c203755553c343e0f7fc65c93092b330124
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751312"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurer le chiffrement et l’authentification SSL pour Apache Kafka dans Azure HDInsight

Cet article explique comment configurer le chiffrement SSL entre des clients Apache Kafka et des répartiteurs Apache Kafka. Il fournit également les étapes nécessaires pour configurer l’authentification des clients (parfois appelée « SSL bidirectionnel »).

> [!Important]
> Vous pouvez utiliser deux clients pour les applications Kafka : un client Java et un client de console. Seul le client Java `ProducerConsumer.java` peut utiliser SSL à la fois pour la production et la consommation. Le client du producteur de console `console-producer.sh` ne fonctionne pas avec SSL.

## <a name="apache-kafka-broker-setup"></a>Configuration du répartiteur Apache Kafka

Lors de sa configuration, le répartiteur Kafka SSL utilise quatre machines virtuelles de cluster HDInsight, de la manière suivante :

* Nœud principal 0 - Autorité de certification (CA)
* Nœuds Worker 0, 1, et 2 - Répartiteurs

> [!Note] 
> Ce guide utilise des certificats auto-signés. Toutefois, la solution la plus sécurisée consiste à utiliser des certificats émis par des autorités de certification.

Le récapitulatif du processus de configuration du répartiteur est le suivant :

1. Les étapes suivantes sont répétées sur chacun des trois nœuds Worker :

    1. Génération d’un certificat.
    1. Création d’une requête de signature de certificat.
    1. Envoi de cette requête à l’autorité de certification (CA).
    1. Connexion à l’autorité de certification et signature de la requête.
    1. Utilisez SCP pour renvoyer le certificat au nœud Worker.
    1. Utilisez SCP pour renvoyer le certificat de l’autorité de certification au nœud Worker.

1. Une fois que vous disposez de l’ensemble des certificats, vous pouvez les placer dans le magasin de certificats.
1. Accédez à Ambari et modifiez les configurations.

Utilisez les instructions détaillées ci-dessous pour terminer la configuration du répartiteur :

> [!Important]
> Dans les extraits de code suivants, « wnX » est une abréviation de l’un des trois nœuds Worker, qui doit être remplacée par `wn0`, `wn1` ou `wn2` selon le cas. `WorkerNode0_Name` et `HeadNode0_Name` doivent être remplacés par les noms des machines respectives.

1. Effectuez la configuration initiale sur le nœud principal 0, qui remplira le rôle d’autorité de certification pour HDInsight.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Effectuez la même configuration initiale sur chacun des répartiteurs (nœuds Worker 0, 1 et 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Sur chacun des nœuds Worker, exécutez les étapes suivantes à l’aide de l’extrait de code ci-dessous.
    1. Créez un magasin de clés et remplissez-le avec un nouveau certificat privé.
    1. Créez une requête de signature de certificat.
    1. Utilisez SCP pour envoyer la requête de signature de certificat à l’autorité de certification (le nœud principal 0).

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Sur l’ordinateur de l’autorité de certification, exécutez la commande suivante pour créer des fichiers ca-cert et ca-key :

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Passez à l’ordinateur associé à l’autorité de certification et signez toutes les requêtes de signature de certificat reçues :

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envoyez les certificats signés sur les nœuds Worker à partir de l’autorité de certification (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envoyez le certificat public à l’autorité de certification de chaque nœud Worker.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Sur chacun de ces nœuds, ajoutez le certificat public de l’autorité de certificat dans le magasin d’approbations et le magasin de clés. Ensuite, ajoutez le certificat signé du nœud Worker dans le magasin de clés.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Mettre à jour la configuration Kafka pour utiliser le protocole SSL et redémarrer les répartiteurs

Vous avez désormais configuré chaque répartiteur Kafka avec un magasin de clés et un magasin d’approbations, et importé les certificats nécessaires. Nous allons maintenant modifier les propriétés de configuration Kafka associées à l’aide d’Ambari, puis redémarrer les répartiteurs Kafka.

Pour terminer la modification de configuration, procédez comme suit :

1. Connectez-vous au portail Azure, puis sélectionnez votre cluster Apache Kafka Azure HDInsight.
1. Accédez à l’interface utilisateur d’Ambari en cliquant sur **Accueil Ambari** sous **Tableaux de bord du cluster**.
1. Sous **Kafka Broker** (Répartiteur Kafka), définissez la propriété **listeners** sur `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. Sous **Advanced kafka-broker** (Répartiteurs Kafka avancés), définissez la propriété **security.inter.broker.protocol** sur `SSL`

    ![Modification des propriétés de configuration SSL Kafka dans Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sous **Custom kafka-broker** (Répartiteur Kafka personnalisé), définissez la propriété **ssl.client.auth** sur `required`. Cette étape n’est nécessaire que si vous configurez l’authentification et le chiffrement.

    ![Modification des propriétés de configuration SSL Kafka dans Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Sous **Advanced kafka-env**, ajoutez les lignes suivantes à la fin de la propriété **kafka-env template**.

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Modification de la propriété kafka-env template dans Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Redémarrez tous les répartiteurs Kafka.
1. Démarrez le client d’administration avec les options de producteur et consommateur, afin de vérifier que les producteurs et consommateurs fonctionnent sur le port 9093.

## <a name="client-setup-with-authentication"></a>Configuration du client (avec authentification)

> [!Note]
> Les étapes suivantes ne sont nécessaires que si vous installez à la fois le chiffrement **et** l’authentification SSL. Si vous configurez uniquement le chiffrement, passez à l’étape [Configuration du client sans authentification](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Procédez comme suit pour effectuer la configuration du client :

1. Connectez-vous à la machine cliente (nœud principal en attente).
1. Créez un magasin de clés Java afin d’obtenir un certificat auto-signé pour le répartiteur. Ensuite, copiez le certificat sur la machine virtuelle où l’autorité de certification est exécutée.
1. Basculez vers la machine de l’autorité de certification (nœud principal actif) pour signer le certificat client.
1. Accédez à la machine cliente (nœud principal en attente), puis au dossier `~/ssl`. Copiez le certificat auto-signé sur la machine cliente.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (active head node) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (standby head node), navigate to ~/ssl folder and copy signed cert from the CA (active head node) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Pour finir, affichez le fichier `client-ssl-auth.properties` avec la commande `cat client-ssl-auth.properties`. Il doit contenir les lignes suivantes :

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Configuration du client (sans authentification)

Si vous n’avez pas besoin d’authentification, les étapes de configuration du chiffrement SSL uniquement sont les suivantes :

1. Connectez-vous à la machine cliente (hn1) et accédez au dossier `~/ssl`
1. Copiez le certificat auto-signé de la machine d’autorité de certification (wn0) sur la machine cliente.
1. Importer le certificat d’autorité de certification dans le magasin d’approbations
1. Importer le certificat d’autorité de certification dans le magasin de clés

Les étapes sont présentées dans l’extrait de code suivant.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Pour finir, affichez le fichier `client-ssl-auth.properties` avec la commande `cat client-ssl-auth.properties`. Il doit contenir les lignes suivantes :

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Apache Kafka sur HDInsight](apache-kafka-introduction.md)
