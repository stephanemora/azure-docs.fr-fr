---
title: Déployer Avere vFXT pour Azure
description: Étapes à suivre pour déployer le cluster Avere vFXT dans Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 47773f9375927e4d8dfbfec922e4cf11e42ade04
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670046"
---
# <a name="deploy-the-vfxt-cluster"></a>Déployer le cluster vFXT

Pour créer un cluster vFXT, le moyen le plus simple consiste à utiliser un contrôleur de cluster, à savoir une machine virtuelle disposant des scripts, des modèles et de l’infrastructure logicielle nécessaires pour créer et gérer le cluster vFXT.

Pour déployer un nouveau cluster vFXT, effectuez les étapes suivantes :

1. [Créez le contrôleur de cluster](#create-the-cluster-controller-vm).
1. Si vous utilisez Stockage Blob Azure, [créez un point de terminaison de stockage](#create-a-storage-endpoint-if-using-azure-blob) dans votre réseau virtuel.
1. [Connectez-vous au contrôleur de cluster](#access-the-controller). Les étapes restantes sont effectuées sur la machine virtuelle du contrôleur de cluster. 
1. [Créez le rôle d’accès](#create-the-cluster-node-access-role) pour les nœuds de cluster. Un prototype est fourni.
1. [Personnalisez le script de création de cluster](#edit-the-deployment-script) pour le type de cluster vFXT que vous souhaitez créer.
1. [Exécutez le script de création de cluster](#run-the-script).

Pour plus d’informations sur la planification du déploiement du cluster et les étapes à suivre, consultez [Planifier votre système Avere vFXT](avere-vfxt-deploy-plan.md) et [Vue d’ensemble du déploiement](avere-vfxt-deploy-overview.md). 

Après avoir suivi les instructions de ce document, vous disposerez d’un réseau virtuel, d’un sous-réseau, d’un contrôleur et d’un cluster vFXT, comme le montre le diagramme suivant :

![Diagramme montrant un réseau virtuel contenant un stockage d’objets blob facultatif et un sous-réseau contenant trois machines virtuelles regroupées, étiquetées « nœuds vFXT/cluster vFXT », et une machine virtuelle étiquetée « contrôleur de cluster »](media/avere-vfxt-deployment-500px.png)

Avant de commencer, veillez à satisfaire ces prérequis :  

1. [Nouvel abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Autorisations du propriétaire de l’abonnement](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota pour le cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

Vous pouvez éventuellement créer le rôle de nœud de cluster [avant](avere-vfxt-pre-role.md) de créer le contrôleur de cluster, mais vous pourrez le faire plus facilement après.

## <a name="create-the-cluster-controller-vm"></a>Créer la machine virtuelle du contrôleur de cluster

La première étape consiste à créer la machine virtuelle qui créera et configurera les nœuds de cluster vFXT. 

Le contrôleur de cluster est une machine virtuelle Linux sur laquelle le logiciel et les scripts de création du cluster Avere vFXT sont préinstallés. Ses besoins en puissance de traitement et en espace de stockage n’étant pas importants, vous pouvez choisir des options bon marché. Cette machine virtuelle sera utilisée occasionnellement tout au long de la durée de vie du cluster vFXT.

Vous pouvez créer la machine virtuelle du contrôleur de cluster de deux façons. Un [modèle Azure Resource Manager](#create-controller---arm-template) est fourni [ci-dessous](#create-controller---arm-template) pour simplifier le processus, mais vous pouvez également créer le contrôleur à partir de [l’image Place de marché Azure](#create-controller---azure-marketplace-image). 

Vous pouvez créer un groupe de ressources dans le cadre de la création du contrôleur.

> [!TIP]
>
> Choisissez d’utiliser ou non une adresse IP publique sur le contrôleur de cluster. Une adresse IP publique permet d’accéder plus simplement au cluster vFXT, mais elle pose un léger risque pour la sécurité.
>
>  * Une adresse IP publique vous permet d’utiliser le contrôleur de cluster en tant qu’hôte de saut (« jump host »). Vous pouvez ainsi vous connecter au cluster Avere vFXT à partir d’un emplacement externe au réseau virtuel.
>  * Si vous ne configurez aucune adresse IP publique sur le contrôleur, vous devez utiliser un autre hôte de saut, une connexion VPN ou ExpressRoute pour accéder au cluster. Par exemple, créez le contrôleur au sein d’un réseau virtuel disposant d’une connexion VPN configurée.
>  * Si vous créez un contrôleur avec une adresse IP publique, vous devez protéger la machine virtuelle du contrôleur avec un groupe de sécurité réseau. Autorisez uniquement l’accès à Internet par le biais du port 22.

### <a name="create-controller---resource-manager-template"></a>Créer un contrôleur - Modèle Resource Manager

Pour créer le nœud de contrôleur de cluster à partir du portail, cliquez sur le bouton « Déployer sur Azure » ci-dessous. Ce modèle de déploiement crée la machine virtuelle qui va créer et gérer le cluster Avere vFXT.

[![Bouton permettant de créer le contrôleur](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

Fournissez les informations suivantes.

Dans la section **DE BASE** :  

* **Abonnement** pour le cluster
* **Groupe de ressources** pour le cluster 
* **Lieu** 

Dans la section **PARAMÈTRES** :

* Indiquez s’il faut ou non créer un réseau virtuel.

  * Si vous créez un réseau virtuel, le contrôleur de cluster se voit affecter une adresse IP publique pour que vous puissiez y accéder. Un groupe de sécurité réseau est également créé pour ce réseau virtuel, limitant le trafic entrant au port 22.
  * Si vous souhaitez utiliser ExpressRoute ou un VPN pour vous connecter au contrôleur de cluster, affectez `false` à cette valeur et spécifiez un réseau virtuel existant dans les champs restants. Le contrôleur de cluster utilise ce réseau virtuel pour la communication réseau. 

* Groupe de ressources, nom et nom de sous-réseau du réseau virtuel : tapez les noms de ressources existantes (si vous utilisez un réseau virtuel existant) ou tapez de nouveaux noms si vous créez un réseau virtuel.
* **Nom du contrôleur** : donnez un nom à la machine virtuelle du contrôleur.
* Nom d’utilisateur de l’administrateur du contrôleur: la valeur par défaut est `azureuser`.
* Clé SSH : collez la clé publique à associer au nom d’utilisateur de l’administrateur. Si vous avez besoin d’aide, consultez [Comment créer et utiliser des clés SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

Sous **CONDITIONS GÉNÉRALES** : 

* Lisez les conditions d’utilisation du service et cochez la case pour les accepter. 

  > [!NOTE] 
  > Si vous n’êtes pas un propriétaire d’abonnement, demandez à un propriétaire d’accepter les conditions pour vous en suivant les étapes prérequises dans [Accepter les conditions du logiciel à l’avance](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 

Quand vous avez terminé, cliquez sur **Acheter**. Au terme d’un délai de cinq ou six minutes, votre nœud de contrôleur est opérationnel.

Visitez la page des sorties pour obtenir les informations relatives au cluster. Pour en savoir plus, consultez [Entrées nécessaires à la création d’un cluster](#inputs-needed-for-cluster-creation).

### <a name="create-controller---azure-marketplace-image"></a>Créer le contrôleur - Image Place de marché Azure

Pour trouver le modèle de contrôleur, recherchez ``Avere`` dans la Place de marché Azure. Sélectionnez le modèle **Contrôleur Avere vFXT pour Azure**. 

Si ce n’est pas déjà fait, acceptez les conditions et activez l’accès programmatique pour l’image Place de marché en cliquant sur le lien « Vous voulez déployer par programmation ? » situé sous le bouton **Créer**.

> [!NOTE] 
> Durant la première semaine de disponibilité générale (du 31 octobre au 7 novembre 2018), au lieu de suivre cette procédure, vous devez utiliser l’option de ligne de commande pour accepter les conditions associées à deux images logicielles. Suivez les instructions dans [Accepter les conditions du logiciel à l’avance](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 

![Capture d’écran d’un lien vers l’accès programmatique situé sous le bouton Créer](media/avere-vfxt-deploy-programmatically.png)

Cliquez sur le bouton **Activer** et enregistrez le paramètre.

![Capture d’écran montrant un clic de souris pour activer l’accès programmatique](media/avere-vfxt-enable-program.png)

Revenez à la page principale du modèle **Contrôleur Avere vFXT pour Azure** et cliquez sur **Créer**. 

Dans le premier panneau, renseignez ou confirmez ces options de base :

* **Abonnement**
* **Groupe de ressources** : entrez un nouveau nom pour créer un groupe.
* **Nom de la machine virtuelle** : nom du contrôleur.
* **Région**
* **Options de disponibilité** : la redondance n’est pas nécessaire.
* **Image** : image du nœud de contrôleur Avere vFXT.
* **Taille** : conservez la valeur par défaut ou choisissez un autre type peu onéreux.
* **Compte Administrateur** : définissez le mode de connexion au contrôleur de cluster. 
  * Sélectionnez Nom d’utilisateur/mot de passe ou Clé publique SSH (recommandé).
  
    > [!TIP] 
    > Une clé SSH est plus sécurisée. Si vous avez besoin d’aide, consultez [Comment créer et utiliser des clés SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys). 
  * Spécifiez le nom d’utilisateur. 
  * Collez la clé SSH, ou entrez et confirmez le mot de passe.
* **Règles des ports d’entrée** : si vous utilisez une adresse IP publique, ouvrez le port 22 (SSH).

Cliquez sur **Suivant** pour définir les options relatives aux disques :

* **Type de disque de système d’exploitation** : la valeur par défaut (HDD) est suffisante.
* **Utiliser des disques non managés** : cette option n’est pas nécessaire.
* **Disques de données** : n’utilisez pas cette option.

Cliquez sur **Suivant** pour définir les options de mise en réseau :

* **Réseau virtuel** : sélectionnez le réseau virtuel pour le contrôleur, ou entrez un nom pour créer un réseau virtuel. Si vous ne souhaitez pas utiliser une adresse IP publique sur le contrôleur, songez à placer celui-ci dans un réseau virtuel disposant d’ExpressRoute ou d’une autre méthode d’accès déjà configurée.
* **Sous-réseau** : sélectionnez un sous-réseau dans le réseau virtuel (facultatif). Si vous créez un réseau virtuel, vous pouvez créer un sous-réseau en même temps.
* **Adresse IP publique** : si vous souhaitez utiliser une adresse IP publique, spécifiez-la ici. 
* **Groupe de sécurité réseau** : conservez le paramètre par défaut (**De base**). 
* **Ports d’entrée publics** : si vous utilisez une adresse IP publique, utilisez ce contrôle pour autoriser les accès provenant du trafic SSH. 
* **Mise en réseau accélérée** n’est pas disponible pour cette machine virtuelle.

Cliquez sur **Suivant** pour définir les options de gestion :

* **Diagnostics de** **démarrage** : désactivez cette option.
* **Diagnostics du système d’exploitation invité** : conservez cette option désactivée.
* **Compte de stockage des diagnostics** : sélectionnez ou spécifiez un nouveau compte pour contenir les informations de diagnostic (facultatif).
* **Managed Service** **Identity** : activez cette option pour créer un principal de service Azure AD pour le contrôleur de cluster.
* **Arrêt automatique** : conservez cette option désactivée. 

À ce stade, vous pouvez cliquer sur **Vérifier + créer** si vous ne souhaitez pas utiliser de balises d’instance. Sinon, cliquez deux fois sur **Suivant** pour ignorer la page **Configuration de l’invité** et accéder à la page des balises. Une fois terminé, cliquez sur **Vérifier + créer**. 

Une fois vos sélections validées, cliquez sur le bouton **Créer**.  

La création prend cinq ou six minutes.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Créer un point de terminaison de stockage (si vous utilisez Stockage Blob Azure)

Si vous utilisez Stockage Blob Azure pour le stockage de vos données back-end, vous devez créer un point de terminaison de service de stockage dans votre réseau virtuel. Ce [point de terminaison de service](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) conserve le trafic des objets blob Azure au niveau local au lieu de l’acheminer par Internet.

1. Dans le portail, cliquez sur **Réseaux virtuels** à gauche.
1. Sélectionnez le réseau virtuel pour votre contrôleur. 
1. Cliquez sur **Points de terminaison de service** à gauche.
1. Cliquez sur **Ajouter** dans la partie supérieure.
1. Conservez le service ``Microsoft.Storage`` et choisissez le sous-réseau du contrôleur.
1. Dans la partie inférieure, cliquez sur **Ajouter**.

  ![Capture d’écran du portail Azure montrant les étapes de création du point de terminaison de service](media/avere-vfxt-service-endpoint.png)

## <a name="gather-needed-inputs"></a>Collecter les entrées nécessaires

Vous avez besoin des informations suivantes pour créer le cluster. 

Si vous avez créé le nœud de contrôleur à l’aide du modèle Resource Manager, vous pouvez [obtenir les informations à partir de la sortie du modèle](#finding-template-output). 

Ce qu’il faut pour se connecter au contrôleur : 

* Nom d’utilisateur et clé SSH ou mot de passe du contrôleur
* Adresse IP du contrôleur ou autre méthode permettant de se connecter à la machine virtuelle du contrôleur

Ce qu’il faut pour créer le cluster : 

* Nom de groupe ressources
* Emplacement Azure 
* Nom du réseau virtuel
* Nom du sous-réseau
* Nom de rôle du nœud de cluster
* Nom du compte de stockage en cas de création d’un conteneur d’objets blob

Pour trouver les informations manquantes, accédez à la page d’informations sur la machine virtuelle du contrôleur. Par exemple, cliquez sur **Toutes les ressources** et recherchez le nom du contrôleur, puis cliquez dessus pour afficher les détails.

### <a name="finding-template-output"></a>Localisation de la sortie du modèle

Pour trouver ces informations à partir de la sortie du modèle Resource Manager, effectuez les étapes suivantes :

1. À partir de l’icône de notification dans la barre supérieure du portail Azure, cliquez sur **Accéder au groupe de ressources**. Vous accédez au nouveau groupe de ressources contenant le contrôleur et le réseau virtuel.

   ![Section Notifications du portail avec le message « Déploiement réussi » et les boutons « Accéder au groupe de ressources » et « Épingler au tableau de bord »](media/avere-vfxt-browse-to-rg.png)

1. À gauche, cliquez sur **Déploiements**, puis sur **Microsoft.Template**.

   ![Page du portail Groupe de ressources comprenant les déploiements sélectionnés à gauche et Microsoft.Template dans une table sous Nom du déploiement](media/avere-vfxt-deployment-template.png)

1. À gauche, cliquez sur **Sorties**. Copiez les valeurs dans chacun des champs. 

   ![Page Sorties avec SSHSTRING RESOURCE_GROUP, LOCATION, NETWORK, SUBNET et SUBNET_ID indiqués dans les champs à droite des étiquettes](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>Accéder au contrôleur

Pour effectuer les étapes restantes du déploiement, vous devez vous connecter au contrôleur de cluster.

1. La méthode de connexion au contrôleur de cluster varie selon la configuration.

   * Si le contrôleur a une adresse IP publique, utilisez ssh pour vous connecter à l’adresse IP du contrôleur avec le nom d’administrateur que vous avez défini (par exemple, ``ssh azureuser@40.117.136.91``).
   * Si le contrôleur n’a pas d’adresse IP publique, utilisez un [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou une connexion VPN à votre réseau virtuel.

1. Une fois connecté à votre contrôleur, authentifiez-vous en exécutant `az login`. Copiez le code d’authentification fourni dans l’interpréteur de commandes, puis utilisez un navigateur web pour charger [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) et vous authentifier auprès du système Microsoft. Retournez à l’interpréteur de commandes pour confirmation.

   ![Sortie de ligne de commande de la commande « AZ login » indiquant le lien du navigateur et le code d’authentification](media/avere-vfxt-azlogin.png)

1. Ajoutez votre abonnement en exécutant cette commande avec votre ID d’abonnement : ```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>Créer le rôle d’accès du nœud de cluster

> [!NOTE] 
> Si vous n’êtes pas un propriétaire d’abonnement et que le rôle n’a pas déjà été créé, demandez à un propriétaire d’abonnement de suivre ces étapes ou d’utiliser la procédure décrite dans [Créer le rôle d’accès du runtime de cluster Avere vFXT sans contrôleur](avere-vfxt-pre-role.md).

Le [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) donne aux nœuds de cluster vFXT l’autorisation d’effectuer les tâches nécessaires.  

Dans le cadre d’un cluster vFXT normal, les nœuds vFXT individuels doivent effectuer diverses opérations, comme lire les propriétés des ressources Azure, gérer le stockage et contrôler les paramètres d’interface réseau des autres nœuds. 

1. Sur le contrôleur, ouvrez le fichier ``/avere-cluster.json`` dans un éditeur.

   ![Console montrant une commande list, puis « vi /avere-cluster.json »](media/avere-vfxt-open-role.png)

1. Modifiez le fichier en indiquant votre ID d’abonnement et en supprimant la ligne située au-dessus de celui-ci. Enregistrez le fichier sous le nom ``avere-cluster.json``.

   ![Éditeur de texte de la console indiquant l’ID d’abonnement et la ligne à supprimer](media/avere-vfxt-edit-role.png)

1. Utilisez cette commande pour créer le rôle :  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Vous allez passer le nom du rôle au script de création du cluster à l’étape suivante. 

## <a name="create-nodes-and-configure-the-cluster"></a>Créer les nœuds et configurer le cluster

Pour créer le cluster Avere vFXT, modifiez l’un des exemples de script inclus sur le contrôleur et exécutez-le à cet endroit. Vous trouverez des exemples de scripts dans le répertoire racine (`/`) sur le contrôleur de cluster.

* Si vous souhaitez créer un conteneur d’objets blob à utiliser comme système de stockage back-end pour Avere vFXT, utilisez le script ``create-cloudbacked-cluster``.

* Si vous envisagez d’ajouter le stockage plus tard, utilisez le script ``create-minimal-cluster``.

> [!TIP]
> Des prototypes de script pour l’ajout de nœuds et la destruction du cluster vFXT sont également inclus dans le répertoire `/` de la machine virtuelle du contrôleur de cluster.

### <a name="edit-the-deployment-script"></a>Modifier le script de déploiement

Ouvrez l’exemple de script dans un éditeur. Pensez à enregistrer le script personnalisé sous un nom différent pour ne pas remplacer l’original.

Affectez des valeurs à ces variables de script.

* Nom de groupe ressources

  * Si vous utilisez des composants réseau ou de stockage qui se trouvent dans des groupes de ressources différents, décommentez les variables et fournissez également ces noms. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* Nom de l’emplacement
* Nom du réseau virtuel
* Nom du sous-réseau
* Nom de rôle du runtime Azure AD : si vous avez suivi l’exemple dans [Créer le rôle d’accès du nœud de cluster](#create-the-cluster-node-access-role), utilisez ``avere-cluster``. 
* Nom du compte de stockage (si vous créez un conteneur d’objets blob)
* Nom du cluster : vous ne pouvez pas avoir deux clusters vFXT portant le même nom dans le même groupe de ressources. 
* Mot de passe d’administration : choisissez un mot de passe sécurisé pour superviser et administrer le cluster. Ce mot de passe est affecté à l’utilisateur ``admin``. 
* Type d’instance de nœud : pour plus d’informations, consultez [Tailles de nœuds vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes).
* Taille du cache de nœud : pour plus d’informations, consultez [Tailles de nœuds vFXT](avere-vfxt-deploy-plan.md#vfxt-node-sizes).

Enregistrez le fichier et quittez l’éditeur.

### <a name="run-the-script"></a>Exécutez le script
Exécutez le script en tapant le nom de fichier que vous avez créé. Par exemple : `./create-cloudbacked-cluster-west1`.  

Envisagez d’exécuter cette commande dans un [multiplexeur de terminal](http://linuxcommand.org/lc3_adv_termmux.php) comme `screen` ou `tmux` au cas où vous perdriez votre connexion.  
La sortie est également journalisée dans `~/vfxt.log`.

Au terme du script, copiez l’adresse IP de gestion nécessaire à l’administration du cluster.

![Sortie de ligne de commande du script affichant l’adresse IP de gestion vers la fin](media/avere-vfxt-mgmt-ip.png)

### <a name="next-step"></a>Étape suivante

Maintenant que le cluster est en cours d’exécution et que vous connaissez son adresse IP de gestion, vous pouvez [vous connecter à l’outil de configuration du cluster](avere-vfxt-cluster-gui.md) pour activer la prise en charge et ajouter un stockage si nécessaire.
