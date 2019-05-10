---
title: Didacticiel - Créer un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Découvrez comment créer un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 5bc71a2d0f29fed163fb5c93ebd27df7f66a1325
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079474"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Didacticiel : Créer un cluster Azure Red Hat OpenShift

Ce tutoriel est la première partie d’une série d’étapes. Vous allez apprendre à créer un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI, puis à le mettre à l’échelle et le supprimer pour nettoyer les ressources.

Dans la première partie de la série, vous découvrirez comment :

> [!div class="checklist"]
> * Créer un cluster Azure Red Hat OpenShift

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster Azure Red Hat OpenShift
> * [Mettre à l’échelle un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Supprimer un cluster Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

Vérifiez que vous avez bien [configuré votre environnement de développement](howto-setup-environment.md), à savoir :
- Installé la dernière version de l’interface CLI
- Créé un locataire
- Créé un objet Azure Application
- Créé un utilisateur Active Directory pour vous connecter aux applications exécutées sur le cluster.

## <a name="step-1-sign-in-to-azure"></a>Étape 1 : Connexion à Azure

Si vous exécutez Azure CLI localement, ouvrez un interpréteur de commandes Bash et exécutez `az login` pour vous connecter à Azure.

```bash
az login
```

 Si vous avez accès à plusieurs abonnements, exécutez `az account set -s {subscription ID}` en remplaçant `{subscription ID}` par l’abonnement à utiliser.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Étape 2 : Créer un cluster Azure Red Hat OpenShift

Dans votre fenêtre de commande Bash, définissez les variables suivantes :

> [!IMPORTANT]
> Le nom de votre cluster doit être en minuscules pour que la création fonctionne.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Utilisez le même nom de cluster que celui que vous avez choisi à l’étape 6 de [Créer une inscription de nouvelle application](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Choisissez un emplacement pour créer votre cluster. Pour obtenir une liste des régions Azure qui prennent en charge OpenShift dans Azure, voir [Régions prises en charge](supported-resources.md#azure-regions). Par exemple : `LOCATION=eastus`.

Définissez `FQDN` sur le nom complet de votre cluster. Ce nom est composé du nom du cluster, de l’emplacement et de l’élément `.cloudapp.azure.com` ajouté à la fin. Il s’agit du même nom que l’URL de connexion que vous avez créée à l’étape 6 de [Créer une inscription de nouvelle application](howto-aad-app-configuration.md#create-a-new-app-registration). Par exemple :   

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Définissez `APPID` sur la valeur que vous avez enregistrée à l’étape 9 de [Créer une inscription de nouvelle application](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Définissez `SECRET` sur la valeur que vous avez enregistrée à l’étape 6 de [Créer une clé secrète client](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Définissez `TENANT` sur la valeur d’ID de locataire que vous avez enregistrée à l’étape 7 de [Créer un nouveau locataire](howto-create-tenant.md#create-a-new-azure-ad-tenant).  

```bash
TENANT=<tenant ID>
```

Créez le groupe de ressources du cluster. Exécutez la commande suivante à partir de l’interpréteur de commandes Bash que vous avez utilisé pour définir les variables ci-dessus :

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Facultatif : Connecter le réseau virtuel du cluster à un réseau virtuel existant

Si vous n’avez pas besoin de connecter le réseau virtuel du cluster que vous créez à un réseau virtuel existant, ignorez cette étape.

Commencez par obtenir l’identificateur du réseau virtuel existant. L’identificateur aura la forme suivante : `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Si vous ne connaissez pas le nom du réseau ou le groupe de ressources auquel le réseau virtuel existant appartient, accédez au [panneau des réseaux virtuels](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) et cliquez sur votre réseau virtuel. La page des réseaux virtuels s’affiche et propose une liste contenant le nom du réseau ainsi que le groupe de ressources dont il fait partie.

Définissez une variable VNET_ID à l’aide de la commande CLI suivante dans un interpréteur de commandes BASH :

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Par exemple : `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Création du cluster

Vous êtes prêt à créer un cluster.

 Si vous ne connectez pas le réseau virtuel du cluster à un réseau virtuel existant, omettez le paramètre `--vnet-peer-id $VNET_ID` final dans l’exemple suivant.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Au bout de quelques minutes, la commande `az openshift create` est terminée et renvoie une réponse JSON contenant les détails relatifs à votre cluster.

> [!NOTE]
> Si vous obtenez une erreur indiquant que le nom d’hôte n’est pas disponible, cela peut signifier que le nom de votre cluster n’est pas unique. Essayez de supprimer votre inscription d’application initiale et recommencez les étapes de [Créer une inscription de nouvelle application] (howto-aad-app-configuration.md#create-a-new-app-registration) (ignorez la dernière étape consistant à créer un nouvel utilisateur, puisque vous en avez déjà créé un) avec un autre nom de cluster.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Étape 3 : Se connecter à la console OpenShift

Vous êtes désormais prêt à vous connecter à la console OpenShift pour votre nouveau cluster. La [console web OpenShift](https://docs.openshift.com/dedicated/architecture/infrastructure_components/web_console.html) vous permet de visualiser, de parcourir et de gérer le contenu de vos projets OpenShift.

Nous allons nous connecter en tant que [nouvel utilisateur Azure AD](howto-aad-app-configuration.md#create-a-new-active-directory-user) que vous avez créé pour le test. Pour cela, vous devez utiliser une nouvelle instance de navigateur qui n’a pas mis en cache l’identité que vous utilisez habituellement pour vous connecter au portail Azure.

1. Ouvrez une fenêtre en mode *navigation privée* (Chrome) ou *InPrivate* (Microsoft Edge).
2. Accédez à l’URL de connexion créée à l’étape 6 de [Créer une inscription de nouvelle application](howto-aad-app-configuration.md#create-a-new-app-registration). Par exemple, https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> La console OpenShift utilise un certificat auto-signé.
> Lorsque l’invite du navigateur apparaît, ignorez l’avertissement et acceptez le certificat « non approuvé ».

Connectez-vous avec le nom d’utilisateur et le mot de passe créés dans [Créer un nouvel utilisateur Active Directory](howto-aad-app-configuration.md#create-a-new-active-directory-user). Lorsque la boîte de dialogue **Autorisations requises** apparaît, sélectionnez **Consentement pour le compte de votre organisation**, puis **Accepter**.

Vous êtes désormais connecté à la console du cluster.

[Capture d’écran de la console de cluster OpenShift](./media/aro-console.png)

 Pour en savoir plus sur l’[utilisation de la console OpenShift](https://docs.openshift.com/dedicated/getting_started/developers_console.html) pour créer des images, consultez la documentation [Red Hat OpenShift](https://docs.openshift.com/dedicated/welcome/index.html).

## <a name="step-4-install-the-openshift-cli"></a>Étape 4 : Installer l’interface CLI OpenShift

L’[interface CLI OpenShift](https://docs.openshift.com/dedicated/cli_reference/get_started_cli.html) (ou *Outils OC*) propose des commandes pour gérer vos applications et utilitaires de niveau inférieur pour interagir avec les différents composants de votre cluster OpenShift.

Dans la console OpenShift, cliquez sur le point d’interrogation dans le coin supérieur droit à côté de votre nom de connexion et sélectionnez **Outils de ligne de commande**.  Suivez le lien **Dernière version** pour télécharger et installer l’interface CLI OC prise en charge pour Linux, MacOS ou Windows.

> [!NOTE]
> Si vous ne voyez pas l’icône de point d’interrogation dans le coin supérieur droit, sélectionnez *Catalogue des services* ou *Console de l’application* dans le menu déroulant situé en haut à gauche.
>
> Vous pouvez également [télécharger l’interface CLI OC](https://www.okd.io/download.html) directement.

La page **Outils de ligne de commande** fournit une commande sous la forme `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Cliquez sur le bouton *Copier dans le Presse-papiers* pour copier cette commande.  Dans une fenêtre de terminal, [définissez votre chemin d’accès](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) de manière à inclure votre installation locale des outils OC. Connectez-vous ensuite au cluster à l’aide de la commande CLI OC que vous avez copiée.

Si vous n’avez pas pu obtenir la valeur du jeton à l’aide des étapes ci-dessus, obtenez la valeur de jeton à partir de : `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un cluster Azure Red Hat OpenShift

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Mettre à l’échelle un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)