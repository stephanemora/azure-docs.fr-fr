---
title: Gérer des packages Python 3 dans Azure Automation
description: Cet article explique comment gérer des packages Python 3 (préversion) dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734299"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gérer des packages Python 3 (préversion) dans Azure Automation

Azure Automation vous permet d’exécuter des runbooks Python 3 (préversion) dans Azure et sur des runbooks Worker hybrides Linux. Pour aider à simplifier les runbooks, vous pouvez utiliser des packages Python afin d’importer les modules dont vous avez besoin. Cet article décrit comment gérer et utiliser les packages Python 3 (préversion) dans Azure Automation.

## <a name="import-packages"></a>Importer des packages

Dans votre compte Automation, sélectionnez **Packages Python** sous **Ressources partagées**. Sélectionnez **+ Ajouter un package Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Capture d’écran de la page des packages Python 3 montrant les packages Python 3 dans le menu de gauche et Ajouter un package Python 2 mis en évidence":::

Dans la page **Ajouter un package Python**, sélectionnez Python 3 comme **Version** et sélectionnez un package local à charger. Le package peut être un fichier **.whl** ou **.tar.gz**. Lorsque le package est sélectionné, sélectionnez **OK** pour le charger.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Capture d’écran montrant la page Ajouter un package Python 3 avec un fichier tar.gz chargé sélectionné":::

Une fois qu’un package a été importé, il est listé dans la page Packages Python de votre compte Automation, sous l’onglet **Packages Python 3 (préversion)** . Pour supprimer un package, intervenez sur sa page, sélectionnez-le, puis cliquez sur **Supprimer**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Capture d’écran montrant la page Packages Python 3 après l’importation d’un package":::

## <a name="import-packages-with-dependencies"></a>Importer des packages avec des dépendances

Azure Automation ne résout pas les dépendances des packages Python pendant le processus d’importation. Toutefois, il existe une méthode pour importer un package avec toutes ses dépendances.

### <a name="manually-download"></a>Téléchargement manuel

Sur un ordinateur Windows 64 bits où sont installés [Python 3.8](https://www.python.org/downloads/release/python-380/) et [pip](https://pip.pypa.io/en/stable/), exécutez la commande suivante pour télécharger un package et toutes ses dépendances :

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Une fois les packages téléchargés, vous pouvez les importer dans votre compte Automation.

## <a name="use-a-package-in-a-runbook"></a>Utiliser un package dans un runbook

Une fois le package importé, vous pouvez l’utiliser dans un runbook. Ajoutez le code suivant pour lister tous les groupes de ressources dans un abonnement Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Étapes suivantes

Pour préparer un runbook Python, consultez [Créer un runbook Python](learn/automation-tutorial-runbook-textual-python-3.md).
