---
title: 'Démarrage rapide : Interroger des données à l’aide de la bibliothèque Python d’Azure Data Explorer'
description: Dans ce guide de démarrage rapide, vous allez apprendre à interroger des données à partir de l’Explorateur de données Azure en utilisant Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: 4de8f68e0384742cea4ce50ccd23a7455b186893
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048739"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Démarrage rapide : Interroger des données à l’aide de la bibliothèque Python d’Azure Data Explorer

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. L’Explorateur de données Azure fournit une [bibliothèque cliente de données pour Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Cette bibliothèque vous permet d’interroger les données de votre code. Dans ce guide de démarrage rapide, vous vous connectez à une table sur le *cluster help* que nous avons configuré pour vous assister dans votre apprentissage. Ensuite, vous interrogez une table sur ce cluster, puis retournez les résultats.

Ce guide de démarrage rapide est également disponible dans [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Prérequis

* Un compte e-mail professionnel qui est membre d’Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) installé sur votre ordinateur de développement

## <a name="install-the-data-library"></a>Installer la bibliothèque de données

Installez *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Ajouter les constantes et les instructions d’importation

Importez des classes de la bibliothèque ainsi que *pandas*, bibliothèque d’analyse de données.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Pour authentifier une application, l’Explorateur de données Azure utilise votre ID de locataire AAD. Pour trouver votre ID de locataire, utilisez l’URL suivante en remplaçant *YourDomain* par votre domaine.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Par exemple, si votre domaine est *contoso.com*, l’URL est : [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Cliquez sur cette URL pour voir les résultats. La première ligne est la suivante.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ici, l’ID de locataire est `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Définissez la valeur pour AAD_TENANT_ID avant d’exécuter ce code.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Maintenant, créez la chaîne de connexion. Cet exemple utilise l’authentification de l’appareil pour accéder au cluster. Vous pouvez également utiliser un [certificat d’application AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), une [clé d’application AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) et un [utilisateur et un mot de passe AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Se connecter à l’Explorateur de données Azure et exécuter une requête

Exécutez une requête sur le cluster et stockez le résultat dans un dataframe. Lorsque ce code s'exécute, il renvoie un message semblable au suivant : *Pour vous connecter, utilisez un navigateur web afin d'ouvrir la page https://microsoft.com/devicelogin et entrez le code F3W4VWZDM pour vous authentifier*. Suivez les étapes pour vous connecter, puis revenez pour exécuter le bloc de code suivant.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Explorer les données dans un dataframe

Après avoir entré un identifiant, la requête retourne les résultats, qui sont stockés dans un dataframe. Vous pouvez utiliser les résultats comme vous le faites dans n’importe quel autre dataframe.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Vous devriez voir les dix premiers résultats de la table StormEvents.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Ingérer des données à l’aide de la bibliothèque Python de l’Explorateur de données Azure](python-ingest-data.md)
