---
title: 'Démarrage rapide : Créer un groupe d’administration avec Python'
description: Dans ce guide de démarrage rapide, vous allez utiliser Python pour créer un groupe d’administration afin d’organiser vos ressources dans une hiérarchie de ressources.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101653"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Démarrage rapide : Créer un groupe d’administration avec Python

Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Créez ces conteneurs pour construire une hiérarchie efficace utilisable avec [Azure Policy](../policy/overview.md) et les [contrôles d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](overview.md).

La création du premier groupe d’administration dans l’annuaire peut nécessiter jusqu’à 15 minutes. En effet, des processus s’exécutent la première fois pour configurer le service des groupes d’administration dans Azure pour votre annuaire. Vous recevez une notification quand le processus est terminé. Pour plus d’informations, consultez la [configuration initiale des groupes d’administration](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

- Tout utilisateur Azure AD dans le locataire peut créer un groupe d’administration sans l’autorisation d’écriture du groupe d’administration attribuée à cet utilisateur si la [protection de la hiérarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) n’est pas activée. Ce nouveau groupe d’administration devient alors un enfant du groupe d’administration racine ou le [groupe d’administration par défaut](./how-to/protect-resource-hierarchy.md#setting---default-management-group), et le rôle « Propriétaire » est attribué au créateur. Le service de groupe d’administration offre cette possibilité afin que les attributions de rôles ne soient pas nécessaires au niveau de la racine. Aucun utilisateur n’a accès au groupe d’administration racine lors de sa création. Pour éviter l’obstacle que constitue la recherche des administrateurs généraux Azure AD pour commencer à utiliser des groupes d’administration, nous autorisons la création des groupes d’administration initiaux au niveau racine.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Ajouter la bibliothèque Resource Graph

Pour permettre à Python de gérer des groupes d’administration, la bibliothèque doit être ajoutée. Cette bibliothèque fonctionne partout où Python peut être utilisé, notamment avec [bash sur Windows 10](/windows/wsl/install-win10) ou via une installation locale.

1. Vérifiez que la version la plus récente de Python est installée (**3.8** au minimum). Si ce n’est pas le cas, téléchargez-la à l’adresse [Python.org](https://www.python.org/downloads/).

1. Vérifiez que la version la plus récente d’Azure CLI est installée (**2.5.1** au minimum). Si ce n’est pas le cas, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI est requis pour permettre à Python d’utiliser **l’authentification basée sur l’interface CLI** dans les exemples suivants. Pour plus d’informations sur les autres options, consultez [S’authentifier à l’aide des bibliothèques de gestion Azure pour Python](/azure/developer/python/azure-sdk-authenticate).

1. S’authentifier par le biais d’Azure CLI.

   ```azurecli
   az login
   ```

1. Dans l’environnement Python de votre choix, installez les bibliothèques nécessaires pour les groupes d’administration :

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Si Python est installé pour tous les utilisateurs, cette commande doit être exécutée à partir d’une console avec élévation de privilèges.

1. Vérifiez que les bibliothèques ont été installées. `azure-mgmt-managementgroups` doit avoir la version **0.2.0** ou ultérieure, `azure-mgmt-resource` doit avoir la version **9.0.0** ou ultérieure, et `azure-cli-core` doit avoir la version **2.5.0** ou ultérieure.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Créer le groupe d’administration

1. Créez le script Python, puis enregistrez la source suivante sous `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Authentifiez-vous auprès d’Azure CLI avec `az login`.

1. Entrez la commande suivante dans le terminal :

   ```bash
   py mgCreate.py
   ```

Le résultat de la création du groupe d’administration s’affiche sur la console sous la forme d’un objet `LROPoller`.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez supprimer les bibliothèques installées de votre environnement Python, vous pouvez utiliser la commande suivante :

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un groupe d’administration pour organiser votre hiérarchie de ressources. Le groupe d’administration peut contenir des abonnements ou d’autres groupes d’administration.

Pour en savoir plus sur les groupes d’administration et sur la gestion de votre hiérarchie de ressources, consultez :

> [!div class="nextstepaction"]
> [Gérer vos ressources avec des groupes d’administration](./manage.md)