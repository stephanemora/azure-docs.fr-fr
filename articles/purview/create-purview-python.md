---
title: 'Démarrage rapide : Créer un compte Purview à l’aide de Python'
description: Créez un compte Azure Purview à l’aide de Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.openlocfilehash: 7a6d13da2ee3138e6dfc5eca4a5b75f454f90457
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212478"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Démarrage rapide : Créer un compte Purview à l’aide de Python

Dans ce guide de démarrage rapide, vous allez créer un compte Purview programmatiquement à l’aide de Python. La [référence Python pour Purview](/python/api/azure-mgmt-purview/) est disponible, mais cet article vous guide dans les étapes à suivre pour créer un compte avec Python.

Azure Purview est un service de gouvernance de données qui vous aide à gérer et régir votre paysage de données. En vous connectant aux données de vos sources locales, multiclouds et SaaS (software-as-a-service), Purview crée un mappage à jour de vos informations. Il identifie et classe les données sensibles, et fournit un lignage de bout en bout. Les consommateurs de données peuvent découvrir des données au sein de votre organisation, et les administrateurs de données sont en mesure d’auditer, de sécuriser et de garantir l’utilisation appropriée de vos données.

Pour plus d’informations sur Purview, [consultez notre page de présentation](overview.md). Pour plus d’informations sur le déploiement de Purview dans votre organisation, [consultez nos bonnes pratiques de déploiement](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="install-the-python-package"></a>Installer le package Python

1. Ouvrez un terminal ou une invite de commandes avec des privilèges d’administrateur.
2. D’abord, installez le package Python pour les ressources de gestion Azure :

    ```python
    pip install azure-mgmt-resource
    ```

3. Pour installer le package Python pour Purview, exécutez la commande suivante :

    ```python
    pip install azure-mgmt-purview
    ```

    Le [kit SDK Python pour Purview](https://github.com/Azure/azure-sdk-for-python) prend en charge Python 2.7, 3.3, 3.4, 3.5, 3.6 et 3.7.

4. Pour installer le package Python pour l’authentification d’identité Azure, exécutez la commande suivante :

    ```python
    pip install azure-identity
    ```

    > [!NOTE]
    > Le package « azure-identity » peut être en conflit avec « azure-cli » sur certaines dépendances communes. Si vous rencontrez un problème d’authentification, supprimez « azure-cli » et ses dépendances, ou utilisez un nouvel ordinateur sans installer le package « azure-cli ».

## <a name="create-a-purview-client"></a>Créer un client Purview

1. Créez un fichier nommé **purview.py**. Insérez les instructions suivantes pour ajouter des références aux espaces de noms.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Ajoutez le code suivant à la méthode **Main** qui crée une instance de la classe PurviewManagementClient. Vous utiliserez cet objet pour créer des comptes Purview, supprimer des comptes Purview, vérifier la disponibilité d’un nom et pour d’autres opérations de fournisseur de ressources.

    ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Créer un compte Purview

1. Ajoutez le code suivant à la méthode **Main** qui crée un **compte Purview**. Si votre groupe de ressources existe déjà, commentez la première instruction `create_or_update`.

   ```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
      ```

2. Maintenant, ajoutez l’instruction suivante pour appeler la méthode **main** lorsque le programme est exécuté :

   ```python
   # Start the main method
   main()
   ```

## <a name="full-script"></a>Script complet

Voici le code Python complet :

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Exécuter le code

Créez et démarrez l’application. La console affiche la progression de la création du compte Purview. Attendez la fin de cette opération.
Voici l'exemple de sortie :

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Vérifier la sortie

Accédez à la page **Comptes Purview** du portail Azure et vérifiez le compte créé à l'aide du code ci-dessus.

## <a name="delete-purview-account"></a>Supprimer un compte Purview

Pour supprimer un compte Purview, ajoutez le code suivant au programme, puis exécutez-le :

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Étapes suivantes

Le code présenté dans ce tutoriel crée un compte Purview et supprime un compte Purview. Vous pouvez maintenant télécharger le kit de développement logiciel (SDK) Python et découvrir d'autres actions de fournisseur de ressources que vous pouvez effectuer pour un compte Purview.

Lisez les articles suivants pour apprendre à naviguer dans Purview Studio, à créer une collection et à accorder l’accès à Purview.

* [Comment utiliser Purview Studio](use-purview-studio.md)
* [Création d'une collection](quickstart-create-collection.md)
* [Ajouter des utilisateurs à votre compte Azure Purview](catalog-permissions.md)