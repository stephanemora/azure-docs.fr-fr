---
title: Exécuter l’émulateur Azure Cosmos DB sur Docker pour Linux
description: Découvrez comment exécuter et utiliser l’émulateur Azure Cosmos DB pour Linux sur Linux et macOS. L’émulateur vous permet de développer et de tester votre application localement, sans abonnement Azure et sans frais.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 06/04/2021
ms.openlocfilehash: 6e3fd0c2dafd9d174b79206cb5482450fee74f8e
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984045"
---
# <a name="run-the-emulator-on-docker-for-linux-preview"></a>Exécuter l’émulateur sur Docker pour Linux (Préversion)

L’émulateur Azure Cosmos DB pour Linux fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement. Actuellement, l’émulateur Linux prend uniquement en charge l’API SQL. L’émulateur Azure Cosmos DB vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur Azure Cosmos DB pour Linux, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud. Cet article décrit l’installation et l’utilisation de l’émulateur sur les environnements macOS et Linux.

> [!NOTE]
> L’émulateur Cosmos DB Linux est actuellement en préversion et prend en charge uniquement l’API SQL. Les utilisateurs peuvent noter une légère dégradation des performances en termes de nombre de requêtes par seconde traitées par l’émulateur par rapport à la version Windows. Le nombre de partitions physiques par défaut qui ont un impact direct sur le nombre de conteneurs pouvant être approvisionnés est de 10.
> 
> Nous vous déconseillons d’utiliser l’émulateur (préversion) en production. Pour les charges de travail plus lourdes, utilisez notre [émulateur Windows](local-emulator.md).

## <a name="how-does-the-emulator-work"></a>Comment l’émulateur fonctionne-t-il ?

L’émulateur Azure Cosmos DB pour Linux fournit une émulation haute fidélité du service Azure Cosmos DB. Il prend en charge les mêmes fonctionnalités qu’Azure Cosmos DB, notamment la prise en charge de la création et de l’interrogation des données, la configuration et la mise à l’échelle des conteneurs, et l’exécution des procédures stockées et des déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur Azure Cosmos DB pour Linux, et les déployer sur Azure à l’échelle mondiale en mettant à jour le point de terminaison de connexion Azure Cosmos DB.

Les fonctionnalités qui s’appuient sur l’infrastructure Azure, comme la réplication globale, la latence en quelques millisecondes pour les lectures/écritures et les niveaux de cohérence ajustables ne s’appliquent pas lorsque vous utilisez l’émulateur.

## <a name="differences-between-the-linux-emulator-and-the-cloud-service"></a>Différences entre l’émulateur pour Linux et le service cloud
Dans la mesure où l’émulateur Azure Cosmos DB offre un environnement émulé qui s’exécute sur la station de travail de développement locale, il existe quelques différences entre les fonctionnalités de l’émulateur et celles d’un compte Azure Cosmos dans le cloud :

- Actuellement, le volet **Explorateur de données** dans l’émulateur prend entièrement en charge les clients d’API SQL uniquement.

- Avec l’émulateur pour Linux, vous pouvez créer un compte Azure Cosmos en mode [débit provisionné](set-throughput.md) uniquement ; actuellement, il ne prend pas en charge le mode [sans serveur](serverless.md).

- L’émulateur pour Linux n’est pas un service de stockage scalable et ne prend pas en charge un grand nombre de conteneurs. Par défaut, vous pouvez créer jusqu’à 10 conteneurs de taille fixe à 400 RU/s (pris en charge uniquement avec les kits SDK Azure Cosmos DB), ou 5 conteneurs illimités avec l’émulateur Azure Cosmos DB. Pour plus d’informations sur la modification de cette valeur, consultez l’article [Définir la valeur PartitionCount](emulator-command-line-parameters.md#set-partitioncount).

- Si des [niveaux de cohérence](consistency-levels.md) semblables à ceux du service cloud peuvent être ajustés à l’aide d’arguments de ligne de commande pour les scénarios de test uniquement (le paramètre par défaut est Session), un utilisateur peut ne pas attendre le même comportement que dans le service cloud. Par exemple, les cohérences Forte et Obsolescence limitée n’ont aucun effet sur l’émulateur, à l’exception de la signalisation au SDK Cosmos DB de la cohérence par défaut du compte.

- L’émulateur pour Linux ne permet pas la [réplication entre plusieurs régions](distribute-data-globally.md).

- Votre copie de l’émulateur Azure Cosmos DB pour Linux n’étant pas forcément à jour par rapport aux derniers changements apportés au service Azure Cosmos DB, nous vous conseillons de consulter l’outil de [planification de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md) pour évaluer avec précision les besoins en débit (RU) de votre application. <add link>

- L’émulateur pour Linux prend en charge la taille maximale de propriété d’ ID qui est de 254 caractères.

## <a name="run-the-linux-emulator-on-macos"></a><a id="run-on-macos"></a>Exécuter l’émulateur Linux sur macOS

> [!NOTE]
> L’émulateur prend uniquement en charge les MacBooks avec des processeurs Intel.

Pour commencer, accédez à Docker Hub et installez le [Docker Desktop pour macOS](https://hub.docker.com/editions/community/docker-ce-desktop-mac/). Effectuez les étapes suivantes pour exécuter l’émulateur sur macOS :

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

## <a name="install-the-certificate"></a><a id="install-certificate"></a>Installer le certificat

1. Une fois l’émulateur en cours d’exécution, à l’aide d’un autre terminal, chargez l’adresse IP de votre ordinateur local dans une variable.

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

1. Ensuite, téléchargez le certificat pour l’émulateur.

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
    ```


## <a name="consume-the-endpoint-via-ui"></a><a id="consume-endpoint-ui"></a>Consommer le point de terminaison par le biais de l’interface utilisateur

L’émulateur utilise un certificat auto-signé pour sécuriser la connectivité à son point de terminaison et doit être approuvé manuellement. Utilisez les étapes suivantes pour consommer le point de terminaison par le biais de l’interface utilisateur à l’aide du navigateur web de votre choix :

1. Assurez-vous d’avoir téléchargé le certificat auto-signé de l’émulateur

   ```bash
   curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
   ```

1. Ouvrez l’application **Trousseau d’accès** sur votre Mac pour importer le certificat de l’émulateur.

1. Sélectionnez **Fichier** et **Importer des éléments**, puis importez **emulatorcert.crt**.

1. Après avoir chargé *emulatorcert.crt* dans le trousseau, double-cliquez sur le nom **localhost** et modifiez les paramètres d’approbation en **Toujours faire confiance**.

1. Vous pouvez désormais accéder à `https://localhost:8081/_explorer/index.html` ou `https://{your_local_ip}:8081/_explorer/index.html` et récupérer la chaîne de connexion de l’émulateur.

Si vous le souhaitez, vous pouvez désactiver la validation SSL sur votre application. Ce n’est recommandé qu’à des fins de développement et ça ne doit pas être effectué lors de l’exécution dans un environnement de production.

## <a name="run-the-linux-emulator-on-linux-os"></a><a id="run-on-linux"></a>Exécuter l’émulateur Linux sur Linux OS

Pour commencer, utilisez le package `apt` et installez la dernière version de Docker.

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Si vous utilisez le Sous-système Windows pour Linux (WSL), exécutez la commande suivante pour obtenir `ifconfig` :

```bash
sudo apt-get install net-tools
```

Effectuez les étapes suivantes pour exécuter l’émulateur sur Linux :

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

4. Une fois l’émulateur en cours d’exécution, à l’aide d’un autre terminal, chargez l’adresse IP de votre ordinateur local dans une variable.

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

5. Ensuite, téléchargez le certificat pour l’émulateur. Sinon, le point de terminaison ci-dessous qui télécharge le certificat auto-signé de l’émulateur peut également être utilisé pour la signalisation lorsque le point de terminaison de l’émulateur est prêt à recevoir des requêtes d’une autre application.

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > ~/emulatorcert.crt
    ```

6. Copiez le fichier CRT dans le dossier qui contient les certificats personnalisés dans votre distribution Linux. Sur les distributions Debian, il se trouve en général sur `/usr/local/share/ca-certificates/`.

   ```bash
   cp ~/emulatorcert.crt /usr/local/share/ca-certificates/
   ```

7. Mettez à jour les certificats TLS/SSL, ce qui mettra à jour le dossier `/etc/ssl/certs/`.

   ```bash
   update-ca-certificates
   ```

    Pour les applications basées sur Java, le certificat doit être importé dans le [magasin de confiance Java.](local-emulator-export-ssl-certificates.md)

    ```bash
    keytool -keystore ~/cacerts -importcert -alias  emulator_cert -file ~/emulatorcert.crt
    java -ea -Djavax.net.ssl.trustStore=~/cacerts -Djavax.net.ssl.trustStorePassword="changeit" $APPLICATION_ARGUMENTS
    ```

## <a name="configuration-options"></a><a id="config-options"></a>Options de configuration

|Nom  |Default  |Description  |
|---------|---------|---------|
|  Ports : `-p`   |         |   Actuellement, seuls les ports 8081 et 10251-10255 sont requis par le point de terminaison de l’émulateur.     |
| `AZURE_COSMOS_EMULATOR_PARTITION_COUNT`    |    10     |    Contrôle le nombre total de partitions physiques, ce qui, en retour, contrôle le nombre de conteneurs qui peuvent être créés et exister à un moment donné. Nous vous recommandons de commencer petit pour améliorer le temps de démarrage de l’émulateur, c.-à-d. 3.     |
|  Mémoire : `-m`   |         | En mémoire, 3 Go ou plus sont requis.     |
| Cœurs : `--cpus`  |         |   Veillez à configurer suffisamment de mémoire et de cœurs de processeur. Si l’émulateur peut s’exécuter avec un minimum de 0,5 cœur (très lent cependant), il est recommandé d’en utiliser au moins 2.      |
|`AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE`  | false  | Ce paramètre utilisé en soi permet de conserver les données entre les redémarrages du conteneur.  |

## <a name="troubleshoot-issues"></a><a id="troubleshoot-issues"></a>Problèmes de dépannage

Cette section fournit des conseils pour résoudre les erreurs lors de l’utilisation de l’émulateur Linux.

### <a name="connectivity-issues"></a>Problèmes de connectivité

#### <a name="my-app-cant-connect-to-emulator-endpoint-the-ssl-connection-could-not-be-established-or-i-cant-start-the-data-explorer"></a>Mon application ne peut pas se connecter au point de terminaison de l’émulateur (« La connexion SSL n’a pas pu être établie ») ou je ne parviens pas à démarrer l’Explorateur de données

- Vérifiez que l’émulateur est en cours d’exécution avec la commande suivante :

    ```bash
    docker ps --all
    ```

- Vérifiez que le conteneur d’émulateur spécifique est dans un état En cours d’exécution.

- Vérifiez qu’aucune autre application n’utilise les ports de l’émulateur : 8081 et 10250-10255.

- Vérifiez que le port de conteneur 8081 est mappé correctement et qu’il est accessible à partir d’un environnement en dehors du conteneur.  

   ```bash
   netstat -lt
   ```

- Essayez d’accéder au point de terminaison et au port de l’émulateur à l’aide de l’adresse IP du conteneur Docker au lieu de « localhost ».

- Assurez-vous que le certificat auto-signé de l’émulateur a été correctement ajouté au [Trousseau](#consume-endpoint-ui).

- Pour les applications Java, assurez-vous d’importer le certificat dans la [section du magasin de certificats Java](#run-on-linux).

- Pour les applications .NET, vous pouvez désactiver la validation SSL :

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

Pour toute application s’exécutant dans une infrastructure compatible avec .NET Standard 2.1 ou une version ultérieure, nous pouvons tirer parti de `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

Pour toute application s’exécutant dans une infrastructure compatible avec .NET Standard 2.0, nous pouvons tirer parti de `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

---

#### <a name="my-nodejs-app-is-reporting-a-self-signed-certificate-error"></a>Mon application Node.js signale une erreur de certificat auto-signé

Si vous tentez de vous connecter à l’émulateur via une adresse autre que `localhost`, telle que l’adresse IP des conteneurs, Node.js génère une erreur signalant que le certificat est auto-signé et ce, même si le certificat a été installé.

La vérification TLS peut être désactivée par la définition de la variable d’environnement `NODE_TLS_REJECT_UNAUTHORIZED` sur `0` :

```bash
NODE_TLS_REJECT_UNAUTHORIZED=0
```

Cet indicateur n’est recommandé que pour le développement local, puisqu’il désactive TLS pour Node.js. Pour plus d’informations, consultez la [documentation Node.js](https://nodejs.org/api/cli.html#cli_node_tls_reject_unauthorized_value) et la [documentation Certificats de l’émulateur Cosmos DB](local-emulator-export-ssl-certificates.md#how-to-use-the-certificate-in-nodejs).

#### <a name="the-docker-container-failed-to-start"></a>Le conteneur Docker n’a pas pu démarrer

L’émulateur a rencontré le message suivant :

```bash
/palrun: ERROR: Invalid mapping of address 0x40037d9000 in reserved address space below 0x400000000000. Possible causes:
1. The process (itself, or via a wrapper) starts up its own running environment sets the stack size limit to unlimited via syscall setrlimit(2);
2. The process (itself, or via a wrapper) adjusts its own execution domain and flag the system its legacy personality via syscall personality(2);
3. Sysadmin deliberately sets the system to run on legacy VA layout mode by adjusting a sysctl knob vm.legacy_va_layout.
```

Cette erreur est probablement due au fait que le type de processeur de l’hôte Docker actuel est incompatible avec notre image Docker. Autrement dit, l’ordinateur est un MacBook avec un chipset M1.

#### <a name="my-app-received-too-many-connectivity-related-timeouts"></a>Mon application a reçu un trop grand nombre d’expirations de délai liées à la connectivité

- Le conteneur Docker n’est pas approvisionné avec suffisamment de ressources [(cœurs ou mémoire)](#config-options). Nous vous recommandons d’incrémenter le nombre de cœurs et de réduire le nombre de partitions physiques approvisionnées lors du démarrage.

- Assurez-vous que le nombre de connexions TCP ne dépasse pas les paramètres actuels de votre système d’exploitation.

- Essayez de réduire la taille des documents dans votre application.
    
#### <a name="my-app-could-not-provision-databasescontainers"></a>Mon application n’a pas pu approvisionner les bases de données/conteneurs

Le nombre de partitions physiques approvisionnées sur l’émulateur est trop faible. Vous pouvez soit supprimer vos bases de données/collections inutilisées, soit démarrer l’émulateur avec un [plus grand nombre de partitions physiques](#config-options).

### <a name="reliability-and-crashes"></a>Fiabilité et incidents

- Le démarrage de l’émulateur échoue :

  - Vérifiez que vous [exécutez la dernière image de l’émulateur Cosmos DB pour Linux](#refresh-linux-container). Sinon, consultez la section ci-dessus concernant les problèmes liés à la connectivité.

  - Si le dossier de données de l’émulateur Cosmos DB est un « volume monté », vérifiez que le volume dispose de suffisamment d’espace et qu’il est en lecture/écriture.

  - Vérifiez que la création d’un conteneur avec les paramètres recommandés fonctionne. Si c’est le cas, il est probable que les paramètres supplémentaires ont été transmis par le biais de la commande Docker correspondante lors du démarrage du conteneur.

  - Si l’émulateur ne parvient pas à démarrer avec l’erreur suivante :
  
    ```bash
    "Failed loading Emulator secrets certificate. Error: 0x8009000f or similar, a new policy might have been added to your host that prevents an application such as Azure Cosmos DB emulator from creating and adding self signed certificate files into your certificate store."
    ```

    Cela peut être le cas même lorsque vous l’exécutez dans le contexte Administrateur, car la stratégie spécifique généralement ajoutée par votre service informatique est prioritaire par rapport à l’administrateur local. À la place, l’utilisation d’une image Docker pour l’émulateur peut aider dans ce cas, à condition que vous ayez toujours l’autorisation d’ajouter le certificat SSL d’émulateur auto-signé dans le contexte de votre ordinateur hôte (requis par l’application cliente du SDK Cosmos Java et .NET).

- L’émulateur plante :

  - Vérifiez que la création d’un conteneur avec les [paramètres recommandés](#run-on-linux) fonctionne. Si c’est le cas, il est probable que les paramètres supplémentaires ont été transmis par le biais de la commande Docker correspondante lors du démarrage du conteneur.

  - Démarrez le conteneur Docker de l’émulateur dans un mode attaché (voir `docker start -it`).

  - Collectez les données ou faites un vidage sur incident et suivez les [étapes décrites](#report-an-emulator-issue) pour signaler le problème.  

### <a name="data-explorer-errors"></a>Erreurs de l’Explorateur de données

- Je ne peux pas afficher mes données :

  - Consultez la section relative aux problèmes liés à la connectivité ci-dessus.

  - Assurez-vous que le certificat auto-signé de l’émulateur est correctement importé et approuvé manuellement pour que votre navigateur accède à la page de l’Explorateur de données.

  - Essayez de créer une base de données/un conteneur et d’insérer un élément à l’aide de l’Explorateur de données. En cas de réussite, la cause la plus probable du problème réside dans votre application. Si ce n’est pas le cas, [contactez l’équipe Cosmos DB](#report-an-emulator-issue).

### <a name="performance-issues"></a>Problèmes de performance

Le nombre de requêtes par seconde est faible, la latence des requêtes est élevée :

- Le conteneur Docker n’est pas approvisionné avec suffisamment de ressources [(cœurs ou mémoire)](#config-options). Nous vous recommandons d’incrémenter le nombre de cœurs et de réduire le nombre de partitions physiques approvisionnées lors du démarrage.

## <a name="refresh-linux-container"></a><a id="refresh-linux-container"></a>Actualiser le conteneur Linux

Pour actualiser le conteneur Linux, procédez comme suit :

1. Exécutez la commande suivante pour répertorier tous les conteneurs Docker.

   ```bash
   docker ps --all
   ```

1. Supprimez le conteneur à l’aide de l’ID récupéré à partir de la commande ci-dessus.

   ```bash
   docker rm ID_OF_CONTAINER_FROM_ABOVE
   ```

1. La liste suivante répertorie toutes les images Docker.

   ```bash
   docker images
   ```

1. Supprimez l’image à l’aide de l’ID récupéré à l’étape précédente.

   ```bash
   docker rmi ID_OF_IMAGE_FROM_ABOVE
   ```

1. Tirez la dernière image de l’émulateur Cosmos DB pour Linux.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```

1. Pour démarrer un conteneur arrêté, exécutez ce qui suit :

   ```bash
   docker start -ai ID_OF_CONTAINER
   ```

## <a name="report-an-emulator-issue"></a>Signaler un problème d’émulateur

Lorsque vous signalez un problème avec l’émulateur Linux, fournissez autant d’informations que possible sur votre problème. Ces détails sont les suivants :

- Description de l’erreur ou du problème rencontré
- Environnement (système d’exploitation, configuration d’hôte)
- Ordinateur et type de processeur
- Commande utilisée pour créer et démarrer l’émulateur (fichier YML si Docker Compose est utilisé)
- Description de la charge de travail
- Échantillon de la base de données/de la collection et élément utilisé
- Inclure la sortie de la console à partir du démarrage du conteneur Docker pour l’émulateur en mode attaché
- Envoyez tous les éléments ci-dessus à l’[équipe Azure Cosmos DB](mailto:cdbportalfeedback@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser l’émulateur Azure Cosmos DB pour Linux pour un développement local gratuit. Vous pouvez maintenant passer aux articles suivants :

- [Exporter les certificats de l’émulateur Azure Cosmos DB pour une utilisation avec des applications Java, Python et Node.js](local-emulator-export-ssl-certificates.md)
- [Problèmes de débogage avec l’émulateur](troubleshoot-local-emulator.md)
