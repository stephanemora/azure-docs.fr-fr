---
title: Intégrer des dépendances et des bibliothèques tierces dans Azure Functions
description: Découvrez comment intégrer des fichiers ou des bibliothèques tierces.
ms.date: 4/6/2020
ms.topic: article
zone_pivot_groups: bring-third-party-dependency-programming-functions
ms.openlocfilehash: 3cd1139e8c733650acc879bf1d0d5d9efb2f6fb4
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716345"
---
# <a name="bring-dependencies-or-third-party-library-to-azure-functions"></a>Intégrer des dépendances et des bibliothèques tierces dans Azure Functions

Dans cet article, vous allez apprendre à intégrer des dépendances tierces dans vos applications de fonction. Les fichiers JSON, les fichiers binaires et les modèles Machine Learning sont des exemples de dépendances tierces. 

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Intégrer des dépendances via un projet de code Functions 
::: zone pivot="programming-language-python"
> [!div class="checklist"]
> * Intégrer des dépendances via le montage d’Azure FileShare
::: zone-end

## <a name="bring-in-dependencies-from-the-project-directory"></a>Intégrer des dépendances depuis le répertoire du projet
::: zone pivot="programming-language-python"
En matière d’intégration de dépendances, l’une des méthodes les plus simples consiste à placer les fichiers/artefacts en même temps que le code d’application Functions dans la structure de répertoire du projet Functions. Vous trouverez ci-dessous des exemples de répertoire dans un projet Functions Python :
```
<project_root>/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - dependencies/
 | | - dependency1
 | - .funcignore
 | - host.json
 | - local.settings.json
```
En plaçant les dépendances dans un dossier à l’intérieur du répertoire de projet d’application Functions, le dossier des dépendances est déployé en même temps que le code. Dès lors, votre code de fonction peut accéder aux dépendances dans le cloud via l’API du système de fichiers. 

### <a name="accessing-the-dependencies-in-your-code"></a>Accéder aux dépendances dans votre code

Voici un exemple d’accès et d’exécution de la dépendance ```ffmpeg``` placée dans le répertoire ```<project_root>/ffmpeg_lib```. 


```python
import logging

import azure.functions as func
import subprocess

FFMPEG_RELATIVE_PATH = "../ffmpeg_lib/ffmpeg"

def main(req: func.HttpRequest,
         context: func.Context) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    # context.function_directory returns the current directory in which functions is executed 
    ffmpeg_path = "/".join([str(context.function_directory), FFMPEG_RELATIVE_PATH])
    
    try:
        byte_output  = subprocess.check_output([ffmpeg_path, command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```
>[!NOTE]
> Vous serez peut-être amené à utiliser `chmod` pour fournir des droits `Execute` au fichier binaire ffmpeg dans un environnement Linux.
::: zone-end

::: zone pivot="programming-language-java"
En matière d’intégration de dépendances, l’une des méthodes les plus simples consiste à placer les fichiers/artefacts en même temps que le code d’application Functions dans la structure de répertoire du projet Functions. Vous trouverez ci-dessous des exemples de répertoire dans un projet Functions Java :
```
<project_root>/
 | - src/
 | | - main/java/com/function
 | | | - Function.java
 | | - test/java/com/function
 | - artifacts/
 | | - dependency1
 | - host.json
 | - local.settings.json
 | - pom.xml
```
Pour Java, vous devez inclure spécifiquement les artefacts dans le dossier build/target lors de la copie des ressources. Voici un exemple relatif à la façon de procéder dans Maven :

```xml
...
<execution>
    <id>copy-resources</id>
    <phase>package</phase>
    <goals>
        <goal>copy-resources</goal>
    </goals>
    <configuration>
        <overwrite>true</overwrite>
        <outputDirectory>${stagingDirectory}</outputDirectory>
        <resources>
            <resource>
                <directory>${project.basedir}</directory>
                <includes>
                    <include>host.json</include>
                    <include>local.settings.json</include>
                    <include>artifacts/**</include>
                </includes>
            </resource>
        </resources>
    </configuration>
</execution>
...
```
En plaçant les dépendances dans un dossier à l’intérieur du répertoire de projet d’application Functions, le dossier des dépendances est déployé en même temps que le code. Dès lors, votre code de fonction peut accéder aux dépendances dans le cloud via l’API du système de fichiers. 

### <a name="accessing-the-dependencies-in-your-code"></a>Accéder aux dépendances dans votre code

Voici un exemple d’accès et d’exécution de la dépendance ```ffmpeg``` placée dans le répertoire ```<project_root>/ffmpeg_lib```. 


```java
public class Function {
    final static String BASE_PATH = "BASE_PATH";
    final static String FFMPEG_PATH = "/artifacts/ffmpeg/ffmpeg.exe";
    final static String HELP_FLAG = "-h";
    final static String COMMAND_QUERY = "command";

    @FunctionName("HttpExample")
    public HttpResponseMessage run(
            @HttpTrigger(
                name = "req",
                methods = {HttpMethod.GET, HttpMethod.POST},
                authLevel = AuthorizationLevel.ANONYMOUS)
                HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) throws IOException{
        context.getLogger().info("Java HTTP trigger processed a request.");
            
        // Parse query parameter
        String flags = request.getQueryParameters().get(COMMAND_QUERY);
        
        if (flags == null || flags.isBlank()) {
            flags = HELP_FLAG;
        }

        Runtime rt = Runtime.getRuntime();
        String[] commands = { System.getenv(BASE_PATH) + FFMPEG_PATH, flags};
        Process proc = rt.exec(commands);
        
        BufferedReader stdInput = new BufferedReader(new 
        InputStreamReader(proc.getInputStream()));
   
        String out = stdInput.lines().collect(Collectors.joining("\n"));
        if(out.isEmpty()) {
            BufferedReader stdError = new BufferedReader(new 
                InputStreamReader(proc.getErrorStream()));
            out = stdError.lines().collect(Collectors.joining("\n"));
        }
        return request.createResponseBuilder(HttpStatus.OK).body(out).build();

    }
```
>[!NOTE]
> Pour obtenir cet extrait de code à utiliser dans Azure, vous devez spécifier un paramètre d’application personnalisé « BASE_PATH » avec la valeur « /home/site/wwwroot ».
::: zone-end


::: zone pivot="programming-language-python"
## <a name="bring-dependencies-by-mounting-a-file-share"></a>Placer des dépendances en montant un partage de fichiers

Lors de l’exécution de votre application de fonction sur Linux, il existe une autre méthode pour intégrer des dépendances tierces. Functions vous permet de monter un partage de fichiers hébergé dans Azure Files. Envisagez cette approche lorsque vous souhaitez découpler des dépendances ou des artefacts de votre code d’application.

Pour commencer, vous devez créer un compte Stockage Azure. Dans ce compte, vous devez également créer un partage de fichiers dans Azure Files. Pour créer ces ressources, suivez ce [guide](../storage/files/storage-how-to-use-files-portal.md).

Une fois le compte de stockage et le partage de fichiers créés, utilisez la commande [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) pour attacher le partage de fichiers à votre application de fonction, comme illustré dans l’exemple suivant.

```console
az webapp config storage-account add \
  --name < Function-App-Name > \
  --resource-group < Resource-Group > \
  --subscription < Subscription-Id > \
  --custom-id < Unique-Custom-Id > \
  --storage-type AzureFiles \
  --account-name < Storage-Account-Name > \
  --share-name < File-Share-Name >  \
  --access-key < Storage-Account-AccessKey > \
  --mount-path </path/to/mount>
```



|       Indicateur    |  Valeur     |
| ------------- | ---------------------------------- | 
| custom-id      |  Toute chaîne unique |
| storage-type      |  Actuellement, seul Azure Files est pris en charge |
| share-name      |  Partage préexistant |
| mount-path     |   Chemin d’accès auquel le partage sera accessible à l’intérieur du conteneur. La valeur doit être au format `/dir-name` et ne peut commencer par `/home`. |

D’autres commandes de modification/suppression de la configuration du partage de fichiers sont disponibles [ici](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-update).


### <a name="uploading-the-dependencies-to-azure-files"></a>Charger les dépendances dans Azure Files

L’une des options permettant de charger votre dépendance dans Azure Files consiste à utiliser le portail Azure. Consultez ce [guide](../storage/files/storage-how-to-use-files-portal.md#upload-a-file) pour obtenir des instructions relatives au chargement des dépendances à l’aide du portail. Parmi les autres options de chargement des dépendances dans Azure Files figurent également [Azure CLI](../storage/files/storage-how-to-use-files-cli.md#upload-a-file) et [PowerShell](../storage/files/storage-how-to-use-files-powershell.md#upload-a-file).


### <a name="accessing-the-dependencies-in-your-code"></a>Accéder aux dépendances dans votre code

Une fois vos dépendances chargées dans le partage de fichiers, vous pouvez y accéder à partir de votre code. Le partage monté est disponible au niveau du *chemin de montage* spécifié, par exemple ```/path/to/mount```. Vous pouvez accéder au répertoire cible à l’aide des API du système de fichiers.

L’exemple suivant montre le code de déclencheur HTTP qui accède à la bibliothèque `ffmpeg`, stockée dans un partage de fichiers monté.

```python
import logging

import azure.functions as func
import subprocess 

FILE_SHARE_MOUNT_PATH = os.environ['FILE_SHARE_MOUNT_PATH']
FFMPEG = "ffmpeg"

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    try:
        byte_output  = subprocess.check_output(["/".join(FILE_SHARE_MOUNT_PATH, FFMPEG), command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```

Lorsque vous déployez ce code dans une application de fonction dans Azure, vous devez [créer un paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) avec le nom de clé `FILE_SHARE_MOUNT_PATH` et la valeur du chemin d’accès au partage de fichiers monté, qui, dans cet exemple, est `/azure-files-share`. Pour effectuer un débogage local, vous devez renseigner `FILE_SHARE_MOUNT_PATH` avec le chemin d’accès du fichier dans lequel vos dépendances sont stockées sur votre ordinateur local. Voici un exemple pour définir `FILE_SHARE_MOUNT_PATH` avec `local.settings.json` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "FILE_SHARE_MOUNT_PATH" : "PATH_TO_LOCAL_FFMPEG_DIR"
  }
}

```
::: zone-end



## <a name="next-steps"></a>Étapes suivantes

+ [Guide du développeur Python sur Azure Functions](functions-reference-python.md)
+ [Guide des développeurs Java sur Azure Functions](functions-reference-java.md)
+ [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
