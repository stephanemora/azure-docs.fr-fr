---
title: Tutoriel - Créer un cluster Azure Red Hat OpenShift
description: Découvrez comment créer un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 0e6aecccc19572ee980feb4d816fae1f2b0101b7
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274886"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutoriel : Créer un cluster Azure Red Hat OpenShift

Ce tutoriel est la première partie d’une série d’étapes. Vous allez apprendre à créer un cluster Microsoft Azure Red Hat OpenShift à l’aide d’Azure CLI, puis à le mettre à l’échelle et le supprimer pour nettoyer les ressources.

Dans la première partie de la série, vous découvrirez comment :

> [!div class="checklist"]
> * Créer un cluster Azure Red Hat OpenShift

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster Azure Red Hat OpenShift
> * [Mettre à l'échelle un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Supprimer un cluster Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Conditions préalables requises

> [!IMPORTANT]
> Ce tutoriel nécessite Azure CLI version 2.0.65.

Avant de commencer ce tutoriel :

Vérifiez que vous avez bien [configuré votre environnement de développement](howto-setup-environment.md), à savoir :
- Installation de la dernière version de l’interface CLI (2.0.65 ou ultérieur)
- Création d’un locataire si vous n’en avez pas déjà un
- Création d’un objet Azure Application si vous n’en avez pas déjà un
- Création d’un groupe de sécurité
- Création d’un utilisateur Active Directory pour vous connecter au cluster

## <a name="step-1-sign-in-to-azure"></a>Étape 1 : Connexion à Azure

Si vous exécutez Azure CLI localement, ouvrez un interpréteur de commandes Bash et exécutez `az login` pour vous connecter à Azure.

```bash
az login
```

 Si vous avez accès à plusieurs abonnements, exécutez `az account set -s {subscription ID}` en remplaçant `{subscription ID}` par l’abonnement que vous souhaitez utiliser.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Étape 2 : Créer un cluster Azure Red Hat OpenShift

Dans une fenêtre de commande Bash, définissez les variables suivantes :

> [!IMPORTANT]
> Choisissez pour le cluster un nom unique et tout en minuscules, sinon la création du cluster échoue.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Choisissez un emplacement pour créer votre cluster. Pour obtenir une liste des régions Azure qui prennent en charge OpenShift dans Azure, voir [Régions prises en charge](supported-resources.md#azure-regions). Par exemple : `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Définissez `APPID` sur la valeur que vous avez enregistrée à l’étape 5 de [Créer une inscription d’application Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Définissez « GROUPID » sur la valeur que vous avez enregistrée à l’étape 10 de [Créer un groupe de sécurité Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Définissez `SECRET` sur la valeur que vous avez enregistrée à l’étape 8 de [Créer un secret client](howto-aad-app-configuration.md#create-a-client-secret).

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

Si vous n’avez pas besoin de connecter le réseau virtuel du cluster que vous créez à un réseau virtuel existant via le peering, ignorez cette étape.

En cas de peering à un réseau se trouvant en dehors de l’abonnement par défaut, alors, dans cet abonnement, vous devrez également inscrire le fournisseur Microsoft.ContainerService. Pour ce faire, exécutez la commande ci-dessous dans cet abonnement. Sinon, si le réseau virtuel dont vous effectuez le peering se trouve dans le même abonnement, vous pouvez ignorer l’étape d’enregistrement.

`az provider register -n Microsoft.ContainerService --wait`

Commencez par obtenir l’identificateur du réseau virtuel existant. L’identificateur aura la forme suivante : `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Si vous ne connaissez pas le nom du réseau ou le groupe de ressources auquel le réseau virtuel existant appartient, accédez au [panneau des réseaux virtuels](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) et cliquez sur votre réseau virtuel. La page des réseaux virtuels s’affiche et propose une liste contenant le nom du réseau ainsi que le groupe de ressources dont il fait partie.

Définissez une variable VNET_ID à l’aide de la commande CLI suivante dans un interpréteur de commandes BASH :

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Par exemple : `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Facultatif : Connecter le cluster à Azure Monitor

Tout d’abord, obtenez l’identificateur de l’espace de travail Log Analytics **existant**. L’identificateur aura la forme suivante :

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Si vous ne connaissez pas le nom de l’espace de travail Log Analytics ou le groupe de ressources auquel il appartient, accédez à [	Espace de travail Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) et cliquez sur vos espaces de travail Log Analytics. La page de l’espace de travail Log Analytics apparaît et indique le nom de l’espace de travail et le groupe de ressources auquel il appartient.

_Pour créer un espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics](../azure-monitor/learn/quick-create-workspace-cli.md)_ .

Définissez une variable WORKSPACE_ID à l’aide de la commande CLI suivante dans un interpréteur de commandes BASH :

```bash
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Créer le cluster

Vous êtes prêt à créer un cluster. La procédure ci-après crée le cluster dans le locataire Azure AD spécifié, puis définit l’objet d’application Azure AD et le secret à utiliser comme principal de sécurité ainsi que le groupe de sécurité qui contient les membres disposant d’un accès administrateur au cluster.

> [!IMPORTANT]
> Avant de créer le cluster, vérifiez que vous avez correctement ajouté les autorisations appropriées pour l’application Azure AD comme [expliqué ici](howto-aad-app-configuration.md#add-api-permissions).

Si vous n’appairez **pas** votre cluster à un réseau virtuel ou ne voulez **pas** Azure Monitor, utilisez la commande suivante :

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Si vous **effectuez** le peering de votre cluster à un réseau virtuel, utilisez la commande suivante qui ajoute l’indicateur `--vnet-peer` :

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Si vous **souhaitez** utiliser Azure Monitor avec votre cluster, utilisez la commande suivante, qui ajoute l’indicateur `--workspace-id` :

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Si vous obtenez une erreur indiquant que le nom d’hôte n’est pas disponible, cela peut signifier que le nom de votre cluster n’est pas unique. Essayez de supprimer votre inscription d’application initiale et recommencez les étapes avec un autre nom de cluster dans [Créer une inscription d’application](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), en ignorant l’étape consistant à créer un utilisateur et un groupe de sécurité.




Après quelques minutes, `az openshift create` se termine.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Obtenir l’URL de connexion pour votre cluster

Pour obtenir l’URL permettant de vous connecter à votre cluster, exécutez la commande suivante :

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Recherchez le `publicHostName` dans la sortie, par exemple : `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

L’URL de connexion pour votre cluster est `https://` suivi de la valeur `publicHostName`.  Par exemple : `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Vous utiliserez cet URI à l’étape suivante dans le cadre de l’URI de redirection de l’inscription d’application.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Étape 3 : mettre à jour votre URI de redirection de l’inscription d’application

Maintenant que vous avez l’URL de connexion pour le cluster, définissez l’interface utilisateur de la redirection de l’inscription d’application :

1. Ouvrez le panneau [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Cliquez sur votre objet d’inscription d’application.
3. Cliquez sur **Ajouter un URI de redirection**.
4. Vérifiez que **TYPE** a pour valeur **Web** et définissez l’**URI DE REDIRECTION** au moyen du format suivant : `https://<public host name>/oauth2callback/Azure%20AD`. Par exemple : `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Cliquez sur **Enregistrer**.

## <a name="step-4-sign-in-to-the-openshift-console"></a>Étape 4 : Se connecter à la console OpenShift

Vous êtes désormais prêt à vous connecter à la console OpenShift pour votre nouveau cluster. La [console web OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) vous permet de visualiser, de parcourir et de gérer le contenu de vos projets OpenShift.

Vous devez utiliser une nouvelle instance de navigateur qui n’a pas mis en cache l’identité que vous utilisez habituellement pour vous connecter au portail Azure.

1. Ouvrez une fenêtre en mode *navigation privée* (Chrome) ou *InPrivate* (Microsoft Edge).
2. Accédez à l’URL de connexion que vous avez obtenue ci-dessus, par exemple : `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Connectez-vous à l’aide du nom d’utilisateur que vous avez créé à l’étape 3 de [Créer un utilisateur Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Une boîte de dialogue **Autorisations demandées** s’affiche. Cliquez sur **Consentement pour le compte de votre organisation**, puis cliquez sur **Accepter**.

Vous êtes désormais connecté à la console du cluster.

![Capture d’écran de la console de cluster OpenShift](./media/aro-console.png)

 Pour en savoir plus sur l’[utilisation de la console OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) pour créer des images, consultez la documentation [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html).

## <a name="step-5-install-the-openshift-cli"></a>Étape 5 : Installer l’interface CLI OpenShift

L’[interface CLI OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (ou *Outils OC*) propose des commandes pour gérer vos applications et utilitaires de niveau inférieur pour interagir avec les différents composants de votre cluster OpenShift.

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
> [Mettre à l'échelle un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)
