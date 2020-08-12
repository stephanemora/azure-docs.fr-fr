---
title: Configurer l’authentification
titleSuffix: Azure Machine Learning
description: Découvrez comment installer et configurer l’authentification pour divers workflows et ressources dans Azure Machine Learning. Il existe plusieurs façons de configurer et d’utiliser l’authentification sur le service, qu’il s’agisse de l’authentification simple basée sur l’interface utilisateur pour le développement ou les tests, ou de l’authentification complète du principal de service dans Azure Active Directory.
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-javascript
ms.openlocfilehash: 4061d7a3d21b8c2db2bf161c422994cb2742b0b4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489875"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurer l’authentification pour des ressources et workflows Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment vous authentifier auprès de votre espace de travail Azure Machine Learning et des modèles déployés en tant que services web.

En général, il existe deux types d’authentification que vous pouvez utiliser avec Azure Machine Learning :

* __Interactive :__ vous utilisez votre compte dans Azure Active Directory pour l’authentification directe ou pour obtenir un jeton utilisé pour l’authentification. L’authentification interactive est utilisée pendant l’expérimentation et le développement itératif. Ou lorsque vous souhaitez contrôler l’accès aux ressources (par exemple, un service web) pour chaque utilisateur.
* __Principal du service__ : Vous créez un compte de principal de service dans Azure Active Directory et l’utilisez pour authentifier ou obtenir un jeton. Un principal de service est utilisé lorsque vous avez besoin d’un processus automatisé pour l’authentification auprès du service, sans intervention de l’utilisateur. Par exemple, un script d’intégration et de déploiement continus qui forme et teste un modèle chaque fois que le code d’apprentissage change. Vous pouvez également utiliser un principal de service pour récupérer un jeton à des fins d’authentification auprès d’un service web, si vous ne souhaitez pas que l’utilisateur final du service s’authentifie. Ou lorsque l’authentification de l’utilisateur final n’est pas effectuée directement à l’aide d’Azure Active Directory.

Quel que soit le type d’authentification utilisé, le contrôle d’accès en fonction du rôle (RBAC) permet de définir l’étendue du niveau d’accès autorisé aux ressources. Par exemple, un compte utilisé pour obtenir le jeton d’accès pour un modèle déployé a uniquement besoin d’un accès en lecture à l’espace de travail. Pour plus d'informations sur le contrôle d’accès en fonction du rôle, consultez [Gérer l’accès à un espace de travail Azure Machine Learning](how-to-assign-roles.md).

## <a name="prerequisites"></a>Prérequis

* Créez un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).
* [Configurez votre environnement de développement](how-to-configure-environment.md) pour installer le SDK Azure Machine Learning ou utilisez une [machine virtuelle de notebooks Azure Machine Learning](concept-azure-machine-learning-architecture.md#compute-instance) avec le SDK déjà installé.

## <a name="interactive-authentication"></a>Authentification interactive

La plupart des exemples de la documentation et des exemples utilisent l’authentification interactive. Par exemple, lors de l’utilisation du SDK, deux appels de fonction vous invitent automatiquement à utiliser un flux d’authentification basée sur l’interface utilisateur :

* L’appel de la fonction `from_config()` génère l’invite.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    La fonction `from_config()` recherche un fichier JSON contenant les informations de connexion à votre espace de travail.

* Lorsque vous utilisez le constructeur `Workspace` pour fournir des informations sur l’abonnement, le groupe de ressources et l’espace de travail, vous êtes également invité à effectuer une authentification interactive.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Si vous avez accès à plusieurs locataires, vous devrez peut-être importer la classe et définir explicitement le locataire ciblé. L’appel du constructeur pour `InteractiveLoginAuthentication` génère également une invite de connexion comme avec les appels montrés ci-dessus.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour utiliser l’authentification du principal de service, vous devez d’abord créer le principal de service et lui accorder l’accès à votre espace de travail. Comme mentionné précédemment, le contrôle d’accès en fonction du rôle (Azure RBAC) permet de contrôler l’accès. Vous devez donc également décider de l’accès accordé au principal du service.

> [!IMPORTANT]
> Lorsque vous utilisez un principal de service, accordez-lui l’__accès minimal requis pour la tâche__ pour laquelle il est utilisé. Par exemple, vous ne pouvez pas accorder l’accès propriétaire ou contributeur au principal de service s’il est utilisé uniquement pour la lecture du jeton d’accès pour un déploiement web.
>
> La raison pour laquelle vous accordez l’accès le plus bas est qu’un principal de service utilise un mot de passe pour l’authentification et que le mot de passe peut être stocké dans le cadre d’un script d’automatisation. Si le mot de passe est divulgué, le fait d’avoir un accès minimal requis pour une tâche spécifique réduit au minimum l’utilisation malveillante du principal de service.

Le moyen le plus simple de créer un principal de service et de lui accorder l’accès à votre espace de travail consiste à utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pour créer un principal de service et lui accorder l’accès à votre espace de travail, procédez comme suit :

> [!NOTE]
> Vous devez être administrateur de l’abonnement pour pouvoir effectuer toutes les étapes ci-dessous.

1. Authentifiez-vous auprès de votre abonnement Azure :

    ```azurecli-interactive
    az login
    ```

    Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Dans le cas contraire, vous devez ouvrir un navigateur et suivre les instructions de la ligne de commande. Les instructions impliquent de naviguer vers [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et d’entrer un code d’autorisation.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Pour les autres méthodes d’authentification, consultez [Se connecter avec Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

1. Installez l’extension Azure Machine Learning :

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Créez le principal de service. Dans l’exemple suivant, un principal de service nommé **ml-auth** est créé :

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

1. Récupérez les détails du principal de service à l’aide de la valeur `clientId` retournée à l’étape précédente :

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Le code JSON ci-dessous est un exemple simplifié de sortie générée par la commande. Notez la valeur du champ `objectId`, car vous devrez l’indiquer à la prochaine étape.

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

1. Autorisez le principal de service à accéder à votre espace de travail Azure Machine Learning. Définissez les paramètres `-w` et `-g` respectivement avec le nom de votre espace de travail et le nom du groupe de ressources associé. Pour le paramètre `--user`, utilisez la valeur `objectId` notée à l’étape précédente. Le paramètre `--role` vous permet de définir le rôle d’accès du principal de service. Dans l’exemple suivant, le principal de service est attribué au rôle **propriétaire**. 

    > [!IMPORTANT]
    > L’accès propriétaire permet au principal de service d’effectuer quasiment n’importe quelle opération dans votre espace de travail. Il est utilisé dans ce document pour vous montrer comment accorder l’accès. Dans un environnement de production, Microsoft recommande d’accorder au principal de service l’accès minimal nécessaire pour qu’il puisse exécuter le rôle que vous lui donnez. Pour plus d’informations, consultez [Gérer l’accès à un espace de travail Azure Machine Learning](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Cet appel ne produit pas de sortie en cas de réussite.

### <a name="use-a-service-principal-from-the-sdk"></a>Utiliser un principal de service à partir du SDK

Pour vous authentifier sur votre espace de travail à partir du SDK, à l’aide du principal de service, utilisez le constructeur de classe `ServicePrincipalAuthentication`. Utilisez les valeurs que vous avez obtenues lors de la création du fournisseur de services en tant que paramètres. Les paramètres `tenant_id`, `service_principal_id` et `service_principal_password` sont respectivement mappés aux valeurs `tenantId`, `clientId`et `clientSecret` notées plus haut.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

La variable `sp` contient maintenant un objet d’authentification que vous utilisez directement dans le SDK. En règle générale, il est conseillé de stocker les ID/secrets utilisés ci-dessus dans des variables d’environnement, comme dans ce code. Le stockage dans des variables d’environnement empêche l’archivage accidentel des informations dans un dépôt GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Pour les workflows automatisés qui s’exécutent dans Python et utilisent principalement le SDK, vous pouvez utiliser cet objet tel quel dans la plupart des cas d’authentification. Le code suivant effectue l’authentification auprès de votre espace de travail à l’aide de l’objet d’authentification que vous avez créé.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Utiliser un principal de service à partir d’Azure CLI

Vous pouvez utiliser un principal de service pour les commandes Azure CLI. Pour plus d’informations, consultez [Se connecter avec un principal de service](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Utiliser un principal de service avec l’API REST (préversion)

Le principal de service permet également de s’authentifier auprès de l’[’API REST](https://docs.microsoft.com/rest/api/azureml/) Azure Machine Learning (préversion). Vous utilisez le [flux d’octroi des informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) Azure Active Directory, qui permet d’effectuer des appels de service à service pour l’authentification sans affichage dans les workflows automatisés. Les exemples sont implémentés avec la [bibliothèque ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) en Python et en Node.js, mais vous pouvez aussi utiliser n’importe quelle bibliothèque open source prenant en charge OpenID Connect 1.0.

> [!NOTE]
> MSAL.js est une bibliothèque plus récente qu’ADAL. Toutefois, MSAL.js ne prend pas en charge l’authentification de service à service avec les informations d’identification du client, car il s’agit principalement d’une bibliothèque côté client conçue pour l’authentification interactive/basée sur l’interface utilisateur qui est associée à un utilisateur spécifique. Nous vous recommandons d’utiliser ADAL comme indiqué ci-dessous pour créer des workflows automatisés avec l’API REST.

#### <a name="nodejs"></a>Node.js

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

La variable `tokenResponse` est un objet qui contient le jeton et des métadonnées associées, comme le délai d’expiration. Les jetons restent valides pendant 1 heure, après quoi vous devez les actualiser en réexécutant le même appel pour récupérer un nouveau jeton. L’extrait de code suivant est un exemple de réponse.

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

#### <a name="python"></a>Python

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

La variable `token_response` est un dictionnaire qui contient le jeton et des métadonnées associées, comme le délai d’expiration. Les jetons restent valides pendant 1 heure, après quoi vous devez les actualiser en réexécutant le même appel pour récupérer un nouveau jeton. L’extrait de code suivant est un exemple de réponse.

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

Les déploiements de modèle créés par Azure Machine Learning fournissent deux méthodes d’authentification :

* **Basée sur une clé** : une clé statique est utilisée pour l’authentification auprès du service web.
* **Basée sur un jeton** : un jeton temporaire doit être obtenu de l’espace de travail et utilisé pour s’authentifier auprès du service web. Ce jeton expire au bout d’un certain temps et doit être actualisé pour continuer à fonctionner avec le service web.

    > [!NOTE]
    > L’authentification basée sur un jeton est disponible uniquement lors du déploiement sur Azure Kubernetes Service.

### <a name="key-based-web-service-authentication"></a>Authentification de service web basée sur une clé

L’authentification basée sur une clé est *activée* par défaut pour les services web déployés sur Azure Kubernetes Service (AKS). Elle est *désactivée* par défaut pour les services déployés sur Azure Container Instances (ACI). Vous pouvez toutefois l’activer en définissant `auth_enabled=True` au moment où vous créez votre service web ACI. Voici un exemple de code montrant comment créer une configuration de déploiement sur ACI lorsque l’authentification basée sur une clé est activée.

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

Pour plus d’informations sur l’authentification auprès d’un modèle déployé, consultez [Créer un client pour un modèle déployé en tant que service web](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Authentification basée sur les jetons auprès d’un service web

Quand vous activez l’authentification par jeton pour un service web, les utilisateurs doivent présenter un jeton JSON Web Token Azure Machine Learning au service web pour y accéder. Le jeton expire après un laps de temps spécifié, après quoi il doit être actualisé pour continuer à effectuer des appels.

* L’authentification par jeton est **désactivée par défaut** lors d’un déploiement sur Azure Kubernetes Service.
* L’authentification par jeton **n’est pas prise en charge** pour les déploiements sur Azure Container Instances.
* L’authentification par jeton **ne peut pas être utilisée en même temps que l’authentification basée sur une clé**.

Pour contrôler l’authentification par jeton, utilisez le paramètre `token_auth_enabled` quand vous créez ou mettez à jour un déploiement :

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Si l’authentification par jeton est activée, vous pouvez utiliser la méthode `get_token` pour récupérer un jeton JWT (JSON Web Token) et son délai d’expiration :

> [!TIP]
> Si vous utilisez un principal de service pour obtenir le jeton et souhaitez qu’il dispose de l’accès minimal requis pour récupérer un jeton, attribuez-le au rôle **lecteur** de l’espace de travail.

```python
token, refresh_by = aks_service.get_token()
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

* [Utiliser des secrets dans les l’apprentissage](how-to-use-secrets-in-runs.md).
* [Entraînez et déployez un modèle de classification d’images](tutorial-train-models-with-aml.md).
* [Utilisez un modèle Azure Machine Learning déployé en tant que service web](how-to-consume-web-service.md).
