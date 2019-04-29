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
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818536"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Utiliser SSL pour sécuriser des services web avec Azure Machine Learning service

Dans cet article, vous allez découvrir comment sécuriser un service web déployé avec le service Azure Machine Learning. Vous pouvez restreindre l’accès aux services web et sécuriser les données soumises par les clients à l’aide de [protocole sécurisé HTTPS (Hypertext Transfer)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS est utilisé pour sécuriser les communications entre un client et votre service web en chiffrant les communications entre les deux. Le chiffrement est géré à l’aide de [sécurité TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Il est toujours parfois en tant que couche SSL (Secure Sockets), ce qui correspondait au prédécesseur TLS.

> [!TIP]
> Le Kit de développement logiciel Azure Machine Learning utilise le terme « SSL » pour les propriétés associée à l’activation des communications sécurisées. Cela ne signifie pas que TLS n’est pas utilisé par votre service web, simplement que SSL est le terme encore plus reconnaissable pour de nombreux lecteurs.

TLS et SSL, les deux s’appuient sur __certificats numériques__, qui sont utilisées pour effectuer la vérification de chiffrement et d’identité. Pour plus d’informations sur leur travail de certificats, consultez la page Wikipedia sur [infrastructure à clé publique (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Si vous ne pas activer et utiliser le protocole HTTPS pour votre service web, les données envoyées vers et depuis le service peuvent être visibles à d’autres utilisateurs sur internet.
>
> HTTPS permet également au client de vérifier l’authenticité du serveur auquel il se connecte. Cela protège les clients contre [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) les attaques.

Le processus de sécurisation d’un service web nouveau ou existant est comme suit :

1. Obtenir un nom de domaine.

2. Obtenir un certificat numérique.

3. Déployer ou mettre à jour le service web avec le paramètre SSL activé.

4. Mettre à jour votre DNS afin qu’il pointe vers le service web.

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

  Lors du déploiement sur FPGA, indiquez les valeurs des paramètres SSL comme indiqué dans l’extrait de code :

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>Mettre à jour votre DNS

Vous devez ensuite mettre à jour votre DNS afin qu’il pointe vers le service web.

+ **Pour ACI** :

  Utilisez les outils fournis par votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.

  Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.

+ **Pour AKS** :

  Mettez à jour le DNS sous l’onglet « Configuration » de l’« adresse IP publique » du cluster AKS, comme illustré dans l’image. Vous pouvez trouver l’adresse IP publique comme l’un des types de ressources créés sous le groupe de ressources qui contient les nœuds d’agent AKS et d’autres ressources de mise en réseau.

  ![Azure Machine Learning service : Sécurisation des services web avec SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment :
+ [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
+ [Exécuter en toute sécurité des expériences et des inférences à l'intérieur d'un réseau virtuel Azure](how-to-enable-virtual-network.md)
