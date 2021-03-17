---
title: Sécuriser des services web à l’aide du protocole TLS
titleSuffix: Azure Machine Learning
description: Découvrez comment activer HTTPS avec TLS version 1.2 pour sécuriser un service web déployé par le biais d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 03/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 71cb2e9e112c49d77a2a0b47c24c49cabfa86589
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149016"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Utiliser TLS pour sécuriser un service web par le biais d’Azure Machine Learning


Cet article vous expliquer comment sécuriser un service web déployé par le biais d’Azure Machine Learning.

Vous utilisez [HTTPS](https://en.wikipedia.org/wiki/HTTPS) pour restreindre l’accès aux services web et sécuriser les données soumises par les clients. HTTPS permet de sécuriser les communications entre un client et un service web en chiffrant les communications entre les deux. Le chiffrement utilise [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS est toujours parfois appelé *SSL (Secure Sockets Layer)* , qui était le prédécesseur de TLS.

> [!TIP]
> Le kit de développement logiciel (SDK) Azure Machine Learning utilise le terme « SSL » pour les propriétés liées aux communications sécurisées. Cela ne signifie pas que votre service web n’utilise pas *TLS*. SSL est tout simplement un terme plus répandu.
>
> Plus précisément, les services web déployés par le biais d’Azure Machine Learning prennent uniquement en charge TLS version 1.2 pour AKS et ACI. Pour les déploiements ACI, si vous utilisez une ancienne version de TLS, nous vous recommandons d’effectuer un nouveau déploiement pour obtenir la dernière version de TLS.
>
> La version 1.3 de TLS pour l’inférence AKS d’Azure Machine Learning n’est pas prise en charge.

TLS et SSL s'appuient tous deux sur des *certificats numériques* qui facilitent le chiffrement et la vérification d'identité. Pour plus d’informations sur le fonctionnement des certificats numériques, consultez l'article Wikipédia [Infrastructure à clé publique](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Si vous n’utilisez pas HTTPS pour votre service web, les données envoyées vers et depuis le service peuvent être visibles par d'autres personnes sur Internet.
>
> HTTPS permet également au client de vérifier l’authenticité du serveur auquel il se connecte. Cette fonctionnalité protège les clients des attaques [de l'intercepteur](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

Voici le processus général visant à sécuriser un service web :

1. Obtenir un nom de domaine.

2. Obtenir un certificat numérique.

3. Déployer ou mettre à jour le service web sur lequel TLS est activé.

4. Mettre à jour votre DNS afin qu’il pointe vers le service web.

> [!IMPORTANT]
> Si vous effectuez un déploiement vers Azure Kubernetes Service (AKS), vous pouvez acheter votre propre certificat ou utiliser un certificat fourni par Microsoft. Si vous utilisez un certificat fourni par Microsoft, vous n'êtes pas tenu d'obtenir un nom de domaine ou un certificat TLS/SSL. Pour plus d’informations, consultez la section [Activer le protocole TLS et le déployer](#enable) de cet article.

La sécurisation des services web sur les [cibles de déploiement](how-to-deploy-and-where.md) présente de légères différences.

## <a name="get-a-domain-name"></a>Obtenir un nom de domaine

Si vous ne possédez pas de nom de domaine, achetez-en-un auprès d’un *bureau d’enregistrement de noms de domaine*. Le processus et le prix varient selon les bureaux d’enregistrement. Le bureau d’enregistrement propose des outils pour gérer le nom de domaine. Vous utilisez ces outils pour mapper un nom de domaine complet (FQDN) (par exemple, www\.contoso.com) à l’adresse IP qui héberge votre service web.

## <a name="get-a-tlsssl-certificate"></a>Obtenir un certificat TLS/SSL

Il existe de nombreuses façons d’obtenir un certificat TLS/SSL (certificat numérique). Le moyen le plus courant consiste à en acheter un auprès d’une *autorité de certification* (CA). Quel que soit l’endroit où vous obtenez le certificat, vous avez besoin des fichiers suivants :

* Un **certificat**. Le certificat doit contenir la chaîne d’approbation et doit être « codé en PEM ».
* Une **clé**. La clé doit aussi être codée en PEM.

Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet (FQDN) de l’adresse que vous envisagez d’utiliser pour le service web (par exemple, www\.contoso.com). L’adresse estampillée sur le certificat et l’adresse utilisée par les clients sont comparées pour vérifier l’identité du service web. Si ces adresses ne correspondent pas, le client reçoit un message d’erreur.

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!WARNING]
> Utilisez les certificats *auto-signés* uniquement pour le développement. Ne les utilisez pas dans des environnements de production. Les certificats autosignés peuvent entraîner des problèmes dans vos applications clientes. Pour plus d’informations, consultez la documentation relative aux bibliothèques réseau que votre application cliente utilise.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Activer le protocole TLS et le déployer

**Pour un déploiement AKS**, vous pouvez activer la terminaison TLS lorsque vous [créez ou attachez un cluster AKS](how-to-create-attach-kubernetes.md) dans un espace de travail AML. Au moment du déploiement du modèle AKS, vous pouvez désactiver la terminaison TLS avec l’objet de configuration de déploiement. Dans le cas contraire, la terminaison TLS est activée pour tous les déploiements de modèle AKS par défaut au moment de la création ou de l’attachement du cluster AKS.

Pour le déploiement ACI, vous pouvez activer la terminaison TLS au moment du déploiement du modèle avec l’objet de configuration de déploiement.


### <a name="deploy-on-azure-kubernetes-service"></a>Déployer sur Azure Kubernetes Service

  > [!NOTE]
  > Les informations contenues dans cette section s’appliquent également lorsque vous déployez un service web sécurisé pour le concepteur. Si vous n’êtes pas habitué à utiliser le kit de développement logiciel (SDK) Python, consultez [Présentation du kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro).

Lorsque vous [créez ou attachez un cluster AKS](how-to-create-attach-kubernetes.md) dans un espace de travail AML, vous pouvez activer la terminaison TLS avec les objets de configuration **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** et **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Les deux méthodes retournent un objet de configuration qui a une méthode **enable_ssl** et vous pouvez utiliser la méthode **enable_ssl** pour activer TLS.

Vous pouvez activer TLS à l’aide d’un certificat Microsoft ou d’un certificat personnalisé acheté auprès d’une autorité de certification. 

* **Lorsque vous utilisez un certificat fourni par Microsoft**, vous devez utiliser le paramètre *leaf_domain_label*. Ce paramètre génère le nom DNS du service. Par exemple, une valeur « contoso » crée un nom de domaine « contoso\<six-random-characters>.\<azureregion>.cloudapp.azure.com », où \<azureregion> correspond à la région contenant le service. Vous pouvez également utiliser le paramètre *overwrite_existing_domain* pour remplacer le paramètre *leaf_domain_label* existant. L’exemple suivant montre comment créer une configuration qui active TLS avec un certificat Microsoft :

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Lorsque vous utilisez un certificat fourni par Microsoft, vous n'êtes pas tenu d'acheter votre propre certificat ou nom de domaine.

    > [!WARNING]
    > Si votre cluster AKS est configuré avec un équilibreur de charge interne, l'utilisation d'un certificat fourni par Microsoft n’est __pas prise en charge__ et vous devez utiliser un certificat personnalisé pour activer TLS.

* **Lorsque vous utilisez un certificat personnalisé que vous avez acheté**, vous utilisez les paramètres *ssl_cert_pem_file*, *ssl_key_pem_file* et *ssl_cname*. L’exemple suivant montre comment utiliser les fichiers .pem pour créer une configuration s'appuyant sur un certificat TLS/SSL que vous avez acheté :
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Pour plus d’informations sur *enable_ssl*, consultez [AksProvisioningConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) et [AksAttachConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Déployer sur Azure Container Instances

Lorsque vous déployez sur Azure Container Instances, vous fournissez des valeurs pour les paramètres TLS, comme l'illustre l’extrait de code suivant :

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Pour plus d’informations, consultez [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Mettre à jour votre DNS

Pour un déploiement AKS avec un certificat personnalisé ou un déploiement ACI, vous devez mettre à jour votre enregistrement DNS de façon à ce qu’il pointe vers l’adresse IP du point de terminaison de scoring.

  > [!IMPORTANT]
  > Lorsque vous utilisez un certificat provenant de Microsoft pour un déploiement AKS, vous n’avez pas besoin de mettre à jour manuellement la valeur DNS pour le cluster. Cette valeur doit être définie automatiquement.

Vous pouvez suivre les étapes ci-dessous afin de mettre à jour l’enregistrement DNS pour votre nom de domaine personnalisé :
* Obtenez l’adresse IP du point de terminaison de scoring à partir de l’URI de point de terminaison de scoring, qui est généralement au format *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* Utilisez les outils de votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du point de terminaison de scoring.
* Après la mise à jour de l’enregistrement DNS, vous pouvez valider la résolution DNS à l’aide de la commande *nslookup custom-domain-name*. Si l’enregistrement DNS est correctement mis à jour, le nom de domaine personnalisé pointe alors vers l’adresse IP du point de terminaison de scoring.
* Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.


## <a name="update-the-tlsssl-certificate"></a>Mettre à jour le certificat TLS/SSL

Les certificats TLS/SSL expirent et doivent être renouvelés. En général, cela se produit chaque année. Utilisez les informations des sections suivantes pour mettre à jour et renouveler votre certificat pour les modèles déployés sur Azure Kubernetes Service :

### <a name="update-a-microsoft-generated-certificate"></a>Mettre à jour un certificat généré par Microsoft

Si le certificat a été à l’origine généré par Microsoft (avec *leaf_domain_label* pour créer le service), **il se renouvellera automatiquement** quand ce sera nécessaire. Si vous souhaitez le renouveler manuellement, utilisez l’un des exemples suivants pour le mettre à jour :

> [!IMPORTANT]
> * Si le certificat existant est toujours valide, utilisez `renew=True` (Kit de développement logiciel [SDK]) ou `--ssl-renew` (CLI) pour forcer la configuration à le renouveler. Par exemple, si le certificat existant est encore valide pendant 10 jours et que vous n’utilisez pas `renew=True`, le certificat ne peut pas être renouvelé.
> * Lorsque le service a été déployé à l’origine, le `leaf_domain_label` a été utilisé pour créer un nom DNS selon le modèle `<leaf-domain-label>######.<azure-region>.cloudapp.azure.com`. Pour conserver le nom existant (y compris les six chiffres générés à l’origine), utilisez la valeur `leaf_domain_label` d’origine. N’incluez pas les six chiffres qui ont été générés.

**Utilisez le SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Utiliser l’interface CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Pour en savoir plus, consultez les documents de référence suivants :

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

### <a name="update-custom-certificate"></a>Mettre à jour un certificat personnalisé

Si le certificat a été initialement généré par une autorité de certification, procédez comme suit :

1. Utilisez la documentation fournie par l’autorité de certification pour renouveler le certificat. Ce processus crée de nouveaux fichiers de certificat.

1. Utilisez le kit de développement logiciel (SDK) ou l’interface CLI pour mettre à jour le service avec le nouveau certificat :

    **Utilisez le SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Utiliser l’interface CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Pour en savoir plus, consultez les documents de référence suivants :

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

## <a name="disable-tls"></a>Désactiver le protocole TLS

Pour désactiver TLS pour un modèle déployé sur Azure Kubernetes Service, créez un `SslConfiguration` avec `status="Disabled"`, puis effectuez une mise à jour :

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment :
+ [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
+ [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md)