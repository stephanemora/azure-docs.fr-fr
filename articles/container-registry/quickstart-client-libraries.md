---
title: 'Démarrage rapide : Gérer le contenu d’un registre de conteneurs avec des bibliothèques de client'
description: Utilisez ce guide de démarrage rapide pour gérer les référentiels, les images et les artefacts à l’aide des bibliothèques de client Azure Container Registry
author: dlepow
ms.topic: quickstart
ms.date: 10/05/2021
ms.author: danlep
ms.custom: ''
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 6af32e7bd841960cdf1fd3aef6af50171cc1e594
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007610"
---
# <a name="quickstart-use-the-azure-container-registry-client-libraries"></a>Démarrage rapide : Utiliser les bibliothèques de client Azure Container Registry

::: zone pivot="programming-language-csharp, programming-language-java, programming-language-javascript, programming-language-python"

Lisez cet article afin de bien démarrer avec la bibliothèque de client pour Azure Container Registry. Effectuez ces étapes pour tester un exemple de code pour des opérations de plan de données sur des images et des artefacts.

Utilisez la bibliothèques de client pour Azure Container Registry afin de :

* Lister des images ou des artefacts dans un registre.
* Obtenir des métadonnées pour des images et artefacts, des dépôts et des étiquettes.
* Définir les propriétés de lecture/écriture/suppression sur des éléments du registre.
* Supprimer des images et artefacts, des référentiels et des étiquettes.

Azure Container Registry a également une bibliothèque de gestion pour les opérations de plan de contrôle, notamment la création et les mises à jour du registre. 

## <a name="prerequisites"></a>Prérequis

* Pour utiliser cette bibliothèque, vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free/) et d’un registre de conteneurs Azure.

    Pour créer un registre de conteneurs Azure, vous pouvez utiliser le [portail Azure](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) ou [Azure CLI](container-registry-get-started-azure-cli.md). Voici un exemple utilisant Azure CLI :

    ```azurecli
    az acr create --name MyContainerRegistry --resource-group MyResourceGroup \
        --location westus --sku Basic
    ```

* Transmettre une ou plusieurs images conteneur à votre registre. Pour connaître les étapes, consultez [Transmettre votre première image à votre registre de conteneurs Azure à l’aide de l’interface de ligne de commande Docker](container-registry-get-started-docker-cli.md).

## <a name="key-concepts"></a>Concepts clés

* Un registre de conteneurs Azure stocke des *images conteneur* et des [artefacts OCI](container-registry-oci-artifacts.md). 
* Une image ou un artefact se compose d’un *manifeste* et de *couches*. 
* Un manifeste décrit les couches qui composent l’image ou l’artefact. Il est identifié de manière unique par son *code de hachage*. 
* Une image ou un artefact peut également être *étiqueté* pour lui attribuer un alias explicite. Une image ou un artefact peut être associé à zéro, une ou plusieurs étiquettes, et chaque étiquette identifie l’image de manière unique. 
* Une collection d’images ou d’artefacts ayant le même nom mais des étiquettes différentes est un *référentiel*.

Pour plus d’informations, consultez [À propos des registres, des dépôts et des artefacts](container-registry-concepts.md).

::: zone-end

::: zone pivot="programming-language-csharp"

## <a name="get-started"></a>Bien démarrer

[Code source][dotnet_source] | [Package (NuGet)][dotnet_package] | [Référence d’API][dotnet_docs] | [Exemples][dotnet_samples]

Pour développer du code d’application .NET capable de se connecter à une instance d’Azure Container Registry, vous aurez besoin de la bibliothèque `Azure.Containers.ContainerRegistry`.

### <a name="install-the-package"></a>Installer le package

Installez la bibliothèque de client Azure Container Registry pour .NET avec [NuGet][nuget] :

```Powershell
dotnet add package Azure.Containers.ContainerRegistry --prerelease
```

## <a name="authenticate-the-client"></a>Authentifier le client

Pour que votre application se connecte à votre registre, vous devez créer un `ContainerRegistryClient` qui peut s’authentifier auprès de celui-ci. Utilisez la [bibliothèque Azure Identity][dotnet_identity] afin d’ajouter la prise en charge Azure Active Directory pour l’authentification des clients du SDK Azure auprès de leurs services Azure correspondants.  

Lorsque vous développez et déboguez votre application localement, vous pouvez utiliser votre propre utilisateur pour vous authentifier auprès de votre registre. L’une des manières d’y parvenir consiste à [authentifier votre utilisateur auprès d’Azure CLI](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity#authenticating-via-the-azure-cli) et à exécuter votre application à partir de cet environnement. Si votre application utilise un client qui a été construit pour s’authentifier avec `DefaultAzureCredential`, il s’authentifiera correctement auprès du registre au point de terminaison spécifié.  

```C#
// Create a ContainerRegistryClient that will authenticate to your registry through Azure Active Directory
Uri endpoint = new Uri("https://myregistry.azurecr.io");
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });
```

Pour découvrir d’autres approches sur l’authentification avec [, à la fois localement et dans les environnements de déploiement, consultez le ][dotnet_identity]fichier Lisez-moi Azure Identity`DefaultAzureCredential`. Pour vous connecter à des registres dans des clouds Azure non publics, consultez les [informations de référence sur l’API][dotnet_docs].

Pour plus d’informations sur l’utilisation d’Azure AD avec Azure Container Registry, consultez la [vue d’ensemble de l’authentification](container-registry-authentication.md).

## <a name="examples"></a>Exemples

Chaque exemple part du principe qu’il existe une variable d’environnement `REGISTRY_ENDPOINT` ayant comme valeur une chaîne contenant le préfixe `https://` et le nom du serveur de connexion, par exemple « https://myregistry.azurecr.io  ».

Les exemples suivants utilisent des API asynchrones qui retournent une tâche. Des API synchrones sont également disponibles.

### <a name="list-repositories-asynchronously"></a>Lister les référentiels de manière asynchrone

Itérez au sein de la collection de référentiels dans le registre.

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="set-artifact-properties-asynchronously"></a>Définir des propriétés d’artefacts de manière asynchrone

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="delete-images-asynchronously"></a>Supprimer des images de manière asynchrone

```C# Snippet:ContainerRegistry_Tests_Samples_DeleteImageAsync
using System.Linq;
using Azure.Containers.ContainerRegistry;
using Azure.Identity;

// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Iterate through repositories
AsyncPageable<string> repositoryNames = client.GetRepositoryNamesAsync();
await foreach (string repositoryName in repositoryNames)
{
    ContainerRepository repository = client.GetRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    AsyncPageable<ArtifactManifestProperties> imageManifests =
        repository.GetManifestPropertiesCollectionAsync(orderBy: ArtifactManifestOrderBy.LastUpdatedOnDescending);

    // Delete images older than the first three.
    await foreach (ArtifactManifestProperties imageManifest in imageManifests.Skip(3))
    {
        RegistryArtifact image = repository.GetArtifact(imageManifest.Digest);
        Console.WriteLine($"Deleting image with digest {imageManifest.Digest}.");
        Console.WriteLine($"   Deleting the following tags from the image: ");
        foreach (var tagName in imageManifest.Tags)
        {
            Console.WriteLine($"        {imageManifest.RepositoryName}:{tagName}");
            await image.DeleteTagAsync(tagName);
        }
        await image.DeleteAsync();
    }
}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="get-started"></a>Bien démarrer

[Code source][java_source] | [Package (Maven)][java_package] | [Référence d’API][java_docs] | [Exemples][java_samples]

### <a name="currently-supported-environments"></a>Environnements actuellement pris en charge

* [Kit de développement Java (JDK)][jdk_link], version 8 ou ultérieure.

### <a name="include-the-package"></a>Inclure le package

[//]: # ({x-version-update-start;com.azure:azure-containers-containerregistry;current})
```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-containers-containerregistry</artifactId>
  <version>1.0.0-beta.3</version>
</dependency>
```
[//]: # ({x-version-update-end})

## <a name="authenticate-the-client"></a>Authentifier le client

La [bibliothèque Azure Identity][java_identity] fournit la prise en charge d’Azure Active Directory pour l’authentification.

Les exemples suivants partent du principe que vous avez une chaîne de point de terminaison de registre contenant le préfixe `https://` et le nom du serveur de connexion, par exemple « https://myregistry.azurecr.io  ».

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L31-L35 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();
```

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L39-L43 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryAsyncClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildAsyncClient();
```

Pour plus d’informations sur l’utilisation d’Azure AD avec Azure Container Registry, consultez la [vue d’ensemble de l’authentification](container-registry-authentication.md).

## <a name="examples"></a>Exemples

Chaque exemple part du principe qu’il existe une chaîne de point de terminaison de registre contenant le préfixe `https://` et le nom du serveur de connexion, par exemple « https://myregistry.azurecr.io  ».

### <a name="list-repository-names"></a>Lister les noms des référentiels

Itérez au sein de la collection de référentiels dans le registre.

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L47-L53 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();

client.listRepositoryNames().forEach(repository -> System.out.println(repository));
```

### <a name="set-artifact-properties"></a>Définir les propriétés des artefacts

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L119-L132 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

RegistryArtifact image = client.getArtifact(repositoryName, digest);

image.updateTagProperties(
    tag,
    new ArtifactTagProperties()
        .setWriteEnabled(false)
        .setDeleteEnabled(false));
```

### <a name="delete-images"></a>Supprimer des images

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L85-L113 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

final int imagesCountToKeep = 3;
for (String repositoryName : client.listRepositoryNames()) {
    final ContainerRepository repository = client.getRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    PagedIterable<ArtifactManifestProperties> imageManifests =
        repository.listManifestProperties(
            ArtifactManifestOrderBy.LAST_UPDATED_ON_DESCENDING,
            Context.NONE);

    imageManifests.stream().skip(imagesCountToKeep)
        .forEach(imageManifest -> {
            System.out.printf(String.format("Deleting image with digest %s.%n", imageManifest.getDigest()));
            System.out.printf("    This image has the following tags: ");

            for (String tagName : imageManifest.getTags()) {
                System.out.printf("        %s:%s", imageManifest.getRepositoryName(), tagName);
            }

            repository.getArtifact(imageManifest.getDigest()).delete();
        });
}
```

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="get-started"></a>Bien démarrer

[Code source][javascript_source] | [Package (npm)][javascript_package] | [Référence d’API][javascript_docs] | [Exemples][javascript_samples]

### <a name="currently-supported-environments"></a>Environnements actuellement pris en charge

- [Versions LTS de Node.js](https://nodejs.org/about/releases/)

Pour plus d’informations, consultez notre [politique de support](https://github.com/Azure/azure-sdk-for-js/blob/main/SUPPORT.md) .

### <a name="install-the-azurecontainer-registry-package"></a>Installez le package `@azure/container-registry`

Installez la bibliothèque de client Container Registry pour JavaScript avec `npm` :

```bash
npm install @azure/container-registry
```

## <a name="authenticate-the-client"></a>Authentifier le client

La [bibliothèque Azure Identity][javascript_identity] fournit la prise en charge d’Azure Active Directory pour l’authentification.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through Active Directory
const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
```

Pour plus d’informations sur l’utilisation d’Azure AD avec Azure Container Registry, consultez la [vue d’ensemble de l’authentification](container-registry-authentication.md).

## <a name="examples"></a>Exemples

Chaque exemple part du principe qu’il existe une variable d’environnement `CONTAINER_REGISTRY_ENDPOINT` ayant comme valeur une chaîne contenant le préfixe `https://` et le nom du serveur de connexion, par exemple « https://myregistry.azurecr.io  ».

### <a name="list-repositories-asynchronously"></a>Lister les référentiels de manière asynchrone

Itérez au sein de la collection de référentiels dans le registre.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  console.log("Listing repositories");
  const iterator = client.listRepositoryNames();
  for await (const repository of iterator) {
    console.log(`  repository: ${repository}`);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="set-artifact-properties-asynchronously"></a>Définir des propriétés d’artefacts de manière asynchrone

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient and RegistryArtifact to access image operations
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
  const image = client.getArtifact("library/hello-world", "v1");

  // Set permissions on the image's "latest" tag
  await image.updateTagProperties("latest", { canWrite: false, canDelete: false });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="delete-images-asynchronously"></a>Supprimer des images de manière asynchrone

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  // Iterate through repositories
  const repositoryNames = client.listRepositoryNames();
  for await (const repositoryName of repositoryNames) {
    const repository = client.getRepository(repositoryName);
    // Obtain the images ordered from newest to oldest by passing the `orderBy` option
    const imageManifests = repository.listManifestProperties({
      orderBy: "LastUpdatedOnDescending"
    });
    const imagesToKeep = 3;
    let imageCount = 0;
    // Delete images older than the first three.
    for await (const manifest of imageManifests) {
      imageCount++;
      if (imageCount > imagesToKeep) {
        const image = repository.getArtifact(manifest.digest);
        console.log(`Deleting image with digest ${manifest.digest}`);
        console.log(`  Deleting the following tags from the image:`);
        for (const tagName of manifest.tags) {
          console.log(`    ${manifest.repositoryName}:${tagName}`);
          image.deleteTag(tagName);
        }
        await image.delete();
      }
    }
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

::: zone-end

::: zone pivot="programming-language-python"

## <a name="get-started"></a>Bien démarrer

[Code source][python_source] | [Package (Pypi)][python_package] | [Référence d’API][python_docs] | [Exemples][python_samples]

### <a name="install-the-package"></a>Installer le package

Installez la bibliothèque de client Azure Container Registry pour Python avec [pip][pip_link] :

```bash
pip install --pre azure-containerregistry
```

## <a name="authenticate-the-client"></a>Authentifier le client

La [bibliothèque Azure Identity][python_identity] fournit la prise en charge d’Azure Active Directory pour l’authentification. `DefaultAzureCredential` part du principe que les variables d’environnement `AZURE_CLIENT_ID`, `AZURE_TENANT_ID` et `AZURE_CLIENT_SECRET` sont définies. Pour en savoir plus, consultez [Variables d’environnement Azure Identity](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#environment-variables).

```python
# Create a ContainerRegistryClient that will authenticate through Active Directory
from azure.containerregistry import ContainerRegistryClient
from azure.identity import DefaultAzureCredential

account_url = "https://mycontainerregistry.azurecr.io"
client = ContainerRegistryClient(account_url, DefaultAzureCredential())
```

## <a name="examples"></a>Exemples

Chaque exemple part du principe qu’il existe une variable d’environnement `CONTAINERREGISTRY_ENDPOINT` ayant comme valeur une chaîne contenant le préfixe `https://` et le nom du serveur de connexion, par exemple « https://myregistry.azurecr.io  ».

### <a name="list-tags-asynchronously"></a>Lister les étiquettes de façon asynchrone

Cet exemple part du principe que le registre a un référentiel `hello-world`.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class ListTagsAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def list_tags(self):
        # Create a new ContainerRegistryClient      
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        manifest = await client.get_manifest_properties("library/hello-world", "latest")
        print(manifest.repository_name + ": ")
        for tag in manifest.tags:
            print(tag + "\n")
```

### <a name="set-artifact-properties-asynchronously"></a>Définir des propriétés d’artefacts de manière asynchrone

Cet exemple part du principe que le registre a un référentiel `hello-world` avec une image étiquetée `v1`.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class SetImagePropertiesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def set_image_properties(self):
        # Create a new ContainerRegistryClient
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        audience = "https://management.azure.com"
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        # [START update_manifest_properties]
        # Set permissions on the v1 image's "latest" tag
        await client.update_manifest_properties(
            "library/hello-world",
            "latest",
            can_write=False,
            can_delete=False
        )
        # [END update_manifest_properties]
        # After this update, if someone were to push an update to "myacr.azurecr.io\hello-world:v1", it would fail.
        # It's worth noting that if this image also had another tag, such as "latest", and that tag did not have
        # permissions set to prevent reads or deletes, the image could still be overwritten. For example,
        # if someone were to push an update to "myacr.azurecr.io\hello-world:latest"
        # (which references the same image), it would succeed.
```

### <a name="delete-images-asynchronously"></a>Supprimer des images de manière asynchrone

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry import ManifestOrder
from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class DeleteImagesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def delete_images(self):
        # [START list_repository_names]   
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        async with client:
            async for repository in client.list_repository_names():
                print(repository)
                # [END list_repository_names]

                # [START list_manifest_properties]
                # Keep the three most recent images, delete everything else
                manifest_count = 0
                async for manifest in client.list_manifest_properties(repository, order_by=ManifestOrder.LAST_UPDATE_TIME_DESCENDING):
                    manifest_count += 1
                    if manifest_count > 3:
                        await client.delete_manifest(repository, manifest.digest)
                # [END list_manifest_properties]
```

::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un registre de conteneurs Azure, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](container-registry-get-started-portal.md#clean-up-resources)
* [Azure CLI](container-registry-get-started-azure-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque de client Azure Container Registry pour effectuer des opérations sur des images et des artefacts dans votre registre de conteneurs.

* Pour plus d’informations, consultez la documentation de référence sur l’API.

    * [.NET][dotnet_docs]
    * [Java][java_docs]
    * [JavaScript][javascript_docs]
    * [Python][python_docs]

* Apprenez-en davantage sur l’[API REST][rest_docs] Azure Container Registry.

[dotnet_source]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/src
[dotnet_package]: https://www.nuget.org/packages/Azure.Containers.ContainerRegistry/
[dotnet_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/samples
[dotnet_docs]: /dotnet/api/azure.containers.containerregistry
[rest_docs]: /rest/api/containerregistry/
[product_docs]:  container-registry-intro.md
[nuget]: https://www.nuget.org/
[dotnet_identity]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity/README.md
[javascript_identity]: https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md
[javascript_source]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/src
[javascript_package]: https://www.npmjs.com/package/@azure/container-registry
[javascript_docs]: /javascript/api/overview/azure/container-registry-readme
[jdk_link]: /java/azure/jdk/
[javascript_samples]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/samples
[java_source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/containerregistry/azure-containers-containerregistry/src
[java_package]: https://search.maven.org/artifact/com.azure/azure-containers-containerregistry
[java_docs]: /java/api/overview/azure/containers-containerregistry-readme
[java_identity]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/identity/azure-identity/README.md
[java_samples]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/containerregistry/azure-containers-containerregistry/src/samples/
[python_package]: https://pypi.org/project/azure-containerregistry/
[python_docs]: /python/api/overview/azure/containerregistry-readme
[python_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry/samples
[pip_link]: https://pypi.org
[python_identity]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity
[python_source]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry
