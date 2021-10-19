---
title: Utilisation des API standard DICOMweb avec Python-API de santé Azure
description: Ce didacticiel explique comment utiliser les API standard DICOMweb avec Python.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: c474409b2dfdbca0a921690b1871c7268bde08d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782415"
---
# <a name="using-dicomwebtrade-standard-apis-with-python"></a>Utilisation &trade; d’API standard DICOMWeb avec Python

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Ce didacticiel utilise Python pour illustrer l’utilisation du service DICOM.

Dans le didacticiel, nous allons utiliser les [fichiers DICOM Sample. DCM](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)suivants.

* Blue-Circle. DCM
* dicom-metadata.csv
* Green-Square. DCM
* Red-triangle. DCM

 Le nom de fichier, studyUID, seriesUID et instanceUID de l’exemple de fichiers DICOM se présente comme suit :

| Fichier | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|Green-Square. DCM|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|Red-triangle. DCM|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|Blue-Circle. DCM|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> Chacun de ces fichiers représente une instance unique et fait partie de la même étude. En outre, le carré vert et le triangle rouge font partie de la même série, tandis que le cercle bleu se trouve dans une série distincte.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les &trade; API standard DICOMWeb, vous devez disposer d’une instance du service DICOM déployée. Si vous n’avez pas encore déployé le service DICOM, consultez [déployer le service DICOM à l’aide de l’portail Azure](deploy-dicom-services-in-azure.md).

Une fois que vous avez déployé une instance du service DICOM, récupérez l’URL de votre App service :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).
1. Recherchez les **ressources récentes** et sélectionnez votre instance de service DICOM.
1. Copiez l' **URL du service** de votre service DICOM. 
2. Si vous n’avez pas encore obtenu de jeton, consultez [obtenir un jeton d’accès pour le service DICOM à l’aide de Azure CLI](dicom-get-access-token-azure-cli.md). 

Pour ce code, nous allons accéder à un service Azure Preview public. Il est important de ne pas charger les informations d’intégrité privée (PHI).

## <a name="working-with-the-dicom-service"></a>Utilisation du service DICOM

La norme DICOMweb utilise &trade; beaucoup les `multipart/related` requêtes http combinées aux en-têtes Accept spécifiques à DICOM. Les développeurs familiarisés avec d’autres API REST trouvent souvent les manipulations de DICOMweb &trade; standard. Toutefois, une fois qu’elle est en cours d’exécution, elle est facile à utiliser. Il suffit simplement de vous familiariser avec la prise en main.

### <a name="import-the-appropriate-python-libraries"></a>Importer les bibliothèques python appropriées

Tout d’abord, importez les bibliothèques Python nécessaires.

Nous avons choisi d’implémenter cet exemple à l’aide de la `requests` bibliothèque synchrone. Pour la prise en charge asynchrone, envisagez d’utiliser `httpx` ou une autre bibliothèque asynchrone. En outre, nous importons deux fonctions de prise en charge de `urllib3` pour prendre en charge l’utilisation des `multipart/related` requêtes.

En outre, nous sommes en train `DefaultAzureCredential` d’importer pour ouvrir une session dans Azure et obtenir un jeton.

```python
import requests
import pydicom
from pathlib import Path
from urllib3.filepost import encode_multipart_formdata, choose_boundary
from azure.identity import DefaultAzureCredential
```

### <a name="configure-user-defined-variables-to-be-used-throughout"></a>Configurer les variables définies par l’utilisateur à utiliser dans tout

Remplacez toutes les valeurs de variables encapsulées dans {} par vos propres valeurs. En outre, vérifiez que toutes les variables construites sont correctes.  Par exemple, `base_url` est construit à l’aide de l’URL du service, puis ajouté à la version de l’API REST utilisée. L’URL du service de votre service DICOM sera : ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Vous pouvez utiliser le portail Azure pour accéder au service DICOM et obtenir l’URL de votre service. Vous pouvez également consulter la documentation sur le contrôle [de version des API pour le service DICOM](api-versioning-dicom-service.md) pour plus d’informations sur le contrôle de version. Si vous utilisez une URL personnalisée, vous devez remplacer cette valeur par les vôtres.

```python
dicom_service_name = "{server-name}"
path_to_dicoms_dir = "{path to the folder that includes green-square.dcm and other dcm files}"

base_url = f"{Service URL}/v{version}"

study_uid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; #StudyInstanceUID for all 3 examples
series_uid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; #SeriesInstanceUID for green-square and red-triangle
instance_uid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; #SOPInstanceUID for red-triangle
```

### <a name="authenticate-to-azure-and-get-a-token"></a>S’authentifier auprès d’Azure et obtenir un jeton

`DefaultAzureCredential` nous permet d’obtenir différentes façons d’obtenir des jetons pour se connecter au service. Nous allons utiliser `AzureCliCredential` pour obtenir un jeton pour se connecter au service. D’autres fournisseurs d’informations d’identification, tels que `ManagedIdentityCredential` et, `EnvironmentCredential` peuvent également être utilisés. Pour pouvoir utiliser AzureCliCredential, vous devez être connecté à Azure à partir de l’interface CLI avant d’exécuter ce code. (Pour plus d’informations, consultez [obtenir un jeton d’accès pour le service DICOM à l’aide de Azure CLI](dicom-get-access-token-azure-cli.md) .) Vous pouvez également simplement copier et coller le jeton récupéré lors de la connexion à partir de l’interface CLI.

> [!NOTE]
> `DefaultAzureCredential` retourne plusieurs objets d’informations d’identification différents. Nous référençons le `AzureCliCredential` comme 5e élément dans la collection retournée. Cela peut ne pas être cohérent. Dans ce cas, supprimez les marques de commentaire de la `print(credential.credential)` ligne. Cela permet de répertorier tous les éléments. Recherchez l’index correct, en rappelant que Python utilise l’indexation de base zéro.

> [!NOTE]
> Si vous n’êtes pas connecté à Azure à l’aide de l’interface CLI, cette opération échoue. Pour que cela fonctionne, vous devez être connecté à Azure à partir de l’interface CLI. 

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()

#print(credential.credentials) # this can be used to find the index of the AzureCliCredential
token = credential.credentials[4].get_token('https://dicom.healthcareapis.azure.com')
bearer_token = f'Bearer {token.token}'
```

### <a name="create-supporting-methods-to-support-multipartrelated"></a>Créer des méthodes de prise en charge pour la prise en charge `multipart\related`

Les `Requests` bibliothèques (et la plupart des bibliothèques Python) ne fonctionnent pas avec `multipart\related` de manière à prendre en charge DICOMweb &trade; . En raison de ces bibliothèques, nous devons ajouter quelques méthodes pour prendre en charge l’utilisation des fichiers DICOM.

`encode_multipart_related` prend un ensemble de champs (dans le cas DICOM, ces bibliothèques sont généralement des fichiers de Dam partie 10) et une limite facultative définie par l’utilisateur. Elle retourne le corps complet, ainsi que le content_type, qui peut être utilisé.

```python
def encode_multipart_related(fields, boundary=None):
    if boundary is None:
        boundary = choose_boundary()

    body, _ = encode_multipart_formdata(fields, boundary)
    content_type = str('multipart/related; boundary=%s' % boundary)

    return body, content_type
```

### <a name="create-a-requests-session"></a>Créer une `requests` session

Crée une `requests` session, appelée `client` , qui sera utilisée pour communiquer avec le service DICOM.


```python
client = requests.session()
```

### <a name="verify-authentication-is-configured-correctly"></a>Vérifier que l’authentification est correctement configurée

Appelez le point de terminaison de l’API offrir, qui renverra un 200 si l’authentification réussit.

```python
headers = {"Authorization":bearer_token}
url= f'{base_url}/changefeed'

response = client.get(url,headers=headers)
if (response.status_code != 200):
    print('Error! Likely not authenticated!')
```

## <a name="uploading-dicom-instances-stow"></a>Chargement des instances DICOM (ARRIMer)

Les exemples suivants mettent en évidence la conservation des fichiers DICOM.

### <a name="store-instances-using-multipartrelated"></a>Stocker des instances à l’aide de `multipart/related`

Cet exemple montre comment télécharger un seul fichier DICOM et utilise un peu de Python pour précharger le fichier DICOM (en octets) en mémoire. En passant un tableau de fichiers au paramètre Fields de `encode_multipart_related` , plusieurs fichiers peuvent être téléchargés dans une seule publication. Il est parfois utilisé pour charger plusieurs instances au sein d’une série ou d’une étude complète.

_Plus_

* Chemin d’accès :.. /studies
* Méthode : POST
* Headers:
    * Accept : application/DICOM + JSON
    * Content-type : multipart/related ; type = "application/DICOM"
    * Autorisation : $token du porteur»

* Corps :
    * Content-type : application/DICOM pour chaque fichier téléchargé, séparé par une valeur limite

Certains langages de programmation et outils se comportent différemment. Par exemple, certaines d’entre elles nécessitent que vous définissiez votre propre limite. Pour ceux-ci, vous devrez peut-être utiliser un en-tête Content-type légèrement modifié. Les éléments suivants ont été utilisés avec succès.
* Content-type : multipart/related ; tapez = "application/DICOM"; limite = ABCD1234
* Content-type : multipart/related ; limite = ABCD1234
* Content-type : multipart/related

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    rawfile = reader.read()
files = {'file': ('dicomfile', rawfile, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```

### <a name="store-instances-for-a-specific-study"></a>Stocker des instances pour une étude spécifique

Cet exemple montre comment charger plusieurs fichiers DICOM dans l’étude spécifiée. Il utilise un peu de Python pour précharger le fichier DICOM (en octets) en mémoire.  

En passant un tableau de fichiers au paramètre Fields de `encode_multipart_related` , plusieurs fichiers peuvent être téléchargés dans une seule publication. Il est parfois utilisé pour télécharger une série ou une étude complète. 

_Plus_
* Chemin d’accès :.. /studies/{study}
* Méthode : POST
* Headers:
    * Accept : application/DICOM + JSON
    * Content-type : multipart/related ; type = "application/DICOM"
    * Autorisation : $token du porteur»
* Corps :
    * Content-type : application/DICOM pour chaque fichier téléchargé, séparé par une valeur limite


```python

filepath_red = Path(path_to_dicoms_dir).joinpath('red-triangle.dcm')
filepath_green = Path(path_to_dicoms_dir).joinpath('green-square.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath_red,'rb') as reader:
    rawfile_red = reader.read()
with open(filepath_green,'rb') as reader:
    rawfile_green = reader.read()  
       
files = {'file_red': ('dicomfile', rawfile_red, 'application/dicom'),
         'file_green': ('dicomfile', rawfile_green, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```
### <a name="store-single-instance-non-standard"></a>Stocker une seule instance (non standard)

L’exemple de code suivant montre comment télécharger un seul fichier DICOM. Il s’agit d’un point de terminaison d’API non standard qui simplifie le chargement d’un fichier unique comme octets binaires envoyés dans le corps d’une demande.

_Plus_
* Chemin d’accès :.. /studies
* Méthode : POST
* Headers:
   *  Accept : application/DICOM + JSON
   *  Type de contenu : application/DICOM
   *  Autorisation : $token du porteur»
* Corps :
    * Contient un seul fichier DICOM comme octets binaires.

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    body = reader.read()

headers = {'Accept':'application/dicom+json', 'Content-Type':'application/dicom', "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
response  # response should be a 409 Conflict if the file was already uploaded in the above request
```

## <a name="retrieve-dicom-instances-wado"></a>Récupération des instances DICOM (WADO)

Les exemples suivants mettent en évidence la récupération d’instances DICOM.

### <a name="retrieve-all-instances-within-a-study"></a>Récupération de toutes les instances dans une étude

Cet exemple récupère toutes les instances au sein d’une même étude.

_Plus_
* Chemin d’accès :.. /studies/{study}
* Méthode : GET
* Headers:
   * Accept : multipart/related ; tapez = "application/DICOM"; Transfer-Syntax = *
   * Autorisation : $token du porteur»

Les trois fichiers DCM que nous avons téléchargés précédemment font partie de la même étude, de sorte que la réponse doit retourner les trois instances. Vérifiez que le code d’état de la réponse est OK et que les trois instances sont retournées.

```python
url = f'{base_url}/studies/{study_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="use-the-retrieved-instances"></a>Utiliser les instances récupérées

Les instances sont récupérées en tant qu’octets binaires. Vous pouvez parcourir les éléments retournés et convertir les octets en un fichier, qui peut être lu par `pydicom` .


```python
import requests_toolbelt as tb
from io import BytesIO

mpd = tb.MultipartDecoder.from_response(response)
for part in mpd.parts:
    # Note that the headers are returned as binary!
    print(part.headers[b'content-type'])
    
    # You can convert the binary body (of each part) into a pydicom DataSet
    #   And get direct access to the various underlying fields
    dcm = pydicom.dcmread(BytesIO(part.content))
    print(dcm.PatientName)
    print(dcm.SOPInstanceUID)
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Récupérer les métadonnées de toutes les instances dans l’étude

Cette requête récupère les métadonnées pour toutes les instances au sein d’une même étude.

_Plus_
* Chemin d’accès :.. /studies/{study}/metadata
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Les trois `.dcm` fichiers que nous avons téléchargés précédemment font partie de la même étude, de sorte que la réponse doit retourner les métadonnées des trois instances. Vérifiez que le code d’état de la réponse est OK et que toutes les métadonnées sont retournées.

```python
url = f'{base_url}/studies/{study_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-all-instances-within-a-series"></a>Récupérer toutes les instances d’une série

Cette requête récupère toutes les instances au sein d’une même série.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}
* Méthode : GET
* Headers:
   * Accept : multipart/related ; tapez = "application/DICOM"; Transfer-Syntax = *
   * Autorisation : $token du porteur»

Cette série a deux instances (vert-carré et triangle rouge), de sorte que la réponse doit retourner les deux instances. Vérifiez que le code d’état de la réponse est OK et que les deux instances sont retournées.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-all-instances-in-series"></a>Récupérer les métadonnées de toutes les instances de la série

Cette requête récupère les métadonnées de toutes les instances d’une même série.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/metadata
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Cette série a deux instances (vert-carré et triangle rouge), de sorte que la réponse doit retourner pour les deux instances. Vérifiez que le code d’état de la réponse est OK et que les deux instances sont retournées.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Récupérer une instance unique au sein d’une série d’études

Cette requête récupère une instance unique.

_Plus_
* Chemin d’accès :.. /studies/{study}/series{series}/instances/{instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM ; Transfer-Syntax = *
   * Autorisation : $token du porteur»

Cet exemple de code ne doit retourner que le triangle rouge de l’instance. Vérifiez que la réponse a un code d’état OK et que l’instance est retournée.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
headers = {'Accept':'application/dicom; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Récupérer les métadonnées d’une instance unique au sein d’une série d’études

Cette requête récupère les métadonnées pour une instance unique au sein d’une même étude et série.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/instances/{instance}/metadata
* Méthode : GET
* Headers:
  * Accept : application/DICOM + JSON
  * Autorisation : $token du porteur»

Cet exemple de code doit retourner uniquement les métadonnées du triangle rouge de l’instance. Vérifiez que la réponse a un code d’état OK et que les métadonnées sont retournées.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Récupérer un ou plusieurs frames à partir d’une seule instance

Cette requête récupère un ou plusieurs frames à partir d’une seule instance.

_Plus_
* Chemin d’accès :.. /Studies/{Study}/Series{Series}/instances/{instance}/frames/1, 2, 3
* Méthode : GET
* Headers:
   * Autorisation : $token du porteur»
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1` (Par défaut) ou
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=*` ou
   * `Accept: multipart/related; type="application/octet-stream";`

Cet exemple de code doit retourner le seul Frame à partir du triangle rouge. Vérifiez que la réponse a un code d’état OK et que le frame est retourné.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/frames/1'
headers = {'Accept':'multipart/related; type="application/octet-stream"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

## <a name="query-dicom-qido"></a>Interroger DICOM (QIDO)

Dans les exemples suivants, nous allons Rechercher des éléments à l’aide de leurs identificateurs uniques. Vous pouvez également rechercher d’autres attributs, tels que PatientName.

Reportez-vous au document de l' [instruction de conformité DICOM](dicom-services-conformance-statement.md#supported-search-parameters) pour les attributs DICOM pris en charge.

### <a name="search-for-studies"></a>Rechercher des études

Cette requête recherche une ou plusieurs études par attributs DICOM.

_Plus_
* Chemin d’accès :.. Stud? StudyInstanceUID = {Study}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Vérifiez que la réponse comprend une étude et que le code de réponse est OK.

```python
url = f'{base_url}/studies'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'StudyInstanceUID':study_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series"></a>Rechercher une série

Cette requête recherche une ou plusieurs séries par attributs DICOM.

_Plus_
* Chemin d’accès :.. AX100? SeriesInstanceUID = {Series}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Vérifiez que la réponse comprend une série et que le code de réponse est OK.

```python
url = f'{base_url}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series-within-a-study"></a>Rechercher des séries au sein d’une étude

Cette requête recherche une ou plusieurs séries au sein d’une même étude par les attributs DICOM.

_Plus_
* Chemin d’accès :.. /studies/{study}/series? SeriesInstanceUID = {Series}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Vérifiez que la réponse comprend une série et que le code de réponse est OK.

```python
url = f'{base_url}/studies/{study_uid}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances"></a>Rechercher des instances

Cette requête recherche une ou plusieurs instances à l’aide d’attributs DICOM.

_Plus_
* Chemin d’accès :.. fois? SOPInstanceUID = {instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Vérifiez que la réponse comprend une instance et que le code de réponse est OK.

```python
url = f'{base_url}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study"></a>Rechercher des instances dans une étude

Cette requête recherche une ou plusieurs instances au sein d’une même étude par les attributs DICOM.

_Plus_
* Chemin d’accès :.. /studies/{study}/instances? SOPInstanceUID = {instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Vérifiez que la réponse comprend une instance et que le code de réponse est OK.

```python
url = f'{base_url}/studies/{study_uid}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study-and-series"></a>Rechercher des instances dans une étude et une série

Cette requête recherche une ou plusieurs instances au sein d’une seule étude et une seule série par attributs DICOM.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/instances? SOPInstanceUID = {instance}
* Méthode : GET
* Headers:
   * Accept : application/DICOM + JSON
   * Autorisation : $token du porteur»

Vérifiez que la réponse comprend une instance et que le code de réponse est OK.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances'
headers = {'Accept':'application/dicom+json'}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

## <a name="delete-dicom"></a>Supprimer DICOM

> [!NOTE]
> La suppression ne fait pas partie de la norme DICOM, mais elle a été ajoutée pour des raisons pratiques.

Un code de réponse 204 est renvoyé lorsque la suppression est réussie. Un code de réponse 404 est renvoyé si le ou les éléments n’existaient jamais ou s’il a déjà été supprimé.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Supprimer une instance spécifique dans une étude et une série

Cette demande supprime une seule instance au sein d’une seule étude et d’une seule série.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}/instances/{instance}
* Méthode : DELETE
* Headers:
   * Autorisation : $token de porteur

Cette demande supprime l’instance de triangle rouge du serveur. Si l’opération réussit, le code d’état de la réponse ne contient pas de contenu.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-series-within-a-study"></a>Supprimer une série spécifique au sein d’une étude

Cette demande supprime une seule série (et toutes les instances enfants) au sein d’une même étude.

_Plus_
* Chemin d’accès :.. /studies/{study}/series/{series}
* Méthode : DELETE
* Headers:
   * Autorisation : $token de porteur

Cet exemple de code supprime l’instance de carré vert (il s’agit du seul élément restant dans la série) du serveur. Si l’opération réussit, le code d’état de la réponse ne contient pas de contenu.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-study"></a>Supprimer une étude spécifique

Cette demande supprime une seule étude (et toutes les séries enfants et instances).

_Plus_
* Chemin d’accès :.. /studies/{study}
* Méthode : DELETE
* Headers:
   * Autorisation : $token de porteur

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le service DICOM, consultez. 

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
