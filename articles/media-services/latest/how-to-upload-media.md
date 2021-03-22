---
titre : Charger des médias : Description d’Azure Media Services : Découvrez comment charger des médias pour le streaming ou l’encodage.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Charger des médias pour le streaming ou l’encodage

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dans Media Services, vous chargez vos fichiers numériques (médias) dans le conteneur d’objets blob associé à une ressource. L’entité [Asset](/rest/api/media/operations/asset) peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des légendes (et les métadonnées concernant ces fichiers). Une fois les fichiers chargés dans le conteneur de la ressource, votre contenu est stocké en toute sécurité dans le cloud et peut faire l'objet d'un traitement et d'une diffusion en continu.

Avant de commencer, vous devez cependant recueillir ou réfléchir à quelques valeurs.

1. Le chemin local du fichier à charger
1. L’ID de la ressource (conteneur)
1. Le nom que vous souhaitez attribuer au fichier chargé, sans oublier l’extension
1. Le nom du compte de stockage que vous utilisez
1. La clé d'accès de votre compte de stockage

## <a name="portal"></a>[Portail](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

En supposant que votre code a déjà établi l’authentification et que vous avez déjà créé une ressource d’entrée, utilisez l’extrait de code suivant pour charger des fichiers locaux vers cette ressource (in_container).

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
Pour les autres méthodes, consultez la [documentation Stockage Azure](../../storage/blobs/index.yml) en vue d’utiliser des objets blob avec [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) et [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Étapes suivantes

> [Présentation de Media Services](media-services-overview.md)
