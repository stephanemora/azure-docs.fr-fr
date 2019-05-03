---
title: Sécuriser des services web avec SSL
titleSuffix: Azure Machine Learning service
description: Découvrez comment sécuriser un service web déployé avec le service Azure Machine Learning en activant HTTPS. HTTPS sécurise les données soumises par les clients à l’aide de la sécurité de la couche transport (TLS), un remplacement pour les couches de socket sécurisé (SSL). Il est également utilisé par les clients pour vérifier l’identité du service web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 50e42172af6ca6b966f9f60d3e037f9ae3dc5cbe
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023776"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Utiliser SSL pour sécuriser des services web avec Azure Machine Learning service

Dans cet article, vous allez découvrir comment sécuriser un service web déployé avec le service Azure Machine Learning. Vous pouvez restreindre l’accès aux services web et sécuriser les données soumises par les clients à l’aide de [protocole sécurisé HTTPS (Hypertext Transfer)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS est utilisé pour sécuriser les communications entre un client et votre service web en chiffrant les communications entre les deux. Le chiffrement est géré à l’aide de [sécurité TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS est toujours parfois en tant que couche SSL (Secure Sockets), ce qui correspondait au prédécesseur TLS.

> [!TIP]
> Le Kit de développement logiciel Azure Machine Learning utilise le terme « SSL » pour les propriétés associée à l’activation des communications sécurisées. Cela ne signifie pas que TLS n’est pas utilisé par votre service web, simplement que SSL est le terme encore plus reconnaissable pour de nombreux lecteurs.

TLS et SSL, les deux s’appuient sur __certificats numériques__, qui sont utilisées pour effectuer la vérification de chiffrement et d’identité. Pour plus d’informations sur leur travail de certificats, consultez la page Wikipedia sur [infrastructure à clé publique (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Si vous ne pas activer et utiliser le protocole HTTPS pour votre service web, les données envoyées vers et depuis le service peuvent être visibles à d’autres utilisateurs sur internet.
>
> HTTPS permet également au client de vérifier l’authenticité du serveur auquel il se connecte. Cela protège les clients contre [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) les attaques.

Le processus général de la sécurisation d’un service web nouveau ou existant est comme suit :

1. Obtenir un nom de domaine.

2. Obtenir un certificat numérique.

3. Déployer ou mettre à jour le service web avec le paramètre SSL activé.

4. Mettre à jour votre DNS afin qu’il pointe vers le service web.

> [!IMPORTANT]
> Si vous déployez Azure Kubernetes Service (AKS), vous pouvez fournir votre propre certificat ou utiliser un certificat fourni par Microsoft. Si vous utilisez le certificat de fournie par Microsoft, il est inutile obtenir un nom de domaine ou d’un certificat SSL. Pour plus d’informations, consultez le [activer SSL et de déployer](#enable) section.

La sécurisation des services web sur les [cibles de déploiement](how-to-deploy-and-where.md) présente de légères différences.

## <a name="get-a-domain-name"></a>Obtenir un nom de domaine

Si vous ne possédez pas déjà un nom de domaine, vous pouvez en acheter un auprès d’un __bureau d’enregistrement de noms de domaine__. La procédure et le coût varient selon les bureaux d’enregistrement. Le bureau d’enregistrement fournit également des outils qui vous permettent de gérer le nom de domaine. Ces outils sont utilisés pour mapper un nom de domaine complet (tel que www\.contoso.com) à l’adresse IP qui héberge votre service web.

## <a name="get-an-ssl-certificate"></a>Obtenir un certificat SSL

Il existe de nombreuses façons pour obtenir un certificat SSL (numérique). Le moyen le plus courant consiste à en acheter un auprès d’une __autorité de certification__ (CA). Quel que soit l’endroit où vous obtenez le certificat, vous avez besoin des fichiers suivants :

* Un __certificat__. Le certificat doit contenir la chaîne d’approbation et doit être codé en PEM.
* Une __clé__. La clé doit être codée en PEM.

Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet (FQDN) de l’adresse que vous envisagez d’utiliser pour le service web. Par exemple, www\.contoso.com. L’adresse estampillée sur le certificat et l’adresse utilisée par les clients sont comparées pour valider l’identité du service web. Si les adresses ne correspondent pas, les clients reçoivent une erreur.

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!WARNING]
> Les certificats auto-signés doivent être utilisés uniquement pour le développement. Ils ne doivent pas être utilisés en production. Les certificats autosignés peuvent entraîner des problèmes dans vos applications clientes. Pour plus d’informations, consultez la documentation relative aux bibliothèques réseau utilisées dans votre application cliente.

## <a id="enable"></a> Activer le protocole SSL et déployer

Pour déployer ou redéployer le service avec SSL activé, définissez la `ssl_enabled` paramètre `True`, autant que possible. Définissez le paramètre `ssl_certificate` sur la valeur du fichier __certificate__ et le paramètre `ssl_key` sur la valeur du fichier __key__.

+ **Visual interface - créer sécurisé Azure Kubernetes Service (AKS) pour le déploiement** 
    
    Désignez-le si vous essayez de créer le calcul de déploiement sécurisé pour l’interface visuelle. Lors de la configuration du cluster AKS, fournir des valeurs pour les paramètres liés à SSL, puis créez un nouveau AKS.  Consultez ci-dessous extrait de code :
    

    > [!TIP]
    >  Si vous n’êtes pas familiarisé avec le kit SDK Python, démarrez à partir [le présentation du kit SDK Python Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Provide SSL-related parameters when provisioning the AKS cluster
    prov_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")   
 
    aks_name = 'secure-aks'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws,
                                        name = aks_name,
                                        provisioning_configuration = prov_config)
    
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```
    
   

+ **Déployer sur Azure Kubernetes Service (AKS) et FPGA**

  Lorsque vous déployez sur AKS, vous pouvez créer un cluster AKS ou joindre un. Création d’un nouveau cluster utilise [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) tandis que l’attachement d’un cluster existant utilise [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Les deux renvoient un objet de configuration qui a un `enable_ssl` (méthode).

  Le `enable_ssl` méthode peut utiliser soit un certificat fourni par Microsoft, ou celui que vous fournissez.

  * Lorsque vous utilisez un certificat __fournis par Microsoft__, vous devez utiliser le `leaf_domain_label` paramètre. À l’aide de ce paramètre crée le service à l’aide d’un certificat fourni par Microsoft. Le `leaf_domain_label` est utilisé pour générer le nom DNS pour le service. Par exemple, une valeur de `myservice` crée le nom de domaine `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, où `<azureregion>` correspond à la région qui contient le service. Si vous le souhaitez, vous pouvez utiliser le `overwrite_existing_domain` paramètre pour remplacer l’étiquette de domaine feuille existante.

    Pour déployer (ou redéployer) le service avec le protocole SSL activé, définissez le paramètre `ssl_enabled` sur `True`, si nécessaire. Définissez le paramètre `ssl_certificate` sur la valeur du fichier __certificate__ et le paramètre `ssl_key` sur la valeur du fichier __key__.

    > [!IMPORTANT]
    > Lorsque vous utilisez un certificat fourni par Microsoft, vous n’avez pas besoin d’acheter votre propre certificat ou nom de domaine.

    L’exemple suivant montre comment créer des configurations qui permettent un certificat SSL créé par Microsoft :

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name).enable_ssl(leaf_domain_label = "myservice")
    ```

  * Lorsque vous utilisez __un certificat que vous avez achetées__, utilisez le `ssl_cert_pem_file`, `ssl_key_pem_file`, et `ssl_cname` paramètres.  L’exemple suivant montre comment créer des configurations qui utilisent un certificat SSL que vous fournissez à l’aide de `.pem` fichiers :

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name).enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Pour plus d’informations sur `enable_ssl`, consultez [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) et [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Déployer sur Azure Container Instances (ACI)**

  Lors du déploiement sur ACI, indiquez les valeurs des paramètres SSL comme indiqué dans l’extrait de code :

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Pour plus d’informations, consultez [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Mettre à jour votre DNS

Vous devez ensuite mettre à jour votre DNS afin qu’il pointe vers le service web.

+ **Pour ACI** :

  Utilisez les outils fournis par votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.

  Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.

+ **Pour AKS** :

  > [!WARNING]
  > Si vous avez utilisé le `leaf_domain_label` pour créer le service avec un certificat fourni par Microsoft, ne modifiez pas manuellement la valeur DNS pour le cluster. La valeur doit être définie automatiquement.

  Mettez à jour le DNS sous l’onglet « Configuration » de l’« adresse IP publique » du cluster AKS, comme illustré dans l’image. Vous pouvez trouver l’adresse IP publique comme l’un des types de ressources créés sous le groupe de ressources qui contient les nœuds d’agent AKS et d’autres ressources de mise en réseau.

  ![Azure Machine Learning service : Sécurisation des services web avec SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)



## <a name="next-steps"></a>Étapes suivantes
Découvrez comment :
+ [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
+ [Exécuter en toute sécurité des expériences et des inférences à l'intérieur d'un réseau virtuel Azure](how-to-enable-virtual-network.md)

