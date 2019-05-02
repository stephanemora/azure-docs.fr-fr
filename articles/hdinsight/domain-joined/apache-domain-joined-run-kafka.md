---
title: Configurer des stratégies Apache Kafka dans HDInsight avec le Pack Sécurité Entreprise - Azure
description: Découvrez comment configurer des stratégies Apache Ranger pour Kafka dans Azure HDInsight avec le Pack Sécurité Entreprise (ESP, Enterprise Security Package).
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 6434f7cae3c3fa402efad00b2f6bfb0bc405f9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730252"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutoriel : Configurer des stratégies Apache Kafka dans HDInsight avec le Pack Sécurité Entreprise (préversion)

Découvrez comment configurer des stratégies Apache Ranger pour des clusters Apache Kafka avec le Pack Sécurité Entreprise. Les clusters ESP sont connectés à un domaine permettant aux utilisateurs de s’authentifier avec les informations d’identification du domaine. Dans ce tutoriel, vous créez deux stratégies Ranger pour restreindre l’accès aux rubriques `sales*` et `marketingspend`.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des utilisateurs du domaine
> * Création de stratégies Ranger
> * Créer des rubriques dans un cluster Kafka
> * Tester des stratégies Ranger

## <a name="before-you-begin"></a>Avant de commencer

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Connectez-vous au [Portail Azure](https://portal.azure.com/).

* Créez un [cluster Kafka HDInsight avec le Pack Sécurité Entreprise](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Connexion à l’interface utilisateur Apache Ranger

1. À partir d’un navigateur, connectez-vous à l’interface d’utilisateur administrateur de Ranger avec l’URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. N’oubliez pas de remplacer `<ClusterName>` par le nom de votre cluster Kafka.

    > [!NOTE]  
    > Les informations d’identification de Ranger ne sont pas les mêmes que celles du cluster Hadoop. Pour empêcher les navigateurs d’utiliser les informations d’identification Hadoop mises en cache, utilisez une nouvelle fenêtre de navigation InPrivate pour vous connecter à l’interface utilisateur administrateur de Ranger.

2. Connectez-vous avec vos informations d’identification d’administrateur Azure Active Directory. Les informations d’identification administrateur Azure AD ne sont pas les mêmes que celles du cluster HDInsight ou que celles du protocole SSH du nœud HDInsight Linux.

   ![Interface utilisateur de l’administrateur Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Créer des utilisateurs du domaine

Consultez [Créer un cluster HDInsight avec le Pack Sécurité Entreprise](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) pour découvrir comment créer les utilisateurs du domaine **sales_user** et **marketing_user**. Dans un scénario de production, les utilisateurs du domaine proviennent de votre locataire Active Directory.

## <a name="create-ranger-policy"></a>Créer une stratégie Ranger

Créez une stratégie Ranger pour **sales_user** et **marketing_user**.

1. Ouvrez **l’interface utilisateur de l’administrateur Ranger**.

2. Cliquez sur **\<nom_cluster>_kafka** sous **Kafka**. Il est possible qu’une stratégie préconfigurée figure dans la liste.

3. Cliquez sur **Ajouter une nouvelle stratégie**, puis entrez les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée**  |
   |---------|---------|
   |Nom de la stratégie  |  hdi sales* policy   |
   |Rubrique   |  sales* |
   |Sélectionner un utilisateur  |  sales_user1 |
   |Autorisations  | publier, consommer, créer |

   Les caractères génériques suivants peuvent être inclus dans le nom de la rubrique :

   * « * » indique zéro, une ou plusieurs occurrences des caractères.
   * « ? » indique n’importe quel caractère individuel.

   ![Interface utilisateur de l’administrateur Apache Ranger - Créer une stratégie](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Attendez quelques instants pour que Ranger se synchronise avec Azure AD si un utilisateur du domaine n’est pas renseigné automatiquement dans **Sélectionner un utilisateur**.

4. Cliquez sur **Ajouter** pour enregistrer la stratégie.

5. Cliquez sur **Ajouter une nouvelle stratégie**, puis entrez les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée**  |
   |---------|---------|
   |Nom de la stratégie  |  hdi marketing policy   |
   |Rubrique   |  marketingspend |
   |Sélectionner un utilisateur  |  marketing_user1 |
   |Autorisations  | publier, consommer, créer |

   ![Interface utilisateur de l’administrateur Apache Ranger - Créer une stratégie](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Cliquez sur **Ajouter** pour enregistrer la stratégie.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Créer des rubriques dans un cluster Kafka avec ESP

Pour créer les deux rubriques, `salesevents` et `marketingspend` :

1. Utilisez la commande suivante pour ouvrir une connexion SSH avec le cluster :

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Remplacez `DOMAINADMIN` par l’utilisateur administrateur de votre cluster qui a été configuré pendant la [création du cluster](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp), puis remplacez `CLUSTERNAME` par le nom de votre cluster. Si vous y êtes invité, entrez le mot de passe du compte de l’utilisateur administrateur. Pour en savoir plus sur l’utilisation de `SSH` avec HDInsight, voir [Utiliser SSH avec Hadoop - Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

2. Utilisez les commandes suivantes pour enregistrer le nom du cluster dans une variable et pour installer un utilitaire d’analyse JSON `jq`. Quand vous y êtes invité, entrez le nom du cluster Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Utilisez les commandes suivantes pour obtenir les hôtes du répartiteur Kafka. Quand vous y êtes invité, entrez le mot de passe du compte de l’administrateur du cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Avant de continuer, il vous faut peut-être configurer votre environnement de développement, si vous ne l’avez pas déjà fait. Vous aurez besoin de composants comme JDK Java, Apache Maven et d’un client SSH avec scp. Pour plus de détails, consultez les [instructions de configuration](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Téléchargez les [exemples de producteur et consommateur joints au domaine Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Suivez les étapes 2 et 3 de la section **Générer et déployer l’exemple** dans [Didacticiel : Utiliser les API de producteur et de consommateur Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Exécutez les commandes suivantes :

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Tester les stratégies Ranger

En fonction des stratégies Ranger configurées, **sales_user** peut produire/consommer la rubrique `salesevents`, mais pas la rubrique `marketingspend`. À l’inverse, **marketing_user** peut produire/consommer la rubrique `marketingspend`, mais pas la rubrique `salesevents`.

1. Ouvrez une connexion SSH avec le cluster. Utilisez la commande suivante pour vous connecter en tant que **sales_user1** :

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Exécutez la commande suivante :

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Utiliser les noms du répartiteur de la section précédente pour définir les variables d’environnement suivantes :

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exemple : `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Suivez l’étape 3 de la section **Générer et déployer l’exemple** dans [Tutoriel : Utiliser les API de producteur et de consommateur Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) pour vérifier que `kafka-producer-consumer.jar` est également accessible à **sales_user**.

5. Vérifiez que **sales_user1** peut produire à destination de la rubrique `salesevents` en exécutant la commande suivante :

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Exécutez la commande suivante pour consommer ce qui provient de la rubrique `salesevents` :

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Vérifiez que vous pouvez lire les messages.

7. Vérifiez que **sales_user1** ne peut pas produire à destination de la rubrique `marketingspend` en exécutant ce qui suit dans la même fenêtre ssh :

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Une erreur d’autorisation se produit et elle peut être ignorée.

8. Notez que **marketing_user1** ne peut pas consommer ce qui provient de la rubrique `salesevents`.

   Répétez les étapes 1 à 4 ci-dessus, mais cette fois en tant que **marketing_user1**.

   Exécutez la commande suivante pour consommer ce qui provient de la rubrique `salesevents` :

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Les messages précédents ne sont pas visibles.

9. Consultez les événements d’accès de l’audit à partir de l’interface utilisateur de Ranger.

   ![Audit de stratégie de l’interface utilisateur de Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Étapes suivantes

* [Apporter votre propre clé pour Apache Kafka sur Azure HDInsight (préversion)](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Introduction à la sécurité Apache Hadoop avec le Pack Sécurité Entreprise](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
