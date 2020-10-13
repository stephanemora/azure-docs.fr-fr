---
title: Utiliser Azure Kubernetes avec Azure Cosmos DB
description: Découvrez comment démarrer un cluster Kubernetes sur Azure qui utilise Azure Cosmos DB (préversion)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: b9e6e1388465542e9fb3ac69540c981a1fd70d4b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570117"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Utiliser Azure Kubernetes avec Azure Cosmos DB (préversion)

L'API etcd d'Azure Cosmos DB vous permet d’utiliser Azure Cosmos DB en tant que magasin principal pour Azure Kubernetes. Azure Cosmos DB implémente le protocole filaire etcd pour permettre aux serveurs d’API du nœud principal d’utiliser Azure Cosmos DB comme pour accéder à un etcd installé localement. Dans Azure Cosmos DB, l'API etcd est actuellement préversion. Lorsque vous utilisez l'API etcd Azure Cosmos en tant que magasin de stockage pour Kubernetes, vous bénéficiez des avantages suivants : 

* Configuration manuelle et gestion d'etcd inutiles.
* Haute disponibilité d’etcd, garantie par Cosmos (99,99 % dans une région, à 99,999 % dans plusieurs régions).
* Extensibilité élastique d'etcd.
* Sécurisé par défaut et adapté au monde de l’entreprise.
* Contrats de niveau de service complets à la pointe du secteur.

Pour en savoir plus sur l'API etcd dans Azure Cosmos DB, consultez l’article [Vue d’ensemble](etcd-api-introduction.md). Cet article vous explique comment utiliser [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) pour démarrer un cluster Kubernetes sur Azure qui utilise [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) plutôt qu'un etcd installé et configuré localement. 

## <a name="prerequisites"></a>Prérequis

1. Installez la dernière version d'[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Vous pouvez télécharger et installer une version d'Azure CLI spécifique à votre système d’exploitation.

1. Installez la [version la plus récente](https://github.com/Azure/aks-engine/releases) d’Azure Kubernetes Engine. Les instructions d’installation des différents systèmes d’exploitation sont disponibles dans la page [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine). Suivez les étapes de la section **Installer AKS Engine** de la documentation correspondante. Une fois téléchargé, extrayez le fichier zip.

   Azure Kubernetes Engine (**aks-engine**) génère des modèles Azure Resource Manager pour les clusters Kubernetes sur Azure. L’entrée d'aks-engine correspond à un fichier de définition de cluster qui décrit le cluster souhaité, notamment l'orchestrateur, les fonctionnalités et les agents. La structure des fichiers d’entrée ressemble à l’API publique d'Azure Kubernetes Service.

1. Dans Azure Cosmos DB, l'API etcd est actuellement préversion. Inscrivez-vous pour utiliser la préversion sur : https://aka.ms/cosmosetcdapi-signup. Une fois le formulaire envoyé, votre abonnement sera autorisé à utiliser l’API etcd Azure Cosmos DB. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Déployer le cluster avec Azure Cosmos DB

1. Ouvrez une fenêtre d’invite de commandes et connectez-vous à Azure à l'aide de la commande suivante :

   ```azurecli-interactive
   az login 
   ```

1. Si vous disposez de plusieurs abonnements, basculez vers l’abonnement autorisé à utiliser l’API etcd Azure Cosmos DB. Pour basculer vers l'abonnement requis, utilisez la commande suivante :

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Ensuite, créez un groupe de ressources dans lequel vous déploierez les ressources requises par le cluster Azure Kubernetes. Veillez à créer le groupe de ressources dans la région « centralus ». Le groupe de ressources ne doit pas obligatoirement se trouver dans la région « centralus », mais actuellement, l'API etcd Azure Cosmos peut être déployée dans la seule région « centralus ». Dès lors, il est préférable de colocaliser le cluster Kubernetes avec l'instance etcd Cosmos :

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Créez ensuite un principal de service pour le cluster Kubernetes Azure afin de lui permettre de communiquer avec les ressources qui font partie du même groupe de ressources. Vous pouvez créer un principal de service à l’aide d’Azure CLI, de PowerShell ou du portail Azure. Dans cet exemple, vous allez utiliser Azure CLI.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Cette commande renvoie les détails d’un service principal, par exemple :
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Notez la valeur des champs **appId** et **password** car vous utiliserez ces paramètres dans les étapes suivantes. 

1. À partir de l’invite de commandes, accédez au dossier où se trouve l’exécutable Azure Kubernetes Engine. Par exemple, dans votre invite de commandes, vous pouvez accéder au dossier comme suit :

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Ouvrez l'éditeur de texte de votre choix et définissez un modèle Resource Manager qui déploie le cluster Azure Kubernetes avec l'API etcd Azure Cosmos DB. Copiez la définition JSON suivante dans votre éditeur de texte et enregistrez le fichier sous `apiModel.json` :

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   Dans le fichier de définition JSON/cluster, le paramètre clé à noter est **« cosmosEtcd » : true**. Ce paramètre se trouve dans les propriétés « masterProfile » et indique le déploiement pour utiliser l'API etcd Azure Cosmos plutôt que l'API etcd classique. 

1. Utilisez la commande suivante pour déployer le cluster Azure Kubernetes qui utilise Azure Cosmos DB :

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes Engine utilise une définition de cluster qui décrit la forme, la taille et la configuration souhaitées du cluster Azure Kubernetes. Plusieurs fonctionnalités peuvent être activées via la définition du cluster. Dans cet exemple, vous allez utiliser les paramètres suivants :

   * **subscription-id** : ID d’abonnement Azure avec API etcd Azure Cosmos DB activée.
   * **client-id :** appID du principal de service. La valeur `appId` a été renvoyée à l’étape 4.
   * **Client-secret :** Mot de passe du principal de service ou mot de passe généré de manière aléatoire. Cette valeur a été renvoyée dans le paramètre « password » de l’étape 4. 
   * **dnsPrefix :** Nom DNS propre à la région. Cette valeur fait partie du nom d'hôte (exemples de valeurs : are-myprod1, staging).
   * **location :**  Emplacement où le cluster doit être déployé ; actuellement, seule la région « centralus » est prise en charge.

   > [!Note]
   > Actuellement, l’API etcd Azure Cosmos peut uniquement être déployée dans la région « centralus ». 
 
   * **api-model :** Chemin d'accès pleinement qualifié fichier de modèle.
   * **force-overwrite :** Cette option permet de remplacer automatiquement les fichiers présents dans le répertoire de sortie.
 
   La commande suivante illustre un exemple de déploiement :

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Le déploiement du modèle prend plusieurs minutes. Une fois le déploiement terminé, la sortie suivante s'affiche dans l'invite de commandes :

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Le groupe de ressources contient maintenant des ressources telles que : machine virtuelle, compte Azure Cosmos (API etcd), réseau virtuel, groupe à haute disponibilité, entre autres ressources requises par le cluster Kubernetes. 

Le nom du compte Azure Cosmos correspond au préfixe DNS spécifié suivi de k8s. Votre compte Azure Cosmos est automatiquement approvisionné avec une base de données et un conteneur respectivement nommés **EtcdDB** et **EtcdData**. Le conteneur stocke toutes les données liées à etcd. Le conteneur est approvisionné avec un certain nombre d’unités de requête et vous pouvez [mettre à l’échelle (augmenter/diminuer) le débit](scaling-throughput.md) en fonction de votre charge de travail. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [utiliser une base de données, des conteneurs et des éléments Azure Cosmos](databases-containers-items.md)
* Découvrir comment [optimiser les coûts du débit approvisionné](optimize-cost-throughput.md)

