---
title: Déployer Avere vFXT pour Azure
description: Étapes à suivre pour déployer le cluster Avere vFXT dans Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 7dbfc39075bb42b1ec13823849eb769e117ddd4a
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409684"
---
# <a name="deploy-the-vfxt-cluster"></a>Déployer le cluster vFXT

Cette procédure explique comment utiliser l'Assistant de déploiement disponible sur la Place de marché Microsoft Azure. L'Assistant déploie automatiquement le cluster à l'aide d'un modèle Azure Resource Manager. Une fois que vous avez entré les paramètres dans le formulaire et cliqué sur **Créer**, Azure exécute automatiquement les étapes suivantes :

* Crée le contrôleur de cluster, qui est une machine virtuelle de base qui contient le logiciel nécessaire pour déployer et gérer le cluster.
* Configure le groupe de ressources et l’infrastructure de réseau virtuel, y compris la création de nouveaux éléments.
* Crée des machines virtuelles à nœud du cluster et les configure en tant que le cluster Avere.
* Si nécessaire, crée un nouveau conteneur d’objets Blob Azure et le configure en tant qu’un serveur de fichiers de base de cluster.

Après avoir suivi les instructions fournies dans ce document, vous aurez un réseau virtuel, un sous-réseau, un contrôleur et un cluster vFXT comme indiqué dans le diagramme suivant. Ce diagramme illustre le facultatif Blob Azure core serveur de fichiers, ce qui inclut un nouveau conteneur de stockage Blob (dans un compte de stockage, non illustré) et un point de terminaison de service pour le stockage de Microsoft à l’intérieur du sous-réseau. 

![diagramme montrant trois concentriques rectangles avec Avere les composants de cluster. Le rectangle externe est étiqueté « Groupe de ressources » et contient un hexagone étiqueté « (facultatif) de stockage d’objets Blob ». Le rectangle suivant en est étiqueté « réseau virtuel : 10.0.0.0/16' et ne contient pas tous les composants uniques. Le rectangle plus profond est étiqueté « Subnet:10.0.0.0/24 » et contient une machine virtuelle étiquetée « controller Cluster », une pile de trois machines virtuelles étiquetés « vFXT nœuds (cluster vFXT) » et un hexagone étiqueté « Point de terminaison de Service ». Il existe une flèche reliant le point de terminaison de service (qui est à l’intérieur du sous-réseau) et le stockage d’objets blob (situé à l’extérieur du sous-réseau et le réseau virtuel, dans le groupe de ressources). La flèche traverse le sous-réseau et les limites du réseau virtuel.](media/avere-vfxt-deployment.png)  

Avant d'utiliser le modèle de création, vérifiez que les conditions préalables suivantes sont réunies :  

1. [Nouvel abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Autorisations du propriétaire de l’abonnement](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota pour le cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Rôles d'accès personnalisés](avere-vfxt-prereqs.md#create-access-roles) : vous devez créer un rôle de contrôle d'accès en fonction du rôle à affecter aux nœuds du cluster. Vous pouvez également créer un rôle d'accès personnalisé pour le contrôleur de cluster, mais la plupart des utilisateurs choisiront le rôle par défaut, Propriétaire, qui confère au contrôleur les mêmes privilèges qu'un propriétaire de groupe de ressources. Pour plus d'informations, consultez [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md#owner).
1. [Point de terminaison de service de stockage (si nécessaire)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) : requis pour déploie à l’aide d’un réseau virtuel existant et la création de stockage d’objets blob

Pour plus d’informations sur la planification du déploiement du cluster et les étapes à suivre, consultez [Planifier votre système Avere vFXT](avere-vfxt-deploy-plan.md) et [Vue d’ensemble du déploiement](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Créer le déploiement Avere vFXT pour Azure

Sur le portail Azure, accédez au modèle de création en recherchant Avere et en sélectionnant « Déploiement Avere vFXT ARM ». 

![Fenêtre du navigateur affichant le portail Azure avec « Nouveau > Place de marché > Tout ». Sur la page Tout, le champ de recherche contient le terme « avere » et le deuxième résultat, « Déploiement Avere vFXT ARM », est surligné en rouge pour le mettre en évidence.](media/avere-vfxt-template-choose.png)

Après avoir lu les détails de la page Déploiement Avere vFXT ARM, cliquez sur **Créer** pour commencer. 

![Place de marché Azure sur laquelle est affichée la première page de du modèle de déploiement](media/avere-vfxt-deploy-first.png)

Le modèle est divisé en quatre étapes : deux pages de collecte d'informations, ainsi que des étapes de validation et de confirmation. 

* La première page est consacrée aux paramètres de la machine virtuelle du contrôleur de cluster. 
* La deuxième page collecte les paramètres de création du cluster et des ressources associées comme les sous-réseaux et le stockage. 
* La troisième page récapitule les paramètres et valide la configuration. 
* La quatrième page contient les conditions générales du logiciel et vous permet de lancer le processus de création du cluster. 

## <a name="page-one-parameters---cluster-controller-information"></a>Paramètres de la première page - Informations sur le contrôleur de cluster

La première page du modèle de déploiement rassemble des informations sur le contrôleur de cluster. 

![Première page du modèle de déploiement](media/avere-vfxt-deploy-1.png)

Renseignez les informations suivantes :

* **Nom du contrôleur de cluster** : définissez le nom de la machine virtuelle du contrôleur de cluster.

* **Nom d'utilisateur du contrôleur** : entrez le nom d'utilisateur racine de la machine virtuelle du contrôleur de cluster. 

* **Type d'authentification** : choisissez un mot de passe ou une authentification par clé publique SSH pour vous connecter au contrôleur. La méthode d'authentification par clé publique SSH est recommandée. Si vous avez besoin d'aide, consultez [Comment créer et utiliser des clés SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

* **Mot de passe** ou **Clé publique SSH** : selon le type d'authentification que vous avez sélectionné, vous devez fournir une clé publique RSA ou un mot de passe dans les champs suivants. Ces informations d'identification sont utilisées avec le nom d'utilisateur fourni précédemment.

* **ID de rôle de création de cluster Avere** : utilisez ce champ pour spécifier le rôle de contrôle d'accès du contrôleur de cluster. La valeur par défaut est le rôle intégré [Propriétaire](../role-based-access-control/built-in-roles.md#owner). Les privilèges de propriétaire du contrôleur de cluster sont limités au groupe de ressources du cluster. 

  Vous devez utiliser l'identificateur global unique correspondant au rôle. Pour la valeur par défaut (Propriétaire), le GUID est 8e3af657-a8ff-443c-a75c-2fe8c4bcb635. Pour trouver le GUID d'un rôle personnalisé, utilisez la commande suivante : 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Abonnement** : sélectionnez l'abonnement correspondant au déploiement Avere vFXT. 

* **Groupe de ressources** : sélectionnez un groupe de ressources vide existant pour le déploiement Avere vFXT, ou cliquez sur « Créer nouveau » et entrez un nouveau nom de groupe de ressources. 

* **Emplacement** : sélectionnez l'emplacement Azure de votre cluster et de vos ressources.

Cliquez sur **OK** lorsque vous avez terminé. 

> [!NOTE]
> Si vous souhaitez que le contrôleur de cluster dispose d'une adresse IP publique, créez un nouveau réseau virtuel pour le cluster au lieu de sélectionner un réseau existant. Ce paramètre se trouve sur la deuxième page.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Paramètres de la deuxième page - Informations sur le cluster vFXT

La deuxième page du modèle de déploiement vous permet de définir la taille du cluster, le type de nœud, la taille du cache et les paramètres de stockage, entre autres paramètres. 

![Deuxième page du modèle de déploiement](media/avere-vfxt-deploy-2.png)

* **Nombre de nœuds du cluster Avere vFXT** : choisissez le nombre de nœuds à utiliser dans le cluster. Le minimum est de trois nœuds et le maximum de douze. 

* **Mot de passe d'administration du cluster** : créez le mot de passe pour l'administration du cluster. Ce mot de passe sera utilisé avec le nom d'utilisateur ```admin``` pour vous connecter au panneau de configuration du cluster afin de surveiller le cluster et de configurer les paramètres.

* **Rôle des opérations de cluster Avere** : spécifiez le nom du rôle de contrôle d'accès pour les nœuds de cluster. Il s'agit d'un rôle personnalisé créé au préalable. 

  L'exemple décrit dans [Créer le rôle d'accès du nœud de cluster](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) enregistre le fichier sous le nom ```avere-operator.json``` et le nom de rôle correspondant est ```avere-operator```.

* **Nom du cluster Avere vFXT** : attribuez un nom unique au cluster. 

* **Taille** -cette section affiche le type de machine virtuelle qui sera utilisé pour les nœuds de cluster. Bien qu’il n'existe qu’une seule option recommandée, le **modifier la taille** lien ouvre une table avec des détails sur ce type d’instance et un lien vers un calculateur de coût.  

* **Taille du cache par nœud** : le cache du cluster est réparti sur les nœuds du cluster. La taille totale du cache de votre cluster Avere vFXT correspondra donc à la taille du cache par nœud multipliée par le nombre de nœuds. 

  La configuration recommandée consiste à utiliser les 4 To par nœud pour les nœuds de Standard_E32s_v3.

* **Réseau virtuel** : définir un nouveau réseau virtuel pour héberger le cluster, ou sélectionnez un réseau virtuel existant qui remplit les conditions préalables décrites dans [planifier votre système de vFXT Avere](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Si vous créez un réseau virtuel, le contrôleur de cluster dispose d'une adresse IP publique pour vous permettre d'accéder au nouveau réseau privé. Si vous choisissez un réseau virtuel existant, le contrôleur de cluster est configuré sans adresse IP publique. 
  > 
  > Une adresse IP publiquement visible sur le contrôleur de cluster facilite l'accès au cluster vFXT, mais pose un léger risque pour la sécurité. 
  >  * Une adresse IP publique vous permet d’utiliser le contrôleur de cluster en tant qu’hôte de saut (« jump host »). Vous pouvez ainsi vous connecter au cluster Avere vFXT à partir d’un emplacement externe au réseau virtuel.
  >  * Si vous ne configurez aucune adresse IP publique sur le contrôleur, vous devez utiliser un autre hôte de saut, une connexion VPN ou ExpressRoute pour accéder au cluster. Par exemple, créez le contrôleur au sein d'un réseau virtuel sur lequel une connexion VPN a déjà été configurée.
  >  * Si vous créez un contrôleur avec une adresse IP publique, vous devez protéger la machine virtuelle du contrôleur avec un groupe de sécurité réseau. Par défaut, le déploiement Avere vFXT pour Azure crée un groupe de sécurité réseau et limite l'accès entrant au seul port 22 pour les contrôleurs dotés d'adresses IP publiques. Vous pouvez renforcer la protection du système en verrouillant l'accès à votre plage d'adresses IP sources, c'est-à-dire autoriser uniquement les connexions des machines que vous avez l'intention d'utiliser pour l'accès au cluster.

  Le modèle de déploiement configure également le nouveau réseau virtuel avec un point de terminaison de service de stockage pour stockage Blob Azure et contrôle d’accès réseau verrouillé sur seules les adresses IP à partir du sous-réseau de cluster. 

* **Sous-réseau** : choisissez un sous-réseau de votre réseau virtuel existant, ou créez-en un. 

* **Créer et utiliser le stockage d’objets blob** -choisissez **true** pour créer un nouveau conteneur d’objets Blob Azure et le configurer en tant que stockage principal pour le nouveau cluster de vFXT Avere. Cette option crée également un nouveau compte de stockage dans le même groupe de ressources que le cluster et un point de terminaison du service de stockage de Microsoft dans le sous-réseau de cluster. 
  
  Si vous fournissez un réseau virtuel existant, il doit avoir un point de terminaison de service de stockage avant de créer le cluster. (Pour plus d’informations, consultez [planifier votre système de vFXT Avere](avere-vfxt-deploy-plan.md).)

  Définissez ce champ sur **false** si vous ne souhaitez pas créer de nouveau conteneur. Dans ce cas, vous devez joindre et configurer le stockage après la création du cluster. Pour obtenir des instructions, consultez [Configurer le stockage](avere-vfxt-add-storage.md). 

* **(Nouveau) Compte de stockage** : si la création d’un conteneur d’objets Blob Azure, entrez un nom pour le nouveau compte de stockage. 

## <a name="validation-and-purchase"></a>Validation et achat

Page trois résume la configuration et valide les paramètres. Au terme de la validation, cliquez sur le bouton **OK** pour continuer. 

![Troisième page du modèle de déploiement - Validation](media/avere-vfxt-deploy-3.png)

Sur la quatrième page, cliquez sur le bouton **Créer** pour accepter les conditions générales et créer le cluster Avere vFXT pour Azure. 

![Quatrième page du modèle de déploiement - Conditions générales, bouton Créer](media/avere-vfxt-deploy-4.png)

Le déploiement du cluster prend 15 à 20 minutes.

## <a name="gather-template-output"></a>Collecter la sortie du modèle

Après avoir créé le cluster, le modèle Avere vFXT fournit des informations importantes sur le nouveau cluster. 

> [!TIP]
> Veillez à copier l'adresse IP de gestion à partir de la sortie du modèle. Vous aurez besoin de cette adresse pour administrer le cluster.

Pour accéder à ces informations, procédez comme suit :

1. Accédez au groupe de ressources de votre contrôleur de cluster.

1. À gauche, cliquez sur **Déploiements**, puis sur **microsoft-avere.vfxt-template**.

   ![Page du portail Groupe de ressources comprenant les déploiements sélectionnés à gauche et microsoft-avere.vfxt-template dans une table sous Nom du déploiement](media/avere-vfxt-outputs-deployments.png)

1. À gauche, cliquez sur **Sorties**. Copiez les valeurs dans chacun des champs. 

   ![page affichant les valeurs SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs et MGMT_IP dans des champs, à droite des étiquettes](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Étape suivante

Maintenant que le cluster est en cours d'exécution et que vous connaissez son adresse IP de gestion, vous pouvez [vous connecter à l'outil de configuration du cluster](avere-vfxt-cluster-gui.md) pour activer la prise en charge, ajouter du stockage si nécessaire et personnaliser d'autres paramètres du cluster.
