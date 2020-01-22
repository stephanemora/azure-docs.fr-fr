---
title: Consortium Hyperledger Fabric sur Azure Kubernetes Service (AKS)
description: Guide pratique pour déployer un réseau de consortium Hyperledger Fabric sur Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6b9cbe1638034ed05bd4759926f6488693fe8ae3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780745"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Consortium Hyperledger Fabric sur Azure Kubernetes Service (AKS)

Vous pouvez utiliser le modèle Hyperledger Fabric (HLF) sur Azure Kubernetes Service (AKS) pour déployer et configurer un réseau consortium Hyperledger Fabric sur Azure.

À la fin de cet article, vous serez capable :

- d’obtenir des connaissances de travail sur Hyperledger Fabric et les divers composants qui constituent les blocs de création d’un réseau blockchain Hyperledger Fabric ;
- de déployer et configurer un consortium Hyperledger Fabric sur Azure Kubernetes Service pour vos scénarios de production.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architecture de consortium Hyperledger Fabric

Pour créer un réseau Hyperledger Fabric sur Azure, vous devez déployer un service de classement et une organisation dotée de nœuds homologues. Les différents composants fondamentaux créés dans le cadre du déploiement du modèle sont les suivants :

- **Nœuds des auteurs des commandes** : Nœud responsable du classement des transactions dans le registre. Avec les autres nœuds, les nœuds ordonnés constituent le service de classement du réseau Hyperledger Fabric.

- **Nœuds homologues** : Nœud qui héberge principalement des registres et des contrats intelligents, ces éléments fondamentaux du réseau.

- **AC Fabric** : L’AC Fabric est l’autorité de certification (AC) pour Hyperledger Fabric. L’AC Fabric vous permet d’initialiser et de démarrer le processus serveur qui héberge l’autorité de certification. Elle vous permet de gérer les identités et les certificats. Chaque cluster AKS déployé dans le cadre du modèle aura un pod d’AC Fabric par défaut.

- **CouchDB ou LevelDB** : La base de données d’état universelle pour les nœuds homologues peut être stockée dans LevelDB ou CouchDB. LevelDB est la base de données d’état par défaut incorporée dans le nœud homologue et stocke les données de code chaîné sous forme de paires clé-valeur simples et prend uniquement en charge les requêtes de clé, de plage de clés et de clé composite. CouchDB est une autre base de données d’état facultative qui prend en charge les requêtes enrichies lorsque les valeurs de données du code chaîné sont modélisées au format JSON.

Le modèle de déploiement lance plusieurs ressources Azure dans votre abonnement. Les différentes ressources Azure déployées sont les suivantes :

- **Cluster AKS** : Cluster Azure Kubernetes configuré en fonction des paramètres d’entrée fournis par le client. Le cluster AKS a plusieurs pods configurés pour l’exécution des composants du réseau Hyperledger Fabric. Les différents pods créés sont les suivants :

  - **Outils Fabric** : L’outil Fabric est responsable de la configuration des composants Hyperledger Fabric.
  - **Pods homologues/des auteurs des commandes** : Nœuds du réseau HLF.
  - **Proxy** : Pod proxy NGNIX via lequel les applications clientes peuvent interagir avec le cluster AKS.
  - **AC Fabric** : Pod qui exécute l’AC Fabric.
- **PostgreSQL** : Une instance de PostgreSQL est déployée pour gérer les identités de l’AC Fabric.

- **Coffre de clés Azure** : Une instance de coffre de clés est déployée pour enregistrer les informations d’identification de l’AC Fabric et les certificats racines fournis par le client, qui est utilisée en cas de nouvelle tentative de déploiement du modèle et qui permet de gérer les mécanismes du modèle.
- **Disque managé Azure** : Le disque managé Azure est destiné au stockage persistant du registre et de la base de données d’état universelle des nœuds homologues.
- **Adresse IP publique** : Point de terminaison IP public du cluster AKS déployé pour interagir avec le cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configuration du réseau blockchain Hyperledger Fabric

Pour commencer, vous avez besoin d’un abonnement Azure qui peut prendre en charge le déploiement de plusieurs machines virtuelles et de comptes de stockage standard. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/free/).

Configurez le réseau blockchain Hyperledger Fabric en procédant comme suit :

- [Déployer l’organisation homologue/des auteurs des commandes](#deploy-the-ordererpeer-organization)
- [Créer le consortium](#build-the-consortium)
- [Exécuter des opérations HLF natives](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Déployer l’organisation homologue/des auteurs des commandes

Pour prendre en main le déploiement des composants du réseau HLF, accédez au [Portail Azure](https://portal.azure.com). Sélectionnez **Créer une ressource > Blockchain** et recherchez **Hyperledger Fabric sur Azure Kubernetes Service**.

1. Sélectionnez **Créer** pour commencer le déploiement du modèle. L’assistant **Créer Hyperledger Fabric sur Azure Kubernetes Service** s’affiche.

    ![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Entrez les détails du projet dans la page **Paramètres de base**.

    ![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Entrez les informations suivantes :
    - **Abonnement**: Choisissez le nom de l’abonnement dans lequel vous souhaitez déployer les composants du réseau HLF.
    - **Groupe de ressources** : Créez un groupe de ressources ou choisissez un groupe de ressources vide existant ; le groupe de ressources contiendra toutes les ressources déployées dans le cadre du modèle.
    - **Région** : Choisissez la région Azure dans laquelle vous souhaitez déployer le cluster Azure Kubernetes pour les composants HLF. Le modèle est disponible dans toutes les régions où AKS est disponible. Assurez-vous de choisir une région dans laquelle votre abonnement n’atteint pas la limite de quota de machines virtuelles.
    - **Préfixe de ressource** : Préfixe servant à nommer les ressources qui sont déployées. La longueur du préfixe de ressource doit être inférieure à six caractères et la combinaison de caractères doit inclure des chiffres et des lettres.
4. Sélectionnez l’onglet **Paramètres Fabric** pour définir les composants du réseau HLF qui seront déployés.

    ![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Entrez les informations suivantes :
    - **Nom de l’organisation** : Nom de l’organisation Fabric, qui est requis pour les différentes opérations de plan de données.
    - **Composant du réseau Fabric** : Choisissez Service de classement ou Nœuds homologues en fonction du composant réseau blockchain que vous souhaitez configurer.
    - **Nombre de nœuds** : les deux types de nœuds sont les suivants :
        - Service de classement : sélectionnez le nombre de nœuds pour la tolérance de panne du réseau. Seuls 3,5 et 7 sont des nombres de nœuds des auteurs des commandes pris en charge.
        - Nœuds homologues : vous pouvez choisir 1 à 10 nœuds en fonction de vos besoins.
    - **Base de données d’état universelle de nœuds homologues** : Choisissez entre LevelDB et CouchDB. Ce champ s’affiche lorsque l’utilisateur choisit le nœud homologue dans la liste déroulante des composants du réseau Fabric.
    - **Nom d’utilisateur Fabric** : Entrez le nom d’utilisateur utilisé pour l’authentification de l’AC Fabric.
    - **Mot de passe de l’AC Fabric** : Entrez le mot de passe pour l’authentification de l’AC Fabric.
    - **Confirmer le mot de passe** : Confirmez le mot de passe de l’AC Fabric.
    - **Certificats** : Si vous souhaitez utiliser vos propres certificats racines pour initialiser l’AC Fabric, choisissez l’option Charger le certificat racine pour l’AC Fabric ; sinon, l’AC Frabric crée par défaut des certificats auto-signés.
    - **Certificat racine** : Chargez le certificat racine (clé publique) avec lequel l’AC Fabric doit être initialisée. Les certificats au format .pem sont pris en charge et les certificats doivent être valides dans le fuseau horaire UTC.
    - **Clé privée du certificat racine** : Chargez la clé privée du certificat racine. Si vous avez un certificat .pem, qui a à la fois une clé publique et une clé privée, chargez-le ici également.


6. Sélectionnez l’onglet **Paramètres du cluster AKS** pour définir la configuration du cluster Azure Kubernetes qui est l’infrastructure sous-jacente sur laquelle les composants du réseau Fabric seront installés.

    ![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Entrez les informations suivantes :
    - **Nom du cluster Kubernetes** : Nom du cluster AKS créé. Ce champ est prérempli en fonction du préfixe de ressource fourni ; vous pouvez le modifier si nécessaire.
    - **Version de Kubernetes** : Version de Kubernetes qui sera déployée sur le cluster. En fonction de la région sélectionnée dans l’onglet **Informations de base**, les versions prises en charge disponibles peuvent changer.
    - **Préfixe DNS** : Préfixe de nom DNS (Domain Name System) pour le cluster AKS. Vous utiliserez le DNS pour vous connecter à l’API Kubernetes lors de la gestion des conteneurs après la création du cluster.
    - **Taille du nœud** : Taille du nœud Kubernetes ; vous pouvez choisir dans la liste des références SKU de machine virtuelle disponibles sur Azure. Pour des performances optimales, nous vous recommandons le Standard DS3 v2.
    - **Nombre de nœuds** : Nombre de nœuds Kubernetes à déployer dans le cluster. Nous vous recommandons de conserver ce nombre de nœuds au moins égal ou supérieur au nombre de nœuds HLF spécifiés dans les paramètres de Fabric.
    - **ID de client du principal du service** : Entrez l’ID client d’un principal de service existant ou créez-en un, ce qui est requis pour l’authentification AKS. Consultez les étapes à suivre pour [créer un principal de service](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Clé secrète client du principal de service** : Entrez la clé secrète client du principal de service fourni dans l’ID client du principal de service.
    - **Confirmer la clé secrète client** : Confirmez la clé secrète client fournie dans la clé secrète client du principal de service.
    - **Activer la supervision de conteneurs** : Choisissez d’activer l’analyse AKS, ce qui permet aux journaux AKS d’envoyer (push) dans l’espace de travail Log Analytics spécifié.
    - **Espace de travail Log Analytics** : L’espace de travail Log Analytics est rempli avec l’espace de travail par défaut qui est créé en cas d’activation de l’analyse.

8. Après avoir fourni toutes les informations ci-dessus, sélectionnez l’onglet **Vérifier et créer**. La vérification et la création déclenchent la validation des valeurs que vous avez fournies.
9. Une fois la validation réussie, vous pouvez sélectionner **Créer**.
Le déploiement prend généralement 10 à 12 minutes ; cela peut varier en fonction de la taille et du nombre de nœuds AKS spécifiés.
10. Une fois le déploiement réussi, vous êtes averti par le biais des notifications Azure dans le coin supérieur droit.
11. Sélectionnez **Accéder au groupe de ressources** pour vérifier toutes les ressources créées dans le cadre du déploiement du modèle. Tous les noms de ressources commencent par le préfixe fourni dans le paramètre **Informations de base**.

## <a name="build-the-consortium"></a>Créer le consortium

Pour créer le consortium blockchain après le déploiement du service de classement et des nœuds homologues, vous devez effectuer les étapes ci-dessous dans l’ordre. Le script **Build Your Network** (byn.sh), qui vous aide à configurer le consortium, à créer le canal et à installer le code chaîné.

> [!NOTE]
> Le script Build Your Network (byn) fourni doit strictement être utilisé pour les scénarios de démonstration ou de déploiement. Pour la configuration des niveaux de production, nous vous recommandons d’utiliser les API HLF natives.

Toutes les commandes permettant d’exécuter le script byn peuvent être exécutées via l’interface de ligne de commande (CLI) d’Azure Bash. Vous pouvez vous connecter à la version Web de l’interpréteur de commandes Azure via l’option ![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) dans l’angle supérieur droit du Portail Azure. À l’invite de commandes, saisissez bash et appuyez sur la touche Entrée pour basculer vers l’interface CLI de Bash.

Pour plus d’informations, consultez [Interpréteur de commandes Azure](https://docs.microsoft.com/azure/cloud-shell/overview).

![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Téléchargez les fichiers byn.sh et fabric-admin.yaml.

```bash-interactive
curl https://raw.githubusercontent.com/ravastra/ARM-template-for-Hyperledger-Fabric-based-on-AKS/master/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/ravastra/ARM-template-for-Hyperledger-Fabric-based-on-AKS/master/fabric-admin.yaml -o fabric-admin.yaml
```
**Définissez les variables d’environnement suivantes sur l’interpréteur de commandes Bash d’Azure CLI** :

Définir les informations de canal et les informations d’organisation des acteurs des commandes

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=<ordererDNSZone>
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Définir les informations de l’organisation homologue

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Créez un partage de fichiers Azure pour partager divers certificats publics entre les organisations homologues et des acteurs des commandes.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry 2020-01-01 --https-only --permissions lruw --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Commandes de gestion de canal**

Accédez au cluster AKS de l’organisation des acteurs des commandes et exécutez la commande pour créer un canal.

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Commandes de gestion du consortium**

Exécutez les commandes ci-dessous dans l’ordre indiqué pour ajouter une organisation homologue dans un canal et un consortium.

1. Accédez au cluster AKS de l’organisation homologue et chargez son fournisseur de service d’adhésion (MSP, Member Service Provide) sur un Stockage Fichier Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Accédez au cluster AKS de l’organisation des acteurs des commandes et ajoutez l’organisation homologue dans la canal et le consortium.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Revenez à l’organisation homologue et exécutez la commande pour joindre les nœuds homologues dans le canal.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

De même, pour ajouter d’autres organisations homologues dans le canal, mettez à jour les variables d’environnement AKS homologues en fonction de l’organisation homologue requise et exécutez les étapes 1 à 3.

**Commandes de gestion des codes chaînés**

Exécutez la commande ci-dessous pour effectuer une opération relative à un code chaîné. Ces commandes effectuent toutes les opérations sur un code chaîné de démonstration. Ce code chaîné de démonstration a deux variables : « a » et « b ». Lors de l’instanciation du code chaîné, « a » est initialisé avec 1000 et « b » est initialisé avec 2000. À chaque appel du code chaîné, 10 unités sont transférées de « a » à « b ». L’opération de requête sur le code chaîné affiche l’état universel de la variable « a ».

Exécutez les commandes suivantes sur le cluster AKS de l’organisation homologue.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Commandes d’opération du code chaîné**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Exécuter des opérations HLF natives

Pour aider les clients à prendre en main l’exécution des commandes natives Hyperledger sur le réseau HLF sur AKS. L’exemple d’application fourni utilise le Kit de développement logiciel (SDK) Fabric NodeJS pour effectuer les opérations HLF. Les commandes sont fournies pour créer une nouvelle identité d’utilisateur et installer votre propre code chaîné.

### <a name="before-you-begin"></a>Avant de commencer

Suivez les commandes ci-dessous pour la configuration initiale de l’application :

- Télécharger les fichiers d’application
- Générer un profil de connexion et un profil d’administrateur
- Importer l’identité de l’utilisateur administrateur

Une fois l’installation initiale terminée, vous pouvez utiliser le Kit de développement logiciel (SDK) pour accomplir les opérations ci-dessous :

- Génération de l’identité de l’utilisateur
- Opérations de code chaîné

Les commandes mentionnées ci-dessus peuvent être exécutées à partir d’Azure Cloud Shell.

### <a name="download-application-files"></a>Télécharger les fichiers d’application

La première installation pour l’exécution de l’application consiste à télécharger tous les fichiers d’application dans un dossier.

**Créez le dossier de l’application et entrez dans le dossier** :

```bash
mkdir app
cd app
```
Exécutez la commande ci-dessous pour télécharger tous les fichiers et packages requis :

```bash-interactive
curl https://raw.githubusercontent.com/ravastra/ARM-template-for-Hyperledger-Fabric-based-on-AKS/master/application/setup.sh | bash
```
Cette commande prend du temps pour charger tous les packages. Une fois l’exécution de la commande terminée, vous pouvez voir un dossier `node_modules` dans le répertoire actif. Tous les packages requis sont chargés dans le dossier `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Générez un profil de connexion et un profil d’administrateur

Créez un répertoire `profile` dans le dossier `app`

```bash
cd app
mkdir ./profile
```
Définissez ces variables d’environnement dans Azure Cloud Shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Exécutez la commande ci-dessous pour générer le profil de connexion et le profil d’administrateur de l’organisation

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Elle crée un profil de connexion et un `profile` d’administrateur pour l’organisation dans le dossier Profil avec les noms `<orgname>-ccp.json` et `<orgname>-admin.json` respectivement.

De même, générez un profil de connexion et un profil d’administrateur pour chaque organisation homologue et des acteurs des commandes.


### <a name="import-admin-user-identity"></a>Importer l’identité de l’utilisateur administrateur

La dernière étape consiste à importer l’identité de l’utilisateur administrateur de l’organisation dans le portefeuille.

```bash
npm run importAdmin -- -o <orgName>

```
La commande ci-dessus exécute importAdmin.js pour importer l’identité de l’utilisateur administrateur dans le portefeuille. Le script lit l’identité de l’administrateur à partir du profil d’administrateur `<orgname>-admin.json` et l’importe dans le portefeuille pour exécuter des opérations HLF.

Les scripts utilisent le portefeuille du système de fichiers pour stocker les identités. Un portefeuille est créé en fonction du chemin d’accès spécifié dans le champ « .wallet » du profil de connexion. Par défaut, le champ « .wallet » est initialisé avec `<orgname>`, ce qui signifie qu’un dossier nommé `<orgname>` est créé dans le répertoire actuel pour stocker les identités. Si vous souhaitez créer un portefeuille suivant un autre chemin d’accès, modifiez le champ « .wallet » dans le profil de connexion avant d’exécuter l’inscription de l’utilisateur administrateur et toute autre opération HLF.

De même, importez l’identité de l’utilisateur administrateur pour chaque organisation.

Pour plus d’informations sur les arguments passés dans la commande, consultez l’aide relative à la commande.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Génération de l’identité de l’utilisateur

Exécutez les commandes ci-dessous dans l’ordre indiqué pour générer de nouvelles identités d’utilisateur pour l’organisation HLF.

> [!NOTE]
> Avant de commencer les étapes de génération d’identités d’utilisateur, assurez-vous que la configuration initiale de l’application est terminée.

Définir les variables d’environnement ci-dessous dans Azure Cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Enregistrer et inscrire un nouvel utilisateur

Pour enregistrer et inscrire un nouvel utilisateur, exécutez la commande ci-dessous qui exécute registerUser.js. Elle enregistre l’identité de l’utilisateur généré dans le portefeuille.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> L’identité de l’utilisateur administrateur est utilisée pour lancer une commande d’enregistrement du nouvel utilisateur. Par conséquent, il est obligatoire d’avoir l’identité de l’utilisateur administrateur dans le portefeuille avant d’exécuter cette commande. Dans le cas contraire, cette commande échoue.

Pour plus d’informations sur les arguments passés dans la commande, consultez l’aide relative à la commande

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Opérations de code chaîné


> [!NOTE]
> Avant de commencer toute opération de code chaîné, assurez-vous que la configuration initiale de l’application est terminée.

Définissez les variables d’environnement spécifiques au code chaîné ci-dessous dans Azure Cloud Shell :

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

Les opérations de code chaîné ci-dessous peuvent être effectuées :

- Installer un code chaîné
- Instancier un code chaîné
- Appeler un code chaîné
- Interroger un code chaîné

### <a name="install-chaincode"></a>Installer un code chaîné

Exécutez la commande ci-dessous pour installer un code chaîné sur l’organisation homologue.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Elle installe un code chaîné sur tous les nœuds homologues de l’organisation définie dans la variable d’environnement `ORGNAME`. Si votre canal contient au moins deux organisations homologues et que vous souhaitez installer un code chaîné sur toutes, exécutez les commandes séparément pour chaque organisation homologue.

Procédez comme suit :

- Définissez `ORGNAME` sur `<peerOrg1Name>` et lancez la commande `installCC`.
- Définissez `ORGNAME` sur `<peerOrg2Name>` et lancez la commande `installCC`.

  Exécutez-la pour chaque organisation homologue.

Pour plus d’informations sur les arguments passés dans la commande, consultez l’aide relative à la commande.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Instancier un code chaîné

Exécutez la commande ci-dessous pour instancier un code chaîné sur l’organisation homologue.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Transmettez le nom de la fonction d’instanciation et la liste d’arguments séparés par des virgules dans `<instantiateFunc>` et `<instantiateFuncArgs>` respectivement. Par exemple, dans [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), pour instancier l’ensemble de code chaîné `<instantiateFunc>` dans `"Init"` et `<instantiateFuncArgs>` dans la chaîne vide `""`.

> [!NOTE]
> Exécutez la commande une seule fois à partir d’une organisation homologue dans le canal.
> Une fois la transaction envoyée à l’acteur de la commande, ce dernier distribue cette transaction à toutes les organisations homologues dans le canal. Par conséquent, le code chaîné est instancié sur tous les nœuds homologues et sur toutes les organisations homologues dans le canal.

Pour plus d’informations sur les arguments passés dans la commande, consultez l’aide relative à la commande

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Appeler un code chaîné

Exécutez la commande ci-dessous pour appeler la fonction de code chaîné :

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Transmettez le nom de la fonction d’appel et la liste d’arguments séparés par des virgules dans `<invokeFunction>` et `<invokeFuncArgs>` respectivement. En poursuivant avec l’exemple fabcar chaincode, pour appeler l’ensemble de fonctions initLedger `<invokeFunction>` dans `"initLedger"` et `<invokeFuncArgs>` dans `""`.

> [!NOTE]
> Exécutez la commande une seule fois à partir d’une organisation homologue dans le canal.
> Une fois la transaction envoyée à l’acteur de la commande, ce dernier distribue cette transaction à toutes les organisations homologues dans le canal. Par conséquent, l’état universel est mis à jour sur tous les nœuds homologues de toutes les organisations homologues dans le canal.

Pour plus d’informations sur les arguments passés dans la commande, consultez l’aide relative à la commande

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Interroger un code chaîné

Exécutez la commande ci-dessous pour interroger un code chaîné :

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Transmettez le nom de la fonction de requête et la liste d’arguments séparés par des virgules dans `<queryFunction>` et `<queryFuncArgs>` respectivement. Là encore, en utilisant le code chaîné `fabcar` comme référence, pour interroger toutes les voitures de l’ensemble des états universels `<queryFunction>` dans `"queryAllCars"` et `<queryArgs>' to ` dans ""`.

Pour plus d’informations sur les arguments passés dans la commande, consultez l’aide relative à la commande

```bash
npm run queryCC -- -h

```
