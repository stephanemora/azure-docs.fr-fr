---
title: 'Démarrage rapide : Interroger des données à l’aide de la bibliothèque Python de l’Explorateur de données Azure'
description: Dans ce guide de démarrage rapide, vous allez apprendre à interroger des données à partir de l’Explorateur de données Azure en utilisant Python.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: fee982812456548ed6d1e15d86151df88532389f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956096"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Démarrage rapide : Interroger des données à l’aide de la bibliothèque Python de l’Explorateur de données Azure

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement scalable pour les données des journaux et de télémétrie. L’Explorateur de données Azure fournit une [bibliothèque cliente de données pour Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Cette bibliothèque vous permet d’interroger les données de votre code. Dans ce guide de démarrage rapide, vous vous connectez à une table sur le *cluster help* que nous avons configuré pour vous assister dans votre apprentissage. Ensuite, vous interrogez une table sur ce cluster, puis retournez les résultats.

Ce guide de démarrage rapide est également disponible dans [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Prérequis

* Un compte e-mail professionnel qui est membre d’Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) installé sur votre ordinateur de développement

## <a name="install-the-data-library"></a>Installer la bibliothèque de données

Installez *azure-kusto-data*.

```python
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Ajouter les constantes et les instructions d’importation

Importez des classes de la bibliothèque ainsi que *pandas*, bibliothèque d’analyse de données.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
import pandas as pd
```

Pour authentifier une application, l’Explorateur de données Azure utilise votre ID de locataire AAD. Pour trouver votre ID de locataire, utilisez l’URL suivante en remplaçant *YourDomain* par votre domaine.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Par exemple, si votre domaine est *contoso.com*, l’URL est : [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Cliquez sur cette URL pour voir les résultats. La première ligne est la suivante. 

```
`"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"`
```

Ici, l’ID de locataire est `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Définissez la valeur pour AAD_TENANT_ID avant d’exécuter ce code.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Maintenant, créez la chaîne de connexion. Cet exemple utilise l’authentification de l’appareil pour accéder au cluster. Vous pouvez également utiliser un certificat d’application AAD, une clé d’application AAD, et un utilisateur et un mot de passe AAD.

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Se connecter à l’Explorateur de données Azure et exécuter une requête

Exécutez une requête sur le cluster et stockez le résultat dans un dataframe. Lorsque ce code s’exécute, il retourne un message similaire au suivant : *Pour vous connecter, utilisez un navigateur web pour ouvrir la page https://microsoft.com/devicelogin et entrez le code F3W4VWZDM pour l’authentification*. Suivez les étapes pour vous connecter, puis revenez pour exécuter le bloc de code suivant.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

df = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, KUSTO_QUERY).primary_results[0].to_dataframe()
```

## <a name="explore-data-in-dataframe"></a>Explorer les données dans un dataframe

Après avoir entré un identifiant, la requête retourne les résultats, qui sont stockés dans un dataframe. Vous pouvez utiliser les résultats comme vous le faites dans n’importe quel autre dataframe.

```python
df
```

Vous devriez voir les dix premiers résultats de la table StormEvents.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Python de l’Explorateur de données Azure](python-ingest-data.md)