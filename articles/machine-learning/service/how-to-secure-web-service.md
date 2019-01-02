---
title: Sécuriser des services web avec SSL
titleSuffix: Azure Machine Learning service
description: Découvrez comment sécuriser un service web déployé avec le service Azure Machine Learning. Vous pouvez restreindre l’accès aux services web et sécuriser les données envoyées par les clients à l’aide de l’authentification SSL et l’authentification basée sur la clé.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.custom: seodec18
ms.openlocfilehash: 14350a04326ba22dcc5c8608b6ac6b9180666832
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101177"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Utiliser SSL pour sécuriser des services web avec Azure Machine Learning service

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

> [!WARNING]
> Les certificats auto-signés doivent être utilisés uniquement pour le développement. Ils ne doivent pas être utilisés en production. Les certificats autosignés peuvent entraîner des problèmes dans vos applications clientes. Pour plus d’informations, consultez la documentation relative aux bibliothèques réseau utilisées dans votre application cliente.

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

  ![Azure Machine Learning service : Sécurisation des services web avec SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md).