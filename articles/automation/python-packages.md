---
title: Gérer des packages Python 2 dans Azure Automation
description: Cet article décrit comment gérer des packages Python 2 dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f20865b92df2a197410f209cf921c5e573723286
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850191"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gérer des packages Python 2 dans Azure Automation

Azure Automation vous permet d’exécuter des runbooks Python 2 sur Azure et sur les Runbooks Worker hybrides Linux. Pour aider à simplifier les runbooks, vous pouvez utiliser des packages Python afin d’importer les modules dont vous avez besoin. Cet article décrit comment gérer et utiliser les packages Python dans Azure Automation.

## <a name="import-packages"></a>Importer des packages

Dans votre compte Automation, sélectionnez **Packages Python 2** sous **Ressources partagées**. Cliquez sur **+ Add a Python 2 package** (+ Ajouter un package Python 2).

![Ajouter un package Python](media/python-packages/add-python-package.png)

Sur la page **Ajouter un package Python 2**, sélectionnez un package local à charger. Le package peut être un fichier `.whl` ou `.tar.gz`. Une fois sélectionné, cliquez sur **OK** pour charger le package.

![Ajouter un package Python](media/python-packages/upload-package.png)

Une fois qu’un package a été importé, il est listé dans la page **Packages Python 2** de votre compte Automation. Pour supprimer un package, intervenez sur sa page, sélectionnez-le, puis choisissez **Supprimer**.

![Liste des packages](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importer des packages avec des dépendances

Azure Automation ne résout pas les dépendances de packages Python pendant le processus d’importation. Il existe deux méthodes pour importer un package avec toutes ses dépendances. Seule l’une des étapes suivantes doit être utilisée pour importer les packages dans votre compte Automation.

### <a name="manually-download"></a>Téléchargement manuel

Sur un ordinateur Windows 64 bits où sont installés [Python 2.7](https://www.python.org/downloads/release/latest/python2) et [pip](https://pip.pypa.io/en/stable/), exécutez la commande suivante pour télécharger un package et toutes ses dépendances :

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Une fois les packages téléchargés, vous pouvez les importer dans votre compte Automation.

### <a name="runbook"></a>Runbook

Importez le runbook Python [Import Python 2 packages from pypi into Azure Automation account](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) dans votre compte Automation, à partir de la galerie. Vérifiez que les paramètres d’exécution sont définis sur **Azure**, puis démarrez le runbook avec les paramètres. Pour que le compte Automation fonctionne, le runbook a besoin d’un compte d’identification. Veillez à démarrer chaque paramètre avec le commutateur, comme indiqué dans la liste et la capture d’écran suivantes :

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Liste des packages](media/python-packages/import-python-runbook.png)

Le runbook vous permet de spécifier le package à télécharger. Par exemple, `Azure` (le quatrième paramètre) télécharge tous les modules Azure et toutes ses dépendances, qui sont au nombre de 105 environ.

Une fois le runbook terminé, vous pouvez consulter la page **Packages Python 2** située sous **Ressources partagées** dans votre compte Automation, pour vérifier que le package a bien été importé.

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
