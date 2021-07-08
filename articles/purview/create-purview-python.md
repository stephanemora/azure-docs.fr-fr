---
title: "Démarrage rapide : Créer un compte Purview à l'aide de Python"
description: Créez un compte Azure Purview à l'aide de Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: ec1f8890d9626ca1ed96f538bd464da7c30caf17
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983785"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Démarrage rapide : Créer un compte Purview à l'aide de Python

Suivez ce guide de démarrage rapide pour créer un compte Purview à l'aide de Python. 

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Votre propre [locataire Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Votre compte doit avoir l’autorisation de créer des ressources dans l’abonnement.

* Si **Azure Policy** empêche toutes les applications de créer un **compte de stockage** et un **espace de noms EventHub**, vous devez introduire une exception de stratégie au moyen d’une étiquette pouvant être renseignée lors du processus de création d’un compte Purview. La raison principale vient de ce que, pour chaque compte Purview créé, un groupe de ressources managé doit être créé, et dans ce groupe de ressources, un compte de stockage et un espace de noms EventHub. Pour plus d'informations, consultez [Créer un portail de catalogues](create-catalog-portal.md).


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
    > Le package « azure-identity » peut être en conflit avec « azure-cli » sur certaines dépendances communes. Si vous rencontrez un problème d’authentification, supprimez « azure-cli » et ses dépendances, ou utilisez un nouvel ordinateur sans installer le package « azure-cli » pour le faire fonctionner.
    
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

2. Ajoutez le code suivant à la méthode **Main** qui crée une instance de la classe PurviewManagementClient. Vous utiliserez cet objet pour créer un compte Purview, supprimer le compte Purview, vérifier la disponibilité d'un nom et d'autres opérations de fournisseur de ressources.
 
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

Créez et démarrez l’application, puis vérifiez l’exécution du pipeline.

La console affiche la progression de la création de la fabrique de données, du service lié, des jeux de données, du pipeline et de l’exécution du pipeline. Patientez jusqu’à l’affichage des détails de l’exécution de l’activité de copie avec la taille des données lues/écrites. Utilisez ensuite des outils comme l’[explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que les objets blob sont copiés dans « outputBlobPath » depuis « inputBlobPath » comme vous l’avez spécifié dans les variables.

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

Pour supprimer un compte Purview, ajoutez le code suivant au programme :

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Étapes suivantes

Le code présenté dans ce tutoriel crée un compte Purview et supprime un compte Purview. Vous pouvez maintenant télécharger le kit de développement logiciel (SDK) Python et découvrir d'autres actions de fournisseur de ressources que vous pouvez effectuer pour un compte Purview.

Passez à l’article suivant pour savoir comment autoriser les utilisateurs à accéder à votre compte Azure Purview. 

> [!div class="nextstepaction"]
> [Ajouter des utilisateurs à votre compte Azure Purview](catalog-permissions.md)
