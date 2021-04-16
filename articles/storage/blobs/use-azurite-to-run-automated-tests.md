---
title: Exécuter des tests automatisés en utilisant Azurite
titleSuffix: Azure Storage
description: Découvrez comment écrire des tests automatisés par rapport aux points de terminaison privés pour Stockage Blob Azure en utilisant Azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110947"
---
# <a name="run-automated-tests-by-using-azurite"></a>Exécuter des tests automatisés en utilisant Azurite

Découvrez comment écrire des tests automatisés par rapport aux points de terminaison privés pour Stockage Blob Azure en utilisant l’émulateur de stockage Azurite.

## <a name="run-tests-on-your-local-machine"></a>Exécuter des tests sur votre ordinateur local

1. Installez la version la plus récente de [Python](https://www.python.org/).

1. Installez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

1. Installez et exécutez [Azurite](../common/storage-use-azurite.md) :

   **Option 1 :** Utilisez npm pour installer, puis exécuter Azurite localement.

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Option 2 :** Utilisez Docker pour exécuter Azurite.

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Dans Explorateur Stockage Azure, sélectionnez **Lier à un émulateur local**.

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Capture d’écran d’Explorateur Stockage Azure se connectant à la source Stockage Azure.":::

1. Indiquez un **nom d’affichage** et un numéro de **port Blobs** pour connecter Azurite et utiliser Explorateur Stockage Azure pour gérer le stockage blob local.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Capture d’écran d’Explorateur Stockage Azure se liant à un émulateur local.":::

1. Créer un environnement Python virtuel

   ```bash
   python -m venv .venv
   ```

1. Créez un conteneur et initialisez les variables d’environnement. Utilisez un fichier [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) pour générer des tests. Voici un exemple de fichier conftest.py :

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > La valeur indiquée pour `AZURE_STORAGE_CONNECTION_STRING` est la valeur par défaut pour Azurite, il ne s’agit pas d’une clé privée.

1. Installez les dépendances figurant dans un fichier [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt).

   ```bash
   pip install -r requirements.txt
   ```

1. Exécutez les tests :

   ```bash
   python -m pytest ./tests
   ```

Après avoir exécuté les tests, vous pouvez voir les fichiers dans le stockage blob Azurite en utilisant Explorateur Stockage Azure.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Capture d’écran d’Explorateur Stockage Azure montrant des fichiers générés par les tests.":::

## <a name="run-tests-on-azure-pipelines"></a>Exécuter des tests sur Azure Pipelines

Après avoir exécuté les tests localement, assurez-vous que les tests passent sur [Azure Pipelines](/azure/devops/pipelines). Utilisez une image Docker Azurite comme agent hébergé sur Azure, ou utilisez npm pour installer Azurite. L’exemple d’Azure Pipelines suivant utilise npm pour installer Azurite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Après l’exécution des tests sur Azure Pipelines, une sortie semblable à celle-ci doit s’afficher :

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Capture d’écran des résultats des tests d’Azure Pipelines.":::

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser l’émulateur Azurite à des fins de développement local pour Stockage Azure](../common/storage-use-azurite.md)
- [Exemple : Utiliser Azurite pour exécuter des tests de stockage blob dans un pipeline Azure DevOps](https://github.com/Azure-Samples/automated-testing-with-azurite)
