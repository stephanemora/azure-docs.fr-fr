---
title: Consortium Hyperledger Fabric sur Azure Kubernetes Service (AKS)
description: Guide pratique pour déployer un réseau de consortium Hyperledger Fabric sur Azure Kubernetes Service
ms.date: 07/27/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 4bc55090234a4ab33125ba43b8416de1eadb702f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533425"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Consortium Hyperledger Fabric sur Azure Kubernetes Service (AKS)

Vous pouvez utiliser le modèle Hyperledger Fabric (HLF) sur Azure Kubernetes Service (AKS) pour déployer et configurer un réseau consortium Hyperledger Fabric sur Azure.

À la fin de cet article, vous serez capable :

- d’obtenir des connaissances de travail sur Hyperledger Fabric et les divers composants qui constituent les blocs de création d’un réseau blockchain Hyperledger Fabric ;
- de déployer et configurer un consortium Hyperledger Fabric sur Azure Kubernetes Service pour vos scénarios de production.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Choisir une solution Azure Blockchain

Avant d’opter pour un modèle de solution, comparez votre scénario avec les cas d’utilisation courants des options Azure Blockchain disponibles.

Option | Modèle de service | Cas d’utilisation courant
-------|---------------|-----------------
Modèles de solution | IaaS | Les modèles de solution correspondent à des modèles Azure Resource Manager que vous pouvez utiliser pour approvisionner une topologie de réseau blockchain entièrement configurée. Les modèles déploient et configurent les services de calcul, de mise en réseau et de stockage Microsoft Azure pour un type de réseau blockchain donné. Les modèles de solution sont fournis sans contrat SLA. Utilisez la [page de questions Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) pour obtenir de l’aide.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service (préversion) simplifie la formation, la gestion et la gouvernance des réseaux blockchain de consortium. Utilisez Azure Blockchain Service pour les solutions nécessitant PaaS, la gestion de consortium ou la confidentialité des contrats et transactions.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS et PaaS | La préversion d’Azure Blockchain Workbench est une collection de services et de fonctionnalités Azure conçus pour vous aider à créer et à déployer des applications blockchain afin de partager des processus métier et des données avec d’autres organisations. Utilisez Azure Blockchain Workbench pour le prototypage d’une solution blockchain ou d’une preuve de concept d’application blockchain. Azure Blockchain Workbench est fourni sans contrat de niveau de service. Utilisez la [page de questions Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) pour obtenir de l’aide.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architecture de consortium Hyperledger Fabric

Pour créer un réseau Hyperledger Fabric sur Azure, vous devez déployer un service de classement et une organisation dotée de nœuds homologues. À l’aide du modèle de solution Hyperledger Fabric sur Azure Kubernetes Service, vous pouvez créer des nœuds de commande ou des nœuds homologues. Vous devez déployer le modèle pour chaque nœud à créer.

Les différents composants fondamentaux créés dans le cadre du déploiement du modèle sont les suivants :

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

## <a name="deploy-the-ordererpeer-organization"></a>Déployer l’organisation homologue/des auteurs des commandes

Pour commencer, vous avez besoin d’un abonnement Azure qui peut prendre en charge le déploiement de plusieurs machines virtuelles et de comptes de stockage standard. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/free/).

Pour prendre en main le déploiement des composants du réseau HLF, accédez au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **Créer une ressource > Blockchain** > recherchez **Hyperledger Fabric sur Azure Kubernetes Service (préversion)** .

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
    - **Nom de l’organisation** : Nom de l’organisation Fabric, qui est requis pour les différentes opérations de plan de données. Le nom de l’organisation doit être unique par déploiement.
    - **Composant du réseau Fabric** : Choisissez Service de tri ou Nœuds homologues en fonction du composant réseau de Blockchain que vous souhaitez configurer.
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
    - **ID de client du principal du service** : Entrez l’ID client d’un principal de service existant ou créez-en un, ce qui est requis pour l’authentification AKS. Consultez les étapes à suivre pour [créer un principal de service](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
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

Pour créer le consortium blockchain après le déploiement du service de classement et des nœuds homologues, vous devez effectuer les étapes ci-dessous dans l’ordre. Azure HLF script (azhlf), qui vous aide à configurer le consortium, à créer le canal et à utiliser les opérations de code chaîné.

> [!NOTE]
> La mise à jour qui existe dans le script consiste à fournir davantage de fonctionnalités avec le script Azure HLF. Si vous souhaitez faire référence à l’ancien script, [cliquez ici pour en savoir plus](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Ce script est compatible avec le modèle Hyperledger Fabric sur Azure Kubernetes Service version 2.0.0 et ultérieure. Pour vérifier la version du déploiement, suivez les étapes décrites dans [Résolution des problèmes](#troubleshoot).

> [!NOTE]
> Le script Azure HLF (azhlf) fourni est destiné à faciliter les scénarios de démonstration/DevTest. Le canal et le consortium créés par ce script possèdent des stratégies HLF de base pour simplifier le scénario de démonstration/DevTest. Pour la configuration de production, nous vous recommandons de mettre à jour les stratégies HLF des canaux/consortiums conformément aux besoins de conformité de votre organisation à l’aide des API HLF natives.


Toutes les commandes permettant d’exécuter le script Azure HLF peuvent être exécutées via la ligne de commande d’Azure Bash. Interface (CLI) Vous pouvez vous connecter à la version web de l’interpréteur de commande Azure via l’option   ![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) en haut à droite du portail Azure. À l’invite de commandes, tapez bash et appuyez sur Entrée pour basculer vers l’interface de ligne de commande Bash, ou choisissez *Bash* dans la barre d’outils de l’interpréteur de commandes.

Pour plus d’informations, consultez [Interpréteur de commandes Azure](../../cloud-shell/overview.md).

![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


L’illustration suivante montre le processus pas à pas pour créer un consortium entre une organisation de l’ordonnanceur et une organisation homologue. Les commandes détaillées permettant d’exécuter ces étapes sont capturées dans les sections suivantes.

![Modèle Hyperledger Fabric sur Azure Kubernetes Service](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Complétez les sections pour la configuration initiale de l’application cliente : 

1. Télécharger les fichiers d’application cliente
1. Définir les variables d’environnement
1. Importer un profil de connexion d’organisation, un utilisateur administrateur et un MSP

Une fois l’installation initiale terminée, utilisez l’application client pour accomplir les opérations suivantes :  

- Gestion de canal
- Gestion de consortium
- Gestion de code chaîné

### <a name="download-client-application-files"></a>Télécharger les fichiers d’application cliente

Le premier programme d’installation consiste à télécharger les fichiers de l’application cliente. Exécutez la commande ci-dessous pour télécharger tous les fichiers et packages requis :

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Ces commandes clonent le code d’application cliente Azure HLF à partir du référentiel GitHub public suivi de l’opération de chargement de tous les packages NPM dépendants. Une fois l’exécution de la commande terminée, vous pouvez voir un dossier node_modules dans le répertoire actif. Tous les packages requis sont chargés dans le dossier node_modules.

### <a name="setup-environment-variables"></a>Valeurs des variables d’environnement

> [!NOTE]
> Toutes les variables d’environnement respectent la convention d’affectation de noms des ressources Azure.

#### <a name="set-environment-variables-for-orderer-organization-client"></a>Définir des variables d’environnement pour le client organisationnel auteur de la commande

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-the-environment-variables-for-peer-organization-client"></a>Définir les variables d’environnement pour le client organisationnel homologue

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> En fonction du nombre d’organisations homologues dans votre consortium, vous devrez peut-être répéter les commandes Peer et définir la variable d'environnement en conséquence.

#### <a name="set-the-environment-variables-for-setting-up-azure-storage-account"></a>Définir les variables d’environnement pour configurer un compte Stockage Azure

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Suivez les étapes ci-dessous pour créer un compte Stockage Azure. Si vous avez déjà créé un compte Stockage Azure, ignorez ces étapes.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Suivez les étapes ci-dessous pour créer un partage de fichiers dans un compte Stockage Azure. Si vous avez déjà créé un partage de fichiers, ignorez ces étapes

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Suivez les étapes ci-dessous pour générer une chaîne de connexion de partage de fichiers Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Importer un profil de connexion d’organisation, une identité d’utilisateur administrateur et MSP

Exécutez les commandes suivantes pour extraire le profil de connexion de l’organisation, l’identité de l’utilisateur administrateur et le MSP du cluster Azure Kubernetes et stocker ces identités dans le magasin local de l’application cliente, par exemple, dans le répertoire « azhlfTool/stores ».

Pour l’organisation de l’ordonnanceur :

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Pour l’organisation homologue :

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-channel-command"></a>Commande Créer un canal

À partir du client de l’organisation de l’ordonnanceur, exécutez la commande pour créer un canal. Cette commande crée un canal avec l’organisation de l’ordonnanceur uniquement.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="consortium-management-commands"></a>Commandes de gestion du consortium

>[!NOTE]
> Avant de commencer toute opération de consortium, assurez-vous que la configuration initiale de l’application cliente est terminée.  

Exécutez les commandes ci-dessous dans l’ordre indiqué pour ajouter une organisation homologue dans un canal et un consortium.
1.  À partir du client de l’organisation homologue, téléchargez le MSP de l’organisation homologue sur Stockage Azure.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  À partir du client de l’organisation de l’ordonnanceur, téléchargez le MSP de l’organisation homologue à partir du Stockage Azure, puis exécutez la commande pour ajouter l’organisation homologue dans le canal/consortium.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  À partir du client de l’organisation de l’ordonnanceur, chargez le profil de connexion de l’ordonnanceur sur le Stockage Azure afin que l’organisation homologue puisse se connecter aux nœuds de l’ordonnanceur à l’aide de ce profil.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  À partir du client de l’organisation homologue, téléchargez le profil de connexion de l’ordonnanceur à partir du Stockage Azure, puis exécutez la commande pour ajouter des nœuds homologues dans le canal.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

De même, pour ajouter d’autres organisations homologues dans le canal, mettez à jour les variables d’environnement homologues en fonction de l’organisation homologue requise et exécutez les étapes 1 à 4.

### <a name="set-anchor-peers-command"></a>Commande pour la définition d’homologue(s) d’ancrage

À partir du client organisationnel homologue, émettez la commande pour définir un ou plusieurs homologues d’ancrage de l’organisation homologue sur le canal spécifié.

>[!NOTE]
> Avant d’exécuter cette commande, assurez-vous que l’organisation homologue est ajoutée au canal à l’aide des commandes de gestion de consortium.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` est une liste séparée par des espaces des nœuds homologues à définir en tant qu’homologue d’ancrage. Par exemple,

  - Définissez `<anchorPeersList>` sur « peer1 » si vous souhaitez que seul le nœud peer1 soit homologue d’ancrage.
  - Définissez `<anchorPeersList>` sur « peer1 » « peer3 » si vous souhaitez définir le nœud peer1 et peer3 en tant qu’homologue d’ancrage.

## <a name="chaincode-management-commands"></a>Commandes de gestion des codes chaînés

>[!NOTE]
> Avant de commencer toute opération de code chaîné, assurez-vous que la configuration initiale de l’application cliente est terminée.  

### <a name="set-the-below-chaincode-specific-environment-variables"></a>Définir les variables d’environnement spécifiques au code chaîné ci-dessous

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Installer un code chaîné  

Exécutez la commande ci-dessous pour installer un code chaîné sur l’organisation homologue.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Elle installe un code chaîné sur tous les nœuds homologues de l’organisation homologue définie dans la variable d’environnement ORGNAME. Si votre canal contient au moins deux organisations homologues et que vous souhaitez installer un code chaîné sur toutes, exécutez cette commande séparément pour chaque organisation homologue.  

Procédez comme suit :  

1.  Définissez `ORGNAME` et `USER_IDENTITY` en fonction de peerOrg1 et émettez la commande `./azhlf chaincode install`.  
2.  Définissez `ORGNAME` et `USER_IDENTITY` en fonction de peerOrg2 et émettez la commande `./azhlf chaincode install`.  

### <a name="instantiate-chaincode"></a>Instancier un code chaîné  

À partir de l’application cliente homologue, exécutez la commande ci-dessous pour instancier un code chaîné sur le canal.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```

Transmettez le nom de la fonction d’instanciation et la liste d’arguments séparés par des espaces dans `<instantiateFunc>` et `<instantiateFuncArgs>` respectivement. Par exemple, dans le code chaîné chaincode_example02.go, pour instancier le code chaîné, définissez `<instantiateFunc>` sur `init` et `<instantiateFuncArgs>` sur « a » « 2000 » « b » « 1000 ».

> [!NOTE]
> Exécutez la commande une seule fois à partir d’une organisation homologue dans le canal. Une fois la transaction envoyée à l’acteur de la commande, ce dernier distribue cette transaction à toutes les organisations homologues dans le canal. Par conséquent, le code chaîné est instancié sur tous les nœuds homologues et sur toutes les organisations homologues dans le canal.  

### <a name="invoke-chaincode"></a>Appeler un code chaîné  

À partir du client de l’organisation homologue, exécutez la commande ci-dessous pour appeler la fonction de code chaîné :  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Transmettez le nom de la fonction d’appel et la liste d’arguments séparés par des espaces dans `<invokeFunction>` et `<invokeFuncArgs>` respectivement. En poursuivant avec l’exemple de code chaîné chaincode_example02.go, pour effectuer l’opération d’appel, définissez `<invokeFunction>` sur `invoke` et `<invokeFuncArgs>` sur « a » « b » « 10 ».  

>[!NOTE]
> Exécutez la commande une seule fois à partir d’une organisation homologue dans le canal. Une fois la transaction envoyée à l’acteur de la commande, ce dernier distribue cette transaction à toutes les organisations homologues dans le canal. Par conséquent, l’état universel est mis à jour sur tous les nœuds homologues de toutes les organisations homologues dans le canal.  


### <a name="query-chaincode"></a>Interroger un code chaîné  

Exécutez la commande ci-dessous pour interroger un code chaîné :  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Transmettez le nom de la fonction de requête et la liste d’arguments séparés par des espaces dans `<queryFunction>` et `<queryFuncArgs>` respectivement. Là encore, en prenant comme référence le code chaîné chaincode_example02.go, pour interroger la valeur de « a» dans l’ensemble mondial d’états `<queryFunction>` à `query` et `<queryArgs>` à « a ».  

## <a name="troubleshoot"></a>Dépanner

**Pour vérifier la version du modèle en cours d’exécution**

Exécutez les commandes ci-dessous pour rechercher la version de votre déploiement de modèle.

Définissez les variables d’environnement ci-dessous selon le groupe de ressources où le modèle a été déployé.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Exécuter la commande ci-dessous pour imprimer la version du modèle
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Support et commentaires

Pour vous tenir informé sur Azure Blockchain, visitez le [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/). Vous y trouverez les toutes dernières offres du service Blockchain ainsi que diverses informations de l’équipe technique d’Azure Blockchain.

Pour faire des commentaires sur le produit ou suggérer de nouvelles fonctionnalités, postez votre idée ou votez pour une autre idée sur le [forum de commentaires Azure pour Blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Support de la communauté pour les objets blob

Communiquez avec les ingénieurs Microsoft et les experts de la communauté Azure Blockchain.

- [Page de questions Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html). Le support d’ingénierie pour les modèles blockchain est limité aux problèmes de déploiement.
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
