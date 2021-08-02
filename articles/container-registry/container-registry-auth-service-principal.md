---
title: S’authentifier avec un principal de service
description: Fournir un accès aux images de votre registre de conteneurs privé à l’aide d’un principal du service Azure Active Directory.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 7d64f63de3227394d1f69b2049f0a58dda35e6e6
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111440715"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Authentification Azure Container Registry avec des principaux de service

Vous pouvez utiliser un principal de service Azure Active Directory (Azure AD) pour fournir le tirage, l’envoi ou un autre accès à votre registre de conteneurs. En utilisant un principal de service, vous pouvez fournir l’accès aux applications et services « sans affichage ».

## <a name="what-is-a-service-principal"></a>Qu’est-ce qu’un principal de service ?

Les *principaux de service* Azure AD donnent accès aux ressources Azure dans votre abonnement. Vous pouvez considérer un principal de service comme l’identité d’un utilisateur pour un service, le « service » étant n’importe quelle application, n’importe quel service ou n’importe quelle plateforme qui a besoin d’accéder aux ressources. Vous pouvez configurer un principal de service avec des droits d’accès limités aux ressources que vous spécifiez. Configurez ensuite votre application ou votre service pour qu’il utilise les informations d’identification du principal du service et accède à ces ressources.

Dans le contexte d’Azure Container Registry, vous pouvez créer un principal du service Azure AD avec des autorisations d’extraction (pull), d’envoi (push) et d’extraction (pull), ou d’autres autorisations pour votre registre privé dans Azure. Pour obtenir la liste complète, consultez [Autorisations et rôles Azure Container Registry](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Pourquoi utiliser un principal de service ?

En utilisant un principal de service Azure AD, vous pouvez fournir un accès délimité à votre registre de conteneurs privé. Créez différents principaux de service pour chacune de vos applications ou chacun de vos services, en personnalisant les droits d’accès à votre registre. De plus, étant donné que vous pouvez éviter le partage des informations d’identification entre les services et applications, vous pouvez faire tourner les informations d’identification ou révoquer l’accès uniquement pour le principal de service (et donc l’application) de votre choix.

Par exemple, configurez votre application web pour utiliser un principal de service qui lui fournit l’accès `pull` uniquement, alors que votre système de génération utilise un principal de service qui lui fournit à la fois l’accès `push` et l’accès `pull`. Si le développement de votre application est confié à quelqu’un d’autre, vous pouvez modifier les informations d’identification du principal de service sans affecter le système de génération.

## <a name="when-to-use-a-service-principal"></a>Quand utiliser un principal de service

Vous devez utiliser un principal de service pour fournir l’accès au registre dans les **scénarios sans affichage**. En d’autres termes, cela concerne toutes les applications, tous les services et tous les scripts qui doivent envoyer ou extraire des images conteneur de manière automatisée ou sans assistance. Par exemple :

  * *Pull* : Déployer des conteneurs d’un registre à des systèmes d’orchestration, y compris Kubernetes, DC/OS et Docker Swarm. Vous pouvez également procéder à des extractions des registres de conteneurs vers des services Azure connexes, par exemple [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml) et [Service Fabric](../service-fabric/index.yml).

    > [!TIP]
    > Un principal de service est recommandé dans plusieurs [scénarios Kubernetes](authenticate-kubernetes-options.md) pour extraire des images à partir d’un registre de conteneurs Azure. Avec Azure Kubernetes Service (AKS), vous pouvez également utiliser un mécanisme automatisé pour vous authentifier auprès d’un registre cible en activant [l’identité managée](../aks/cluster-container-registry-integration.md) du cluster. 
  * *Push* : Générer des images conteneur et les envoyer (push) à un registre en utilisant des solutions d’intégration et de livraison continues, comme Azure Pipelines ou Jenkins.

Pour un accès individuel à un registre, par exemple quand vous extrayez manuellement une image conteneur vers votre station de travail de développement, nous vous recommandons d'utiliser votre propre [identité Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) pour accéder au registre (par exemple avec [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Exemples de scripts

Vous trouverez les exemples de scripts précédents pour Azure CLI sur GitHub, ainsi que les versions pour Azure PowerShell :

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>S’authentifier avec le principal de service

Une fois que vous disposez d’un principal de service auquel vous avez accordé l’accès à votre registre de conteneurs, vous pouvez configurer ses informations d’identification pour accéder aux services et applications sans périphérique de contrôle ou les entrer à l'aide de la commande `docker login`. Utilisez les valeurs suivantes :

* **Nom d'utilisateur** - ID d’application du principal de service (également appelé *ID client*)
* **Mot de passe** - mot de passe du principal de service (également appelé *clé secrète client*)

Chaque valeur est au format `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Vous pouvez régénérer le mot de passe d’un principal du service en exécutant la commande [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset).
>

### <a name="use-credentials-with-azure-services"></a>Utiliser les informations d’identification avec les services Azure

Vous pouvez utiliser les informations d’identification du principal de service à partir de n’importe quel service Azure qui s’authentifie auprès d’un registre de conteneurs Azure.  Utilisez les informations d’identification du principal de service à la place des informations d’identification d’administrateur du Registre pour un large éventail de scénarios.

Par exemple, utilisez les informations d’identification pour extraire une image d’un registre de conteneurs Azure vers [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Utiliser avec la connexion Docker

Vous pouvez exécuter `docker login` à l’aide d’un principal de service. Dans l’exemple suivant, l’ID d’application du principal de service est transmis dans la variable d'environnement `$SP_APP_ID`, et le mot de passe dans la variable `$SP_PASSWD`. Pour connaître les pratiques recommandées de gestion des informations d’identification Docker, consultez la référence de la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Une fois connecté, Docker met en cache les informations d’identification.

### <a name="use-with-certificate"></a>Utiliser avec un certificat

Si vous avez ajouté un certificat à votre principal de service, vous pouvez vous connecter à l’interface de ligne de commande Azure à l’aide de l’authentification basée sur un certificat, puis utiliser la commande [az acr login][az-acr-login] pour accéder à un registre. L’utilisation d’un certificat comme clé secrète au lieu d’un mot de passe fournit une sécurité supplémentaire lorsque vous utilisez la CLI. 

Un certificat auto-signé peut être créé lorsque vous [créez un principal de service](/cli/azure/create-an-azure-service-principal-azure-cli). Vous pouvez également ajouter un ou plusieurs certificats à un principal de service existant. Par exemple, si vous utilisez l’un des scripts de cet article pour créer ou mettre à jour un principal de service ayant des droits d’extraction ou de transmission d’images à partir d’un registre, ajoutez un certificat à l’aide de la commande [az ad sp credential reset][az-ad-sp-credential-reset].

Pour utiliser le principal du service avec un certificat pour [se connecter à Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), le certificat doit être au format PEM et inclure la clé privée. Si votre certificat n’est pas au format requis, utilisez un outil tel que `openssl` pour le convertir. Lorsque vous exécutez [az login][az-login] pour vous connecter à l’interface CLI à l’aide du principal de service, fournissez également l’ID d’application du principal de service et l’ID de locataire Active Directory. L’exemple suivant illustre ces valeurs en tant que variables d’environnement :

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Ensuite, exécutez [az acr login][az-acr-login] pour vous authentifier auprès du registre :

```azurecli
az acr login --name myregistry
```

L’interface CLI utilise le jeton créé lorsque vous avez exécuté `az login` pour authentifier votre session auprès du registre.

## <a name="create-service-principal-for-cross-tenant-scenarios"></a>Création d’un principal de service dans les scénarios inter-locataires

Un principal de service peut également être utilisé dans les scénarios Azure qui impliquent d’extraire des images d’un registre de conteneurs situé dans une instance Azure Active Directory (locataire) vers un service ou une application situé dans une autre. Supposons qu’une organisation exécute une application dans le locataire A qui doit extraire une image d’un registre de conteneurs partagé dans le locataire B.

Pour créer un principal de service capable de s’authentifier auprès d’un registre de conteneurs dans un scénario inter-locataire, elle doit procéder comme suit :

*  Créer une [application multi-locataire](../active-directory/develop/single-and-multi-tenant-apps.md) (principal de service) dans le locataire A 
* Provisionner l’application dans le locataire B
* Autoriser le principal de service à effectuer des extractions à partir du registre dans le locataire B
* Mettre à jour le service ou l’application dans le locataire A pour que l’authentification s’effectue avec le nouveau principal de service

Pour voir un exemple de cette procédure, consultez [Extraction d’images d’un registre de conteneurs Azure vers un cluster AKS situé dans un autre locataire AD](authenticate-aks-cross-tenant.md).

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [Vue d’ensemble de l’authentification](container-registry-authentication.md) pour d’autres scénarios d'authentification avec un registre de conteneurs Azure.

* Pour un exemple d’utilisation d'Azure Key Vault à des fins de stockage et de récupération des informations d’identification d’un principal de service pour un registre de conteneur, consultez le tutoriel sur la [création et le déploiement d’une image conteneur à l’aide d'ACR Tasks](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-login]: /cli/azure/reference-index#az_login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
