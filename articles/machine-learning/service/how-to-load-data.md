---
title: Charger des données avec le kit SDK Azure Machine Learning Data Prep - Python
description: Découvrez comment charger des données avec le kit SDK Azure Machine Learning Data Prep. Vous pouvez charger les différents types de données d’entrée, spécifier les paramètres et types de fichiers de données ou utiliser la fonctionnalité de lecture intelligente du kit SDK pour détecter automatiquement le type de fichier.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 436ff9d318dc311efe27352a8b2ac91cfb5be618
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221319"
---
#<a name="load-and-read-data-with-azure-machine-learning"></a>Charger et lire des données avec Azure Machine Learning

Utilisez le [SDK de préparation des données Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) pour charger différents types de données d’entrée. 

Pour charger vos données, vous disposez de deux approches :
+ Spécifier le type de fichier de données et ses paramètres
+ Utiliser la fonctionnalité de lecture intelligente du SDK pour détecter automatiquement le type d’un fichier

## <a name="use-text-line-data"></a>Utiliser les données des lignes de texte 
Une des méthodes les plus simples pour charger des données consiste à les lire sous forme de lignes de texte.

Voici un exemple de code :
```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Lignes|
|----|-----|
|0|Date \|\| Température minimale \|\| Température maximale|
|1|01-07-2015 \|\| -4,1 \|\| 10,0|
|2|02-07-2015 \|\| -0,8 \|\| 10,8|
|3|03-07-2015 \|\| -7,0 \|\| 10,5|
|4|04-07-2015 \|\| -5,5 \|\| 9,3|

Une fois les données ingérées, vous pouvez récupérer une trame de données pandas pour l’intégralité du jeu de données.

Voici un exemple de code :
```python
df = dataflow.to_pandas_dataframe()
df
```
Exemple de sortie :
||Lignes|
|----|-----|
|0|Date \|\| Température minimale \|\| Température maximale|
|1|01-07-2015 \|\| -4,1 \|\| 10,0|
|2|02-07-2015 \|\| -0,8 \|\| 10,8|
|3|03-07-2015 \|\| -7,0 \|\| 10,5|
|4|04-07-2015 \|\| -5,5 \|\| 9,3|

## <a name="use-csv-data"></a>Utiliser des données CSV
Lors de la lecture de fichiers délimités, vous pouvez laisser le runtime sous-jacent déduire les paramètres d’analyse (par exemple, un séparateur, un codage, s’il faut utiliser des en-têtes, etc.) au lieu de les fournir. Pour cet exemple, essayez de lire un fichier en spécifiant uniquement son emplacement. 

Voici un exemple de code :
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

Exemple de sortie :
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

L’un des paramètres que vous pouvez spécifier renseigne sur un nombre de lignes à ignorer dans les fichiers que vous lisez. Utilisez le code suivant pour exclure la ligne en double.
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

Exemple de sortie :
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

Ensuite, vous pouvez examiner les types de données des colonnes.
Voici un exemple de code :
```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

Malheureusement, toutes les colonnes sont renvoyées sous forme de chaînes. La raison en est la suivante, par défaut, le kit SDK Azure Machine Learning Data Prep ne modifie pas votre type de données. La source de données qui est lue étant un fichier texte, le kit SDK lit donc toutes les valeurs sous forme de chaînes. Pour cet exemple, cependant, nous souhaitons analyser les colonnes numériques sous forme de nombres. Pour ce faire, vous pouvez définir le paramètre inference_arguments sur current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Plusieurs colonnes ont été correctement détectées sous forme de nombres et leur type présente la valeur float64. L’ingestion terminée, vous pouvez récupérer une trame de données pandas pour l’intégralité du jeu de données.
Voici un exemple de code :
```python
df = dataflow.to_pandas_dataframe()
df
```

Exemple de sortie :
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1,017100e+10|49,0|
|1|ALABAMA|Hale County|1,017100e+10|40,0|
|2|ALABAMA|Hale County|1,017100e+10|43,0|
|3|ALABAMA|Hale County|1,017100e+10|2.0|
|4|ALABAMA|Hale County|1,017100e+10|23,0|

## <a name="use-excel-data"></a>Utiliser des données Excel
Le kit SDK Azure Machine Learning Data Prep comprend une fonction `read_excel` pour charger des fichiers Excel. Voici un exemple de code :
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

Exemple de sortie :
||Colonne1|Colonne2|Colonne3|Colonne4|Colonne5|Colonne6|Colonne7|Colonne8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Fer, IVB|60000000,0|Trouvé|1920,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19,58333|17,91667|
|1|Cap York|Fer, IIIAB|58200000,0|Trouvé|1818,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76,13333|-64,93333|
|2|Campo del Cielo|Fer, IAB-MG|50000000,0|Trouvé|1576,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27,46667|-60,58333|
|3|Canyon Diablo|Fer, IAB-MG|30000000,0|Trouvé|1891,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35,05000|-111,03333|
|4|Armanty|Fer, IIIE|28000000,0|Trouvé|1898,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47,00000|88,00000|

Vous avez chargé la première feuille du fichier Excel. Vous pouvez obtenir le même résultat en spécifiant explicitement le nom de la feuille que vous souhaitez charger. Si vous voulez plutôt charger la deuxième feuille, vous pouvez indiquer son nom en tant qu’argument. Par exemple : 
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

Exemple de sortie :
||Colonne1|Colonne2|Colonne3|Colonne4|Colonne5|Colonne6|Colonne7|Colonne8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|
|1|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|
|2|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|Aucun|
|3|RANK|Intitulé|Studio|Mondial|National / %|Colonne1|À l’étranger / %|Colonne2|Année^|
|4|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|5.|

Comme vous pouvez le constater, le tableau dans la deuxième feuille comportait des en-têtes et trois lignes vides. Vous devez modifier les arguments de la fonction en conséquence. Par exemple :
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

Exemple de sortie :
||RANK|Intitulé|Studio|Mondial|National / %|Colonne1|À l’étranger / %|Colonne2|Année^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Titanic|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|
|2|3|Avengers|BV|1518,6|623,4|0.41|895,2|0,59|2012|
|3|4|Harry Potter et les Reliques de la Mort 2e partie|WB|1341,5|381|0,284|960,5|0,716|2011|
|4|5.|Frozen|BV|1274,2|400,7|0,314|873,5|0,686|2013|

## <a name="use-fixed-width-data-files"></a>Utiliser des fichiers de données de largeur fixe
Pour les fichiers de largeur fixe, vous pouvez spécifier une liste de décalages. La première colonne est toujours supposée démarrer au décalage 0. Par exemple :
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

Exemple de sortie :
||010000|99999|FICTIF NORVÈGE|NON|NO_1|ENRS|Colonne7|Colonne8|Colonne9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|FICTIF NORVÈGE|NON|NON|ENSO||||
|1|010010|99999|JAN MAYEN|NON|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NON|NON|||||
|3|010014|99999|SOERSTOKKEN|NON|NON|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NON|NON|ENBL|+61383|+005867|+03270|


S’il n’y a aucun en-tête dans les fichiers, vous devez traiter la première ligne en tant que données. En passant `PromoteHeadersMode.NONE` à l’argument header keyword, vous pouvez éviter la détection de l’en-tête et obtenir les données correctes. Par exemple : 
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

Exemple de sortie :

||Colonne1|Colonne2|Colonne3|Colonne4|Colonne5|Colonne6|Colonne7|Colonne8|Colonne9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|FICTIF NORVÈGE|NON|NO_1|ENRS|Colonne7|Colonne8|Colonne9|
|1|010003|99999|FICTIF NORVÈGE|NON|NON|ENSO||||
|2|010010|99999|JAN MAYEN|NON|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NON|NON|||||
|4|010014|99999|SOERSTOKKEN|NON|NON|ENSO|+59783|+005350|+00500|
|5.|010015|99999|BRINGELAND|NON|NON|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>Utiliser des données SQL
Le kit SDK Azure Machine Learning Data Prep peut également charger des données à partir de serveurs SQL. Actuellement, seul Microsoft SQL Server est pris en charge.
Pour lire des données à partir d’un serveur SQL Server, créez un objet de source de données contenant les informations de connexion. Par exemple : 
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Comme vous pouvez le constater, le paramètre password de `MSSQLDataSource` accepte un objet secret. Vous pouvez obtenir un objet secret de deux manières :
-   Enregistrement du secret et de sa valeur avec le moteur d’exécution 
-   Création du secret avec uniquement un ID (utile si la valeur du secret est déjà inscrite dans l’environnement d’exécution)

Après avoir créé un objet de source de données, vous pouvez passer à la lecture des données. Par exemple : 
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

Exemple de sortie :
||IDProduit|NOM|RéférenceProduit|Couleur|CoûtStandard|ListPrice|Taille|Poids|IDCatégorieProduit|IDModèleProduit|DateDébutVente|DateFinVente|DateInterruption|PhotoMiniature|NomFichierPhotoMiniature|GuidLigne|DateModification|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|Cadre route HL - Noir, 58|FR-R92B-58|Noir|1059,3100|1431,50|58|1016,04|18|6.|01-06-2002 00:00:00+00:00|Aucun|Aucun|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|03-11-2008 |0:01:36.827000+00:00|
|1|706|Cadre route HL - Rouge, 58|FR-R92R-58|Rouge|1059,3100|1431,50|58|1016,04|18|6.|01-06-2002 00:00:00+00:00|Aucun|Aucun|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|03-11-2008 |10:01:36.827000+00:00|
|2|707|Casque Sport-100, Rouge|HL-U509-R|Rouge|13,0863|34,99|Aucun|Aucun|35|33|01-07-2005 00:00:00+00:00|Aucun|Aucun|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|03-11-2008 |10:01:36.827000+00:00|
|3|708|Casque Sport-100, Noir|HL-U509|Noir|13,0863|34,99|Aucun|Aucun|35|33|01-07-2005 00:00:00+00:00|Aucun|Aucun|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|03-11-2008 |10:01:36.827000+00:00|
|4|709|Chaussettes Mountain Bike, M|SO-B909-M|Blanc|3,3963|9,50|M|Aucun|27|18|01-07-2005 00:00:00+00:00|30-06-2006 00:00:00+00:00|Aucun|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|03-11-2008 |10:01:36.827000+00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

Exemple de sortie :
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Utiliser Azure Data Lake Storage
Le kit SDK peut obtenir le jeton OAuth nécessaire à l’accès d’Azure Data Lake Storage de deux façons :
-   Récupération du jeton d’accès à partir d’une session de connexion récente de la connexion Azure CLI de l’utilisateur
-   Utilisation d’un principal de service et d’un certificat en tant que secret

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Utiliser un jeton d’accès à partir d’une session Azure CLI récente
Sur votre machine locale, exécutez la commande suivante :

> [!NOTE] 
> Si votre compte d’utilisateur est membre de plusieurs locataires Azure, vous devez spécifier le locataire sous la forme d’un nom d’hôte URL AAD.


Par exemple : 
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Créer un principal de service avec l’interface Azure CLI
Vous pouvez utiliser l’interface Azure CLI pour créer un principal de service et le certificat correspondant. Ce principal de service particulier est configuré en tant que Lecteur, avec une portée limitée au seul compte Azure Data Lake Storage « dpreptestfiles ».  Par exemple : 
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Cette commande émet le `appId` ainsi que le chemin au fichier de certificat (généralement dans le dossier de base). Le fichier .crt contient le certificat public et la clé privée au format PEM.

Pour extraire le Thumbprint :
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Pour configurer l’ACL pour le système de fichiers Azure Data Lake Storage, utilisez l’objectId de l’utilisateur ou, pour cet exemple, le principal de service. Par exemple : 
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Pour configurer l’accès `Read` et `Execute` pour le système de fichiers Azure Data Lake Storage, vous devez configurer l’ACL individuellement pour les dossiers et pour les fichiers. Ceci est dû au fait que le modèle d’ACL du système de fichiers HDFS sous-jacent ne prend pas en charge l’héritage. Par exemple : 
```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
### <a name="acquire-an-oauth-access-token"></a>Obtenir un jeton d’accès OAuth
Utilisez le package `adal` (par le biais de : `pip install adal`) pour créer un contexte d’authentification sur le locataire MSFT et vous procurer un jeton d’accès OAuth. Pour ADLS, la ressource dans la demande de jeton doit être pour 'https://datalake.azure.net', ce qui est différent de la plupart des autres ressources Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Website|street|city|County|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledonia|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Six Mile|||
|4|1010691|10th Steet Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php... |10th Street and Poplar|Lamar|Barton|

## <a name="use-smart-reading"></a>Utiliser la « lecture intelligente »

Utilisez la fonctionnalité de lecture intelligente du SDK pour détecter automatiquement le type d’un fichier.
