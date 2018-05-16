---
title: Comment déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning
description: Découvrez comment déployer un service web avec un modèle en cours d’exécution sur un FPGA avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning

Dans ce document, vous allez apprendre à configurer votre environnement de station de travail et à déployer un modèle en tant que service web sur des [FGPA (field programmable gate arrays)](concept-accelerate-with-fpgas.md). Le service web utilise le projet Brainwave pour exécuter le modèle sur un FPGA.

L’utilisation de FGPA assure une inférence à très faible latence, même avec une taille de lot unique.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Créer un compte Gestion des modèles Azure Machine Learning

1. Accédez à la page de création de comptes Gestion des modèles sur le [Portail Azure](https://aka.ms/aml-create-mma).

2. Dans le portail, créez un compte Gestion des modèles dans la région **Est des États-Unis 2**.

   ![Image de l’écran de création d’un compte Gestion des modèles](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Donnez un nom à votre compte Gestion des modèles, choisissez un abonnement, puis choisissez un groupe de ressources.

   >[!IMPORTANT]
   >Pour l’emplacement, vous devez choisir la région **Est des États-Unis 2**.  Aucune autre région n’est actuellement disponible.

4. Choisissez un niveau tarifaire (le niveau S1 est suffisant, mais pouvez également utiliser S2 et S3).  Le niveau DevTest n’est pas pris en charge.  

5. Cliquez sur **Sélectionner** pour confirmer le niveau de tarification.

6. Cliquez sur **Créer** sur le volet Gestion des modèles ML à gauche de l’écran.

## <a name="get-model-management-account-information"></a>Obtenir les informations du compte Gestion des modèles

Pour obtenir des informations sur votre compte Gestion des modèles, cliquez sur le __Compte Gestion des modèles__ dans le portail Azure.

Copiez les valeurs des éléments suivants :

+ Nom du compte Gestion des modèles (en haut à gauche)
+ Nom de groupe ressources
+ Identifiant d’abonnement
+ Emplacement (utilisez « eastus2 »)

![Informations du compte Gestion des modèles](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Configurer votre machine

Pour configurer votre station de travail pour un déploiement FPGA, suivez les étapes ci-dessous :

1. Téléchargez et installez la toute dernière version de [Git](https://git-scm.com/downloads).

2. Installez [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Pour télécharger l’environnement Anaconda, utilisez la commande suivante à partir d’une invite de commandes Git :

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Pour créer l’environnement, ouvrez une **invite Anaconda** (et non une invite d’Azure Machine Learning Workbench) et exécutez la commande suivante :

    > [!IMPORTANT]
    > Le fichier `environment.yml` se trouve dans le référentiel git que vous avez cloné à l’étape précédente. Modifiez le chemin d’accès de manière à pointer vers le fichier résidant sur votre station de travail.

    ```
    conda env create -f environment.yml
    ```

5. Pour activer l’environnement, utilisez la commande suivante :

    ```
    conda activate amlrealtimeai
    ```

6. Pour démarrer le serveur Jupyter Notebook, utilisez la commande suivante :

    ```
    jupyter notebook
    ```

    Le résultat de la commande se présente ainsi :

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Vous obtenez un jeton différent chaque fois que vous exécutez la commande.

    Si votre navigateur ne s’ouvre pas automatiquement sur le serveur Jupyter Notebook, utilisez l’URL HTTP renvoyée par la commande précédente pour ouvrir la page.

    ![Image de la page web Jupyter Notebook](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Déployer votre modèle

Dans Jupyter Notebook, ouvrez le notebook `00_QuickStart.ipynb` à partir du répertoire `notebooks/resnet50`. Suivez les instructions du notebook pour :

* Définir le service
* Déployer le modèle
* Utiliser le modèle déployé
* Supprimer les services déployés

> [!IMPORTANT]
> Pour optimiser la latence et le débit, votre station de travail doit se trouver dans la même région Azure en tant que point de terminaison.  Les API sont actuellement créées dans la région Azure Est des États-Unis.

## <a name="ssltls-and-authentication"></a>SSL/TLS et authentification

Azure Machine Learning prend en charge le protocole SSL et l’authentification par clé. Cela vous permet de restreindre l’accès à votre service et de sécuriser les données envoyées par vos clients.

> [!NOTE]
> Les étapes décrites dans cette section s’appliquent uniquement aux modèles Azure Machine Learning Hardware Accelerated. Pour les services Azure Machine Learning standard, consultez le document [Activer SSL sur un cluster Azure Machine Learning Compute (MLC)](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc).

> [!IMPORTANT]
> L’authentification est activée uniquement pour les services qui ont fourni un certificat SSL et une clé. 
>
> Si vous n’activez pas le protocole SSL, n’importe quel utilisateur sur Internet sera en mesure de passer des appels au service.
>
> Si vous activez SSL, une clé d’authentification est obligatoire pour accéder au service.

SSL chiffre les données envoyées entre le client et le service. Il est également utilisé par le client pour vérifier l’identité du serveur.

Vous pouvez déployer un service sur lequel SSL est activé ou mettre à jour un service déjà déployé de manière à activer le protocole. La procédure est la même :

1. Acquérir un nom de domaine.

2. Acquérir un certificat SSL.

3. Déployer ou mettre à jour le service sur lequel SSL est activé.

4. Mettre à jour votre serveur DNS pour pointer vers le service.

### <a name="acquire-a-domain-name"></a>Acquérir un nom de domaine

Si vous ne possédez pas déjà un nom de domaine, vous pouvez en acheter un auprès d’un __bureau d’enregistrement de noms de domaine__. La procédure et le coût varient selon les bureaux d’enregistrement. Le bureau d’enregistrement fournit également des outils qui vous permettent de gérer le nom de domaine. Ces outils sont utilisés pour mapper un nom de domaine complet (par exemple, www.contoso.com) à l’adresse IP sur laquelle votre service est hébergé.

### <a name="acquire-an-ssl-certificate"></a>Acquérir un certificat SSL

Il existe de nombreuses façons d’obtenir un certificat SSL. Le moyen le plus courant consiste à en acheter un auprès d’une __autorité de certification__ (CA). Quel que soit l’endroit où vous obtenez le certificat, vous avez besoin des fichiers suivants :

* Un __certificat__. Le certificat doit contenir la chaîne d’approbation et doit être codé en PEM.
* Une __clé__. La clé doit être codée en PEM.

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!IMPORTANT]
> Les certificats auto-signés doivent être utilisés uniquement pour le développement. Ils ne doivent pas être utilisés en production.
>
> Si vous utilisez un certificat auto-signé, consultez la section [Utilisation des services avec des certificats auto-signés](#self-signed) pour obtenir des instructions spécifiques.

> [!WARNING]
> Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet (FQDN) de l’adresse que vous envisagez d’utiliser pour le service. Par exemple, contoso.com. L’adresse estampillée sur le certificat et l’adresse utilisée par les clients sont comparées pour valider l’identité du service.
>
> Si les adresses ne correspondent pas, les clients reçoivent une erreur. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Déployer ou mettre à jour le service sur lequel SSL est activé

Pour déployer le service avec le protocole SSL activé, définissez le paramètre `ssl_enabled` sur `True`. Définissez le paramètre `ssl_certificate` sur la valeur du fichier __certificate__ et le paramètre `ssl_key` sur la valeur du fichier __key__. L’exemple suivant illustre le déploiement d’un service sur lequel SSL est activé :

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

La réponse de l’opération `create_service` contient l’adresse IP du service. L’adresse IP est utilisée lors du mappage du nom DNS à l’adresse IP du service.

La réponse contient également une __clé primaire__ et une __clé secondaire__ qui permettent d’utiliser le service.

### <a name="update-your-dns-to-point-to-the-service"></a>Mettre à jour votre serveur DNS pour pointer vers le service

Utilisez les outils fournis par votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.

> [!NOTE]
> Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.

### <a name="consuming-authenticated-services"></a>Utilisation des services authentifiés

Les exemples suivants montrent comment utiliser un service authentifié à l’aide de Python et de C# :

> [!NOTE]
> Remplacez `authkey` par la clé primaire ou secondaire retournée lors de la création du service.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

D’autres clients gRPC peuvent authentifier les demandes en définissant un en-tête d’autorisation. L’approche générale consiste à créer un objet `ChannelCredentials` qui associe `SslCredentials` à `CallCredentials`. Celui-ci est ajouté à l’en-tête d’autorisation de la demande. Pour plus d’informations sur l’implémentation de la prise en charge pour vos en-têtes spécifiques, consultez [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

Les exemples suivants montrent comment définir l’en-tête en C# et Go :

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Utilisation des services avec des certificats auto-signés

Il existe deux façons de permettre au client de s’authentifier auprès d’un serveur sécurisé avec un certificat auto-signé :

* Sur le système client, définissez la variable d’environnement `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` sur le système client pour pointer vers le fichier de certificat.

* Lorsque vous construisez un objet `SslCredentials`, passez le contenu du fichier de certificat au constructeur.

Ces deux méthodes obligent gRPC à utiliser le certificat en tant que certificat racine.

> [!IMPORTANT]
> gRPC n’accepte pas les certificats non approuvés. L’utilisation d’un certificat non approuvé entraîne un échec avec un code d’état `Unavailable`. Les détails de l’échec contiennent `Connection Failed`.
