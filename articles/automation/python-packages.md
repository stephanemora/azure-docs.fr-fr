---
title: Gérer des packages Python 2 dans Azure Automation
description: Cet article décrit comment gérer des packages Python 2 dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b53e07d6086f2a02fd1bbd158ffc09dc95b0c377
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796377"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gérer des packages Python 2 dans Azure Automation

Azure Automation vous permet d’exécuter des runbooks Python 2 sur Azure et sur les Runbooks Worker hybrides Linux. Pour aider à simplifier les runbooks, vous pouvez utiliser des packages Python afin d’importer les modules dont vous avez besoin. Cet article décrit comment gérer et utiliser les packages Python dans Azure Automation.

## <a name="import-packages"></a>Importer des packages

Dans votre compte Automation, sélectionnez **Packages Python 2** sous **Ressources partagées**. Cliquez sur **+ Add a Python 2 package** (+ Ajouter un package Python 2).

![Ajouter un package Python](media/python-packages/add-python-package.png)

Sur la page **Ajouter un package Python 2**, sélectionnez un package local à charger. Le package peut être un fichier `.whl` ou `.tar.gz`. Une fois sélectionné, cliquez sur **OK** pour charger le package.

![Ajouter un package Python](media/python-packages/upload-package.png)

Une fois qu’un package a été importé, il est répertorié dans le **packages Python 2** page dans votre compte Automation. Pour supprimer un package, intervenez sur sa page, sélectionnez-le, puis choisissez **Supprimer**.

![Liste des packages](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importer des packages avec des dépendances

Azure automation ne résout pas les dépendances de packages python pendant le processus d’importation. Il existe deux méthodes pour importer un package avec toutes ses dépendances. Uniquement une des étapes suivantes doit être utilisé pour importer les packages dans votre compte Automation.

### <a name="manually-download"></a>Télécharger manuellement

Sur Windows 64 bits de l’ordinateur avec [Python 2.7](https://www.python.org/downloads/release/latest/python2) et [pip](https://pip.pypa.io/en/stable/) installé, exécutez la commande suivante pour télécharger un package et toutes ses dépendances :

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Une fois que les packages sont téléchargés, vous pouvez les importer dans votre compte automation.

### <a name="runbook"></a>Runbook

Importer le runbook python [importation Python 2 des packages à partir de pypi dans un compte Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) à partir de la galerie dans votre compte Automation. Assurez-vous que les paramètres d’exécution sont définies sur **Azure** et démarrer le runbook avec les paramètres. Le runbook nécessite un compte d’identification du compte Automation à utiliser. Pour chaque paramètre Veillez vous démarrer avec le commutateur comme indiqué dans la liste et l’image suivante :

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Liste des packages](media/python-packages/import-python-runbook.png)

Le runbook, vous pouvez spécifier quel package à télécharger, par exemple, `Azure` (le quatrième paramètre) télécharge tous les modules Azure et toutes ses dépendances, qui est d’environ 105.

Une fois le runbook terminé, vous pouvez vérifier le **packages Python 2** page sous **ressources partagées** dans votre compte Automation pour vérifier qu’ils le package a été correctement importé.

## <a name="use-a-package-in-a-runbook"></a>Utiliser un package dans un runbook

Une fois que vous avez importé un package, vous pouvez maintenant l’utiliser dans un runbook. L’exemple suivant utilise le [package de l’utilitaire Azure Automation](https://github.com/azureautomation/azure_automation_utility). Ce package facilite l’utilisation de Python avec Azure Automation. Pour utiliser ce package, suivez les instructions du référentiel GitHub et ajoutez-le au runbook en utilisant par exemple `from azure_automation_utility import get_automation_runas_credential` pour importer la fonction de récupération du compte d’identification.

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
