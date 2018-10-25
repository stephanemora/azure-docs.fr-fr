---
title: Sécuriser les services web Azure Machine Learning avec SSL
description: Découvrez comment sécuriser un service web déployé avec le service Azure Machine Learning. Vous pouvez restreindre l’accès aux services web et sécuriser les données envoyées par les clients à l’aide de l’authentification SSL et l’authentification basée sur la clé.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801010"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Sécuriser les services web Azure Machine Learning avec SSL

Dans cet article, vous allez découvrir comment sécuriser un service web déployé avec le service Azure Machine Learning. Vous pouvez restreindre l’accès aux services web et sécuriser les données envoyées par les clients à l’aide de l’authentification SSL et l’authentification basée sur la clé.

> [!Warning]
> Si vous n’activez pas le protocole SSL, n’importe quel utilisateur sur Internet sera en mesure de passer des appels au service web.

SSL chiffre les données transitant entre le client et le service web. Il est également utilisé par le client pour vérifier l’identité du serveur. L’authentification est activée uniquement pour les services qui ont fourni un certificat SSL et une clé.  Si vous activez SSL, une clé d’authentification est obligatoire pour accéder au service web.

Que vous déployiez un service web activé avec SSL ou que vous activiez SSL pour le service web déployé existant, les étapes sont les mêmes :

1. Obtenir un nom de domaine.

2. Obtenir un certificat SSL.

3. Déployer ou mettre à jour le service web avec le paramètre SSL activé.

4. Mettre à jour votre DNS afin qu’il pointe vers le service web.

La sécurisation des services web sur les [cibles de déploiement](how-to-deploy-and-where.md) présente de légères différences. 

## <a name="get-a-domain-name"></a>Obtenir un nom de domaine

Si vous ne possédez pas déjà un nom de domaine, vous pouvez en acheter un auprès d’un __bureau d’enregistrement de noms de domaine__. La procédure et le coût varient selon les bureaux d’enregistrement. Le bureau d’enregistrement fournit également des outils qui vous permettent de gérer le nom de domaine. Ces outils sont utilisés pour mapper un nom de domaine complet (par exemple, www.contoso.com) à l’adresse IP sur laquelle votre service web est hébergé.

## <a name="get-an-ssl-certificate"></a>Obtenir un certificat SSL

Il existe de nombreuses façons d’obtenir un certificat SSL. Le moyen le plus courant consiste à en acheter un auprès d’une __autorité de certification__ (CA). Quel que soit l’endroit où vous obtenez le certificat, vous avez besoin des fichiers suivants :

* Un __certificat__. Le certificat doit contenir la chaîne d’approbation et doit être codé en PEM.
* Une __clé__. La clé doit être codée en PEM.

Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet (FQDN) de l’adresse que vous envisagez d’utiliser pour le service web. Par exemple, contoso.com. L’adresse estampillée sur le certificat et l’adresse utilisée par les clients sont comparées pour valider l’identité du service web. Si les adresses ne correspondent pas, les clients reçoivent une erreur. 

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!IMPORTANT]
> Les certificats auto-signés doivent être utilisés uniquement pour le développement. Ils ne doivent pas être utilisés en production. Si vous utilisez un certificat auto-signé, consultez la section [Utilisation des services web avec des certificats auto-signés](#self-signed) pour obtenir des instructions spécifiques.


## <a name="enable-ssl-and-deploy"></a>Activer le protocole SSL et le déployer

Pour déployer (ou redéployer) le service avec le protocole SSL activé, définissez le paramètre `ssl_enabled` sur `True`, si nécessaire. Définissez le paramètre `ssl_certificate` sur la valeur du fichier __certificate__ et le paramètre `ssl_key` sur la valeur du fichier __key__. 

+ **Déployer Azure Kubernetes Service (AKS)**
  
  Lors de la configuration du cluster AKS, indiquez les valeurs des paramètres SSL comme indiqué dans l’extrait de code :

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Déployer sur Azure Container Instances (ACI)**
 
  Lors du déploiement sur ACI, indiquez les valeurs des paramètres SSL comme indiqué dans l’extrait de code :

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Déployer sur des Field Programmable Gate Arrays (FPGA)**

  La réponse de l’opération `create_service` contient l’adresse IP du service. L’adresse IP est utilisée lors du mappage du nom DNS à l’adresse IP du service. La réponse contient également une __clé primaire__ et une __clé secondaire__ qui permettent d’utiliser le service. Indiquez les valeurs des paramètres SSL comme indiqué dans l’extrait de code :

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

## <a name="update-your-dns"></a>Mettre à jour votre DNS

Vous devez ensuite mettre à jour votre DNS afin qu’il pointe vers le service web.

+ **Pour ACI et FPGA** :  

  Utilisez les outils fournis par votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.  

  Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.

+ **Pour AKS** : 

  Mettez à jour le DNS sous l’onglet « Configuration » de l’« adresse IP publique » du cluster AKS, comme illustré dans l’image. Vous pouvez trouver l’adresse IP publique comme l’un des types de ressources créés sous le groupe de ressources qui contient les nœuds d’agent AKS et d’autres ressources de mise en réseau.

  ![Service Azure Machine Learning : Sécuriser les services web avec SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>Utiliser des services authentifiés

### <a name="how-to-consume"></a>Mode d’utilisation 
+ **Pour ACI et AKS** : 

  Pour les services web ACI et AKS, découvrez comment utiliser les services web dans les articles suivants :
  + [Déployer sur ACI](how-to-deploy-to-aci.md)

  + [Déployer sur AKS](how-to-deploy-to-aks.md)

+ **Pour FPGA** :  

  Les exemples suivants montrent comment utiliser un service FPGA authentifié à l’aide de Python et de C#.
  Remplacez `authkey` par la clé primaire ou secondaire retournée lors du déploiement du service.

  Exemple Python :
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  Exemple en code C# :
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Définir l’en-tête d’autorisation
D’autres clients gRPC peuvent authentifier les demandes en définissant un en-tête d’autorisation. L’approche générale consiste à créer un objet `ChannelCredentials` qui associe `SslCredentials` à `CallCredentials`. Celui-ci est ajouté à l’en-tête d’autorisation de la demande. Pour plus d’informations sur l’implémentation de la prise en charge pour vos en-têtes spécifiques, consultez [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

Les exemples suivants montrent comment définir l’en-tête en C# et Go :

+ Utilisez C# pour définir l’en-tête :
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Utilisez Go pour définir l’en-tête :
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Utiliser des services avec des certificats auto-signés

Il existe deux façons de permettre au client de s’authentifier auprès d’un serveur sécurisé avec un certificat auto-signé :

* Sur le système client, définissez la variable d’environnement `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` sur le système client pour pointer vers le fichier de certificat.

* Lorsque vous construisez un objet `SslCredentials`, passez le contenu du fichier de certificat au constructeur.

Ces deux méthodes obligent gRPC à utiliser le certificat en tant que certificat racine.

> [!IMPORTANT]
> gRPC n’accepte pas les certificats non approuvés. L’utilisation d’un certificat non approuvé entraîne un échec avec un code d’état `Unavailable`. Les détails de l’échec contiennent `Connection Failed`.
