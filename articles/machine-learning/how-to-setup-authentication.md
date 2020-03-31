---
title: Configurer l’authentification
titleSuffix: Azure Machine Learning
description: Découvrez comment installer et configurer l’authentification pour divers workflows et ressources dans Azure Machine Learning. Il existe plusieurs façons de configurer et d’utiliser l’authentification sur le service, qu’il s’agisse de l’authentification simple basée sur l’interface utilisateur pour le développement ou les tests, ou de l’authentification complète du principal de service dans Azure Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79237025"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurer l’authentification pour des ressources et workflows Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous découvrez comment installer et configurer l’authentification pour divers workflows et ressources dans Azure Machine Learning. Il existe plusieurs méthodes d’authentification sur le service, qu’il s’agisse de l’authentification simple basée sur l’interface utilisateur pour le développement ou les tests, ou de l’authentification complète du principal de service dans Azure Active Directory. Cet article décrit également les différences de fonctionnement de l’authentification auprès d’un service web et explique comment s’authentifier auprès de l’API REST Azure Machine Learning.

Ce guide pratique vous montre comment effectuer les tâches suivantes :

* Utiliser l’authentification interactive basée sur l’interface utilisateur pour le développement ou les tests
* Configurer l’authentification du principal de service
* Vous authentifier auprès de votre espace de travail
* Obtenir des jetons de type porteur OAuth 2.0 pour l’API REST Azure Machine Learning
* Comprendre le fonctionnement de l’authentification auprès d’un service web

Pour avoir une vue d’ensemble de la sécurité et de l’authentification dans Azure Machine Learning, consultez l’[article de présentation des concepts](concept-enterprise-security.md).

## <a name="prerequisites"></a>Conditions préalables requises

* Créez un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).
* [Configurez votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning ou utilisez une [machine virtuelle de notebooks Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) avec le SDK déjà installé.

## <a name="interactive-authentication"></a>Authentification interactive

La plupart des exemples donnés dans la documentation de ce service utilisent l’authentification interactive dans les notebooks Jupyter, car c’est une méthode simple pour les tests et les démonstrations. Cette méthode légère vous permet de tester facilement ce que vous développez. Elle utilise deux appels de fonction qui vous invitent automatiquement à utiliser un flux d’authentification basée sur l’interface utilisateur.

L’appel de la fonction `from_config()` génère l’invite.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

La fonction `from_config()` recherche un fichier JSON contenant les informations de connexion à votre espace de travail. Vous pouvez aussi spécifier les détails de la connexion de manière explicite avec le constructeur `Workspace`, qui génère également une invite d’authentification interactive. Les deux appels sont équivalents.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Si vous avez accès à plusieurs locataires, vous devrez peut-être importer la classe et définir explicitement le locataire ciblé. L’appel du constructeur pour `InteractiveLoginAuthentication` génère également une invite de connexion comme avec les appels montrés ci-dessus.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Bien qu’elle soit utile pour le test et l’apprentissage, l’authentification interactive ne vous permet pas de créer facilement des workflows automatisés ou sans affichage (headless). Configurer l’authentification du principal de service est la meilleure approche pour les processus automatisés qui utilisent le SDK.

## <a name="set-up-service-principal-authentication"></a>Configurer l’authentification du principal de service

Ce processus est requis pour effectuer l’authentification de manière dissociée d’une connexion utilisateur spécifique et permettre ainsi une authentification auprès du SDK Azure Machine Learning pour Python dans les workflows automatisés. L’authentification du principal de service rend également possible l’[authentification auprès de l’API REST](#azure-machine-learning-rest-api-auth).

Pour configurer l’authentification du principal de service, vous créez d’abord une inscription d’application dans Azure Active Directory, puis vous accordez à l’application un accès en fonction du rôle à votre espace de travail ML. Le moyen le plus simple d’effectuer cette configuration est d’utiliser [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) dans le portail Azure. Une fois que vous êtes connecté au portail, cliquez sur l’icône `>_` en haut à droite de la page, à côté de votre nom, pour ouvrir l’interpréteur de commandes.

Si vous n’avez encore jamais utilisé Cloud Shell dans votre compte Azure, vous devez créer une ressource de compte de stockage pour y stocker les fichiers créés. En général, ce compte de stockage entraîne un coût mensuel négligeable. De plus, si vous ne l’avez pas déjà utilisée préalablement, installez l’extension Machine Learning à l’aide de cette commande.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Vous devez être administrateur de l’abonnement pour pouvoir effectuer les étapes ci-dessous.

Ensuite, exécutez la commande suivante pour créer le principal de service. Donnez-lui un nom, comme **ml-auth** ici.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Vous devez obtenir une sortie JSON semblable à ce qui suit. Notez les valeurs des champs `clientId`, `clientSecret`et `tenantId`, car vous en aurez besoin pour certaines étapes ultérieures dans cet article.

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

Exécutez aussi la commande suivante pour obtenir les détails du principal de service que vous venez de créer, en utilisant la valeur `clientId` notée ci-dessus comme entrée du paramètre `--id`.

```azurecli-interactive
az ad sp show --id your-client-id
```

Le code ci-dessous est un exemple simplifié de sortie JSON générée par la commande. Notez la valeur du champ `objectId`, car vous devrez l’indiquer à la prochaine étape.

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

Ensuite, utilisez la commande suivante pour accorder au principal de service l’accès à votre espace de travail Machine Learning. Définissez les paramètres `-w` et `-g` respectivement avec le nom de votre espace de travail et le nom du groupe de ressources associé. Pour le paramètre `--user`, utilisez la valeur `objectId` notée à l’étape précédente. Le paramètre `--role` vous permet d’attribuer au principal de service un rôle d’accès, **propriétaire** ou **contributeur**, en général. Ces deux rôles ont un accès en écriture aux ressources existantes comme les clusters de calcul et les magasins de données ; en revanche, seul le rôle **propriétaire** est autorisé à provisionner ces ressources. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Cet appel ne génère pas de sortie, mais vous avez bien configuré l’authentification du principal de service pour votre espace de travail.

## <a name="authenticate-to-your-workspace"></a>Authentification auprès de votre espace de travail

Maintenant que l’authentification du principal de service est activée, vous pouvez vous authentifier auprès de votre espace de travail dans le SDK sans avoir à vous connecter physiquement en tant qu’utilisateur. Pour cela, utilisez le constructeur de classe `ServicePrincipalAuthentication` et définissez les paramètres aux valeurs que vous avez obtenues aux étapes précédentes. Les paramètres `tenant_id`, `tenantId` et `service_principal_id` sont respectivement mappés aux valeurs `clientId`, `service_principal_password`et `clientSecret` notées plus haut.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

La variable `sp` contient maintenant un objet d’authentification que vous utilisez directement dans le SDK. En règle générale, il est conseillé de stocker les ID/secrets utilisés ci-dessus dans des variables d’environnement, comme dans ce code.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Pour les workflows automatisés qui s’exécutent dans Python et utilisent principalement le SDK, vous pouvez utiliser cet objet tel quel dans la plupart des cas d’authentification. Le code suivant effectue l’authentification auprès de votre espace de travail à l’aide de l’objet d’authentification que vous venez de créer.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Authentification auprès de l’API REST Azure Machine Learning

Le principal de service créé aux étapes précédentes permet également de s’authentifier auprès de l’[’API REST](https://docs.microsoft.com/rest/api/azureml/) Azure Machine Learning. Vous utilisez le [flux d’octroi des informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Azure Active Directory, qui permet d’effectuer des appels de service à service pour l’authentification sans affichage dans les workflows automatisés. Les exemples sont implémentés avec la [bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) en Python et en Node.js, mais vous pouvez aussi utiliser n’importe quelle bibliothèque open source prenant en charge OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js est une bibliothèque plus récente qu’ADAL. Toutefois, MSAL.js ne prend pas en charge l’authentification de service à service avec les informations d’identification du client, car il s’agit principalement d’une bibliothèque côté client conçue pour l’authentification interactive/basée sur l’interface utilisateur qui est associée à un utilisateur spécifique. Nous vous recommandons d’utiliser ADAL comme indiqué ci-dessous pour créer des workflows automatisés avec l’API REST.

### <a name="nodejs"></a>Node.js

Effectuez les étapes suivantes pour générer un jeton d’authentification avec Node.js. Dans votre environnement, exécutez `npm install adal-node`. Ensuite, utilisez les valeurs de `tenantId`, `clientId` et `clientSecret` du principal de service que vous avez créé précédemment comme valeurs des variables correspondantes dans ce script.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

La variable `tokenResponse` est un objet qui contient le jeton et des métadonnées associées, comme le délai d’expiration. Les jetons restent valides pendant 1 heure, après quoi vous devez les actualiser en réexécutant le même appel pour récupérer un nouveau jeton. Voici un exemple de réponse.

```javascript
{ 
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id' 
}
```

Utilisez la propriété `accessToken` pour récupérer (fetch) le jeton d’authentification. Consultez la [documentation de l’API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) si vous souhaitez voir des exemples d’utilisation du jeton pour effectuer des appels d’API.

### <a name="python"></a>Python 

Effectuez les étapes suivantes pour générer un jeton d’authentification avec Python. Dans votre environnement, exécutez `pip install adal`. Ensuite, utilisez les valeurs de `tenantId`, `clientId` et `clientSecret` du principal de service que vous avez créé précédemment comme valeurs des variables correspondantes dans ce script.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

La variable `token_response` est un dictionnaire qui contient le jeton et des métadonnées associées, comme le délai d’expiration. Les jetons restent valides pendant 1 heure, après quoi vous devez les actualiser en réexécutant le même appel pour récupérer un nouveau jeton. Voici un exemple de réponse.

```python
{
    'tokenType': 'Bearer', 
    'expiresIn': 3599, 
    'expiresOn': '2019-12-17 19:47:15.150205', 
    'resource': 'https://management.azure.com/', 
    'accessToken': 'random-oauth-token', 
    'isMRRT': True, 
    '_clientId': 'your-client-id', 
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Utilisez `token_response["accessToken"]` pour récupérer (fetch) le jeton d’authentification. Consultez la [documentation de l’API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) si vous souhaitez voir des exemples d’utilisation du jeton pour effectuer des appels d’API.

## <a name="web-service-authentication"></a>Authentification auprès d’un service web

Les services web dans Azure Machine Learning utilisent un modèle d’authentification différent de ce que nous avons vu jusqu’ici. La méthode la plus simple pour s’authentifier auprès de services web déployés est l’**authentification basée sur les clés**, car cette méthode génère des clés d’authentification statiques de type porteur qui n’ont pas besoin d’être actualisées. Si vous devez uniquement vous authentifier auprès d’un service web déployé, il n’est pas utile de configurer l’authentification du principal de service, comme expliqué plus haut.

L’authentification basée sur les clés est *activée* par défaut pour les services web déployés sur Azure Kubernetes Service. Elle est *désactivée* par défaut pour les services déployés sur Azure Container Instances, mais vous pouvez l’activer en définissant `auth_enabled=True` au moment où vous créez votre service web ACI. Voici un exemple montrant comment créer une configuration de déploiement sur ACI avec l’authentification basée sur les clés activée.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Vous pouvez ensuite utiliser la configuration ACI personnalisée dans le déploiement avec la classe `Model`.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Pour récupérer les clés d’authentification, utilisez `aci_service.get_keys()`. Pour regénérer une clé, utilisez la fonction `regen_key()` et passez la valeur **Primary** ou **Secondary**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Les services web prennent également en charge l’authentification basée sur les jetons, mais uniquement dans le cadre de déploiements sur Azure Kubernetes Service. Pour plus d’informations sur l’authentification, consultez le [guide pratique](how-to-consume-web-service.md) d’utilisation des services web.

### <a name="token-based-web-service-authentication"></a>Authentification basée sur les jetons auprès d’un service web

Quand vous activez l’authentification par jeton pour un service web, les utilisateurs doivent présenter un jeton JSON Web Token Azure Machine Learning au service web pour y accéder. Le jeton expire après un laps de temps spécifié, après quoi il doit être actualisé pour continuer à effectuer des appels.

* L’authentification par jeton est **désactivée par défaut** lors d’un déploiement sur Azure Kubernetes Service.
* L’authentification par jeton **n’est pas prise en charge** pour les déploiements sur Azure Container Instances.

Pour contrôler l’authentification par jeton, utilisez le paramètre `token_auth_enabled` quand vous créez ou mettez à jour un déploiement.

Si l’authentification par jeton est activée, vous pouvez utiliser la méthode `get_token` pour récupérer un jeton JWT (JSON Web Token) et son délai d’expiration :

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Vous devrez demander un nouveau jeton après l’heure de `refresh_by` du jeton. Si vous devez actualiser des jetons en dehors du SDK pour Python, l’une des options possibles est d’utiliser l’API REST avec l’authentification du principal de service pour effectuer l’appel `service.get_token()` régulièrement, comme indiqué précédemment.
>
> Nous vous recommandons vivement de créer votre espace de travail Azure Machine Learning dans la même région que celle de votre cluster Azure Kubernetes Service. 
>
> Pour s’authentifier avec un jeton, le service web appelle la région dans laquelle votre espace de travail Azure Machine Learning est créé. Si la région de votre espace de travail est indisponible, vous ne pouvez pas extraire de jeton pour votre service web, même si votre cluster se trouve dans une région différente de celle de votre espace de travail. Ainsi, Azure AD Authentication n’est pas disponible tant que la région de votre espace de travail n’est pas à nouveau disponible. 
>
> De plus, plus la distance entre la région de votre cluster et celle de votre espace de travail est élevée, plus l’extraction de jeton prend de temps.

## <a name="next-steps"></a>Étapes suivantes

* [Entraînez et déployez un modèle de classification d’images](tutorial-train-models-with-aml.md).
* [Utilisez un modèle Azure Machine Learning déployé en tant que service web](how-to-consume-web-service.md).
