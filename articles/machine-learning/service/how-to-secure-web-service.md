---
title: Sécuriser des services web à l’aide de SSL
titleSuffix: Azure Machine Learning service
description: Découvrez comment sécuriser un service web qui est déployé via le service Azure Machine Learning en activant HTTPS. HTTPS sécurise les données à partir de par les clients à l’aide du protocole TLS (TLS), un remplacement pour les couches de socket sécurisé (SSL). Les clients utilisent également HTTPS pour vérifier l’identité du service web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393825"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Utilisez SSL pour sécuriser un service web via Azure Machine Learning

Cet article vous montre comment sécuriser un service web qui est déployé via le service Azure Machine Learning.

Vous utilisez [HTTPS](https://en.wikipedia.org/wiki/HTTPS) pour restreindre l’accès aux services web et de sécuriser les données soumises par les clients. HTTPS permet de sécuriser les communications entre un client et un service web en chiffrant les communications entre les deux. Le chiffrement utilise [sécurité TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS est toujours parfois appelé *protocole SSL (Secure Sockets Layer)* (SSL), ce qui était le prédécesseur de TLS.

> [!TIP]
> Le Kit de développement logiciel Azure Machine Learning utilise le terme « SSL » pour les propriétés qui sont liées à des communications sécurisées. Cela ne signifie pas que votre service web n’utilise pas *TLS*. SSL est simplement un terme plus communément reconnu.

TLS et SSL, les deux s’appuient sur *certificats numériques*, qui vous aider à la vérification de chiffrement et d’identité. Pour plus d’informations sur leur travail de certificats, consultez la rubrique Wikipédia [infrastructure à clé publique](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Si vous n’utilisez pas HTTPS pour votre service web, les données envoyées vers et depuis le service peuvent être visibles à d’autres personnes sur internet.
>
> HTTPS permet également au client de vérifier l’authenticité du serveur auquel il se connecte. Cette fonctionnalité protège les clients contre [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) les attaques.

Voici le processus général pour sécuriser un service web :

1. Obtenir un nom de domaine.

2. Obtenir un certificat numérique.

3. Déployer ou mettre à jour le service web avec SSL activé.

4. Mettre à jour votre DNS afin qu’il pointe vers le service web.

> [!IMPORTANT]
> Si vous effectuez un déploiement vers Azure Kubernetes Service (AKS), vous pouvez acheter votre propre certificat ou utiliser un certificat qui est fourni par Microsoft. Si vous utilisez un certificat à partir de Microsoft, vous n’avez pas besoin obtenir un nom de domaine ou d’un certificat SSL. Pour plus d’informations, consultez le [activer SSL et de déployer](#enable) section de cet article.

Il existe de légères différences lorsque vous sécurisez des services web entre [cibles de déploiement](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obtenir un nom de domaine

Si vous ne possédez pas encore un nom de domaine, en acheter un à partir d’un *bureau d’enregistrement de nom de domaine*. Le processus et les prix diffèrent des bureaux d’enregistrement. Le bureau d’enregistrement fournit des outils pour gérer le nom de domaine. Ces outils vous permettent de mapper un nom de domaine complet (FQDN) (par exemple www\.contoso.com) à l’adresse IP qui héberge votre service web.

## <a name="get-an-ssl-certificate"></a>Obtenir un certificat SSL

Il existe de nombreuses façons pour obtenir un certificat SSL (numérique). La plus courante est d’acheter l’un à partir d’un *autorité de certification* (CA). Quel que soit l’endroit où vous obtenez le certificat, vous devez les fichiers suivants :

* Un **certificat**. Le certificat doit contenir la chaîne de certificats complète, et il doit être « Encodée PEM. »
* Une **clé**. La clé doit également être codé PEM.

Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet de l’adresse que vous projetez d’utiliser pour le service web (par exemple, www\.contoso.com). L’adresse qui est marqué dans le certificat et l’adresse que les clients utilisent sont comparés pour vérifier l’identité du service web. Si ces adresses ne correspondent pas, le client obtient un message d’erreur.

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM encodé, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!WARNING]
> Utilisez *auto-signés* certificats uniquement pour le développement. Ne les utilisez pas dans les environnements de production. Les certificats autosignés peuvent entraîner des problèmes dans vos applications clientes. Pour plus d’informations, consultez la documentation pour les bibliothèques de réseau qui utilise votre application cliente.

## <a id="enable"></a> Activer le protocole SSL et déployer

Pour déployer ou redéployer le service avec SSL activé, définissez la *ssl_enabled* paramètre sur « True », où qu’elles soient applicable. Définir le *ssl_certificate* paramètre à la valeur de la *certificat* fichier. Définir le *ssl_key* à la valeur de la *clé* fichier.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Déployer sur AKS et le tableau de portes à champ programmable (FPGA)

  > [!NOTE]
  > Les informations contenues dans cette section s’applique également lorsque vous déployez un service web sécurisé pour l’interface visuelle. Si vous n’êtes pas familiarisé avec l’aide du SDK Python, consultez [quel est le Kit de développement Azure Machine Learning pour Python ?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Lorsque vous déployez sur AKS, vous pouvez créer un cluster AKS ou joindre un.
  
-  Si vous créez un nouveau cluster, vous utilisez  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** .
- Si vous attachez un cluster existant, vous utilisez  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** . Les deux renvoient un objet de configuration qui a un **enable_ssl** (méthode).

Le **enable_ssl** méthode peut utiliser un certificat qui est fourni par Microsoft ou un certificat que vous achetez.

  * Lorsque vous utilisez un certificat à partir de Microsoft, vous devez utiliser le *leaf_domain_label* paramètre. Ce paramètre génère le nom DNS pour le service. Par exemple, une valeur de « myservice » crée le nom de domaine « myservice\<caractères aléatoires six >.\< azureregion >. cloudapp.azure.com », où \<azureregion > correspond à la région qui contient le service. Si vous le souhaitez, vous pouvez utiliser la *overwrite_existing_domain* paramètre pour remplacer l’existant *leaf_domain_label*.

    Pour déployer ou redéployer le service avec SSL activé, définissez la *ssl_enabled* paramètre sur « True », où qu’elles soient applicable. Définir le *ssl_certificate* paramètre à la valeur de la *certificat* fichier. Définir le *ssl_key* à la valeur de la *clé* fichier.

    > [!IMPORTANT]
    > Lorsque vous utilisez un certificat à partir de Microsoft, vous n’avez pas besoin d’acheter votre propre certificat ou nom de domaine.

    L’exemple suivant montre comment créer une configuration qui permet à un certificat SSL à partir de Microsoft :

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Lorsque vous utilisez *un certificat que vous avez acheté*, vous utilisez le *ssl_cert_pem_file*, *ssl_key_pem_file*, et *ssl_cname* paramètres. L’exemple suivant montre comment utiliser *.pem* fichiers pour créer une configuration qui utilise un certificat SSL que vous avez acheté :

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Pour plus d’informations sur *enable_ssl*, consultez [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) et [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Déployer sur Azure Container Instances

Lorsque vous déployez sur Azure Container Instances, vous fournissez des valeurs pour les paramètres SSL, comme l’extrait de code suivant :

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Pour plus d’informations, consultez [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Mettre à jour votre DNS

Vous devez ensuite mettre à jour votre DNS afin qu’il pointe vers le service web.

+ **Pour les Instances de conteneur :**

  Utilisez les outils à partir de votre bureau d’enregistrement du nom de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.

  Il peut y avoir un délai de quelques minutes ou heures avant que les clients peuvent résoudre le nom de domaine, en fonction du bureau d’enregistrement et la « durée de vie » (TTL) qui est configuré pour le nom de domaine.

+ **Pour AKS :**

  > [!WARNING]
  > Si vous avez utilisé *leaf_domain_label* pour créer le service à l’aide d’un certificat à partir de Microsoft, ne pas mettre à jour manuellement la valeur DNS pour le cluster. La valeur doit être définie automatiquement.

  Mettre à jour le DNS sur le **Configuration** onglet de l’adresse IP publique du cluster AKS. (Voir l’image suivante.) L’adresse IP publique est un type de ressource qui est créé sous le groupe de ressources qui contient les nœuds d’agent AKS et d’autres ressources réseau.

  ![Azure Machine Learning service : Sécurisation des services web avec SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment :
+ [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
+ [Exécuter en toute sécurité des expériences et l’inférence à l’intérieur d’un réseau virtuel Azure](how-to-enable-virtual-network.md)
