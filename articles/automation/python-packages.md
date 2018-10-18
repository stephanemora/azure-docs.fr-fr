---
title: Gérer des packages Python 2 dans Azure Automation
description: Cet article décrit comment gérer des packages Python 2 dans Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5974a8e622ca0969b2a7b5ee9500766ac95398c9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987220"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gérer des packages Python 2 dans Azure Automation

Azure Automation vous permet d’exécuter des runbooks Python 2 sur Azure et sur les Runbooks Worker hybrides Linux. Pour aider à simplifier les runbooks, vous pouvez utiliser des packages Python afin d’importer les modules dont vous avez besoin. Cet article décrit comment gérer et utiliser les packages Python dans Azure Automation.

## <a name="import-packages"></a>Importer des packages

Dans votre compte Automation, sélectionnez **Packages Python 2** sous **Ressources partagées**. Cliquez sur **+ Add a Python 2 package** (+ Ajouter un package Python 2).

![Ajouter un package Python](media/python-packages/add-python-package.png)

Sur la page **Ajouter un package Python 2**, sélectionnez un package local à charger. Le package peut être un fichier `.whl` ou `.tar.gz`. Une fois sélectionné, cliquez sur **OK** pour charger le package.

![Ajouter un package Python](media/python-packages/upload-package.png)

Une fois qu’un package a été importé, il est répertorié sur la page **Packages Python 2** de votre compte Automation. Pour supprimer un package, intervenez sur sa page, sélectionnez-le, puis choisissez **Supprimer**.

![Liste des packages](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Utiliser un package dans un runbook

À partir du moment où vous avez importé un package, vous pouvez l’utiliser dans un runbook. L’exemple suivant utilise le [package de l’utilitaire Azure Automation](https://github.com/azureautomation/azure_automation_utility). Ce package facilite l’utilisation de Python avec Azure Automation. Pour utiliser ce package, suivez les instructions du référentiel GitHub et ajoutez-le au runbook en utilisant par exemple `from azure_automation_utility import get_automation_runas_credential` pour importer la fonction de récupération du compte d’identification.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Développer et tester les runbooks en mode hors connexion

Pour développer et tester vos runbooks Python 2 en mode hors connexion, utilisez par exemple le module [Azure Automation python emulated assets](https://github.com/azureautomation/python_emulated_assets) (Ressources émulées Python dans Azure Automation) sur GitHub. Ce module vous permet de référencer vos ressources partagées telles que les informations d’identification, variables, connexions et certificats.

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec les runbooks Python 2, voir [Mon premier runbook Python 2](automation-first-runbook-textual-python2.md)