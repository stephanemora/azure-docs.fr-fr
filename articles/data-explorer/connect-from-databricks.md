---
title: Se connecter à Azure Data Explorer à partir d’Azure Databricks en utilisant Python
description: Cette rubrique vous montre comment utiliser la bibliothèque Python dans Azure Databricks pour accéder aux données d’Azure Data Explorer (ADX) en employant l’une des deux méthodes d’authentification.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52429924"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Se connecter à Azure Data Explorer à partir d’Azure Databricks en utilisant Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) est une plateforme d’analytique basée sur Apache Spark et optimisée pour la plateforme de services cloud Microsoft Azure. Cet article vous montre comment utiliser la bibliothèque Python dans Azure Databricks pour accéder aux données d’Azure Data Explorer (ADX). Il existe plusieurs façons de s’authentifier auprès d’ADX, notamment la connexion à l’appareil et l’application Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Prérequis

- [Créer un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
- [Créer un espace de travail Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    Sous **Service Azure Databricks**, dans la liste déroulante **Niveau tarifaire**, sélectionnez **Premium**. Cela vous permet d’utiliser des secrets Azure Databricks pour stocker vos informations d’identification et les référencer dans les notebooks et les travaux.

- [Créez un cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) dans Azure Databricks avec les spécifications suivantes (paramètres minimum nécessaires pour exécuter les exemples de notebooks) :

![Créer un cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Installer la bibliothèque Python sur votre cluster Azure Databricks

Pour installer la [bibliothèque Python](/azure/kusto/api/python/kusto-python-client-library) sur votre cluster Azure Databricks :

1. Accédez à votre espace de travail Azure Databricks et [créez une bibliothèque](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Chargez un package Python PyPI ou Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
    - Chargez, installez et attachez la bibliothèque à votre cluster Databricks.
    - Entrez le nom PyPi : *azure-kusto-data*.

## <a name="connect-to-adx-using-device-login"></a>Se connecter à ADX à l’aide de la connexion à l’appareil

[Importez un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) en utilisant le notebook [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) pour vous connecter à ADX en utilisant vos informations d’identification.

## <a name="connect-to-adx-using-azure-ad-app"></a>Se connecter à ADX en utilisant une application Azure AD

1. Créez une application Azure AD en [provisionnant une application AAD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Accordez l’accès à votre application Azure AD dans votre base de données Azure Data Explorer comme suit :

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | nom de votre base de données |
    | ```AAD App ID``` | ID de votre application Azure AD |
    | ```AAD Tenant ID``` | ID de votre locataire Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Rechercher votre ID de locataire Azure AD

Pour authentifier une application, Azure Data Explorer utilise votre ID de locataire Azure AD. Pour trouver votre ID de locataire, utilisez l’URL suivante en remplaçant *YourDomain* par votre domaine.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Par exemple, si votre domaine est *contoso.com*, l’URL est : [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Cliquez sur cette URL pour voir les résultats. La première ligne est la suivante. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Votre ID d’abonné est `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Stocker et sécuriser vos clé et ID d’application Azure AD 

Stockez et sécurisez vos clé et ID d’application Azure AD en utilisant des [secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) Azure Databricks comme suit :
1. [Configurez l’interface CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installez l’interface CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Configurez l’authentification](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurez les [secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) à l’aide des exemples de commandes suivants :

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importer un notebook
[Importez un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) en utilisant le notebook [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) pour vous connecter à ADX. Mettez à jour les valeurs des espaces réservés avec vos nom de cluster, nom de base de données et ID de locataire Azure AD.