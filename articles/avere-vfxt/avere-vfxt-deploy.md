---
title: Déployer Avere vFXT pour Azure
description: Étapes à suivre pour déployer le cluster Avere vFXT dans Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: da329b5c50fe7c39d9773743b40c2f990e298963
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296373"
---
# <a name="deploy-the-vfxt-cluster"></a>Déployer le cluster vFXT

Cette procédure explique comment utiliser l'Assistant de déploiement disponible sur la Place de marché Microsoft Azure. L'Assistant déploie automatiquement le cluster à l'aide d'un modèle Azure Resource Manager. Une fois que vous avez entré les paramètres dans le formulaire et cliqué sur **Créer**, Azure exécute automatiquement les étapes suivantes : 

* Créez le contrôleur de cluster ; il s'agit d'une machine virtuelle de base contenant le logiciel nécessaire au déploiement et à la gestion du cluster.
* Configurez l'infrastructure du réseau virtuel et du groupe de ressources, notamment en créant de nouveaux éléments si nécessaire.
* Créez les machines virtuelles du nœud de cluster et configurez-les en tant que cluster Avere.
* Si nécessaire, créez un conteneur d'objets Blob Azure et configurez-le en tant que système de stockage principal du cluster.

Après avoir suivi les instructions de ce document, vous disposerez d’un réseau virtuel, d’un sous-réseau, d’un contrôleur et d’un cluster vFXT, comme le montre le diagramme suivant :

![Diagramme montrant un réseau virtuel contenant un stockage d’objets blob facultatif et un sous-réseau contenant trois machines virtuelles regroupées, étiquetées « nœuds vFXT/cluster vFXT », et une machine virtuelle étiquetée « contrôleur de cluster »](media/avere-vfxt-deployment.png)

Après avoir créé le cluster, vous devez [créer un point de terminaison de stockage](#create-a-storage-endpoint-if-using-azure-blob) sur votre réseau virtuel si vous utilisez le stockage d'objets Blob. 

Avant d'utiliser le modèle de création, vérifiez que les conditions préalables suivantes sont réunies :  

1. [Nouvel abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Autorisations du propriétaire de l’abonnement](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Quota pour le cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Rôles d'accès personnalisés](avere-vfxt-prereqs.md#create-access-roles) : vous devez créer un rôle de contrôle d'accès en fonction du rôle à affecter aux nœuds du cluster. Vous pouvez également créer un rôle d'accès personnalisé pour le contrôleur de cluster, mais la plupart des utilisateurs choisiront le rôle par défaut, Propriétaire, qui confère au contrôleur les mêmes privilèges qu'un propriétaire de groupe de ressources. Pour plus d'informations, consultez [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md#owner).

Pour plus d’informations sur la planification du déploiement du cluster et les étapes à suivre, consultez [Planifier votre système Avere vFXT](avere-vfxt-deploy-plan.md) et [Vue d’ensemble du déploiement](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Créer le déploiement Avere vFXT pour Azure

Sur le portail Azure, accédez au modèle de création en recherchant Avere et en sélectionnant « Déploiement Avere vFXT pour Azure ». <!-- xxx update if that name changes xxx --> 

<!-- **[XXX need production image of template deploy in search and/or entry page of template deploy XXX]** -->

Cliquez sur **Créer** pour commencer. 

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

* **Groupe de ressources** : sélectionnez le groupe de ressources du déploiement Avere vFXT, ou cliquez sur « Créer nouveau » et entrez un nouveau nom de groupe de ressources. 

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

* **Taille** : spécifiez le type de machine virtuelle à utiliser lors de la création des nœuds de cluster. 

* **Taille du cache par nœud** : le cache du cluster est réparti sur les nœuds du cluster. La taille totale du cache de votre cluster Avere vFXT correspondra donc à la taille du cache par nœud multipliée par le nombre de nœuds. 

  La configuration recommandée est d'utiliser 1 To par nœud si vous utilisez des nœuds de cluster Standard_D16s_v3, et 4 To par nœud si vous utilisez des nœuds Standard_E32s_v3.

* **Réseau virtuel** : sélectionnez un réseau virtuel existant pour héberger le cluster, ou définissez un nouveau réseau virtuel à créer. 

  > [!NOTE]
  > Si vous créez un réseau virtuel, le contrôleur de cluster dispose d'une adresse IP publique pour vous permettre d'accéder au nouveau réseau privé. Si vous choisissez un réseau virtuel existant, le contrôleur de cluster est configuré sans adresse IP publique. 
  > 
  > Une adresse IP publiquement visible sur le contrôleur de cluster facilite l'accès au cluster vFXT, mais pose un léger risque pour la sécurité. 
  >  * Une adresse IP publique vous permet d’utiliser le contrôleur de cluster en tant qu’hôte de saut (« jump host »). Vous pouvez ainsi vous connecter au cluster Avere vFXT à partir d’un emplacement externe au réseau virtuel.
  >  * Si vous ne configurez aucune adresse IP publique sur le contrôleur, vous devez utiliser un autre hôte de saut, une connexion VPN ou ExpressRoute pour accéder au cluster. Par exemple, créez le contrôleur au sein d'un réseau virtuel sur lequel une connexion VPN a déjà été configurée.
  >  * Si vous créez un contrôleur avec une adresse IP publique, vous devez protéger la machine virtuelle du contrôleur avec un groupe de sécurité réseau. Par défaut, le déploiement Avere vFXT pour Azure crée un groupe de sécurité réseau et limite l'accès entrant au seul port 22 pour les contrôleurs dotés d'adresses IP publiques. Vous pouvez renforcer la protection du système en verrouillant l'accès à votre plage d'adresses IP sources, c'est-à-dire autoriser uniquement les connexions des machines que vous avez l'intention d'utiliser pour l'accès au cluster.

* **Sous-réseau** : choisissez un sous-réseau de votre réseau virtuel existant, ou créez-en un. 

* **Utiliser le stockage d'objets blob** : choisissez de créer ou non un nouveau conteneur d'objets Blob Azure et configurez-le en tant que stockage principal pour le nouveau cluster Avere vFXT. Si vous choisissez de créer un nouveau conteneur, vous devez fournir le compte de stockage de celui-ci. Si vous choisissez de ne pas créer de nouveau conteneur d'objets blob, vous devez joindre un stockage après la création du cluster (pour obtenir des instructions, reportez-vous à [Configurer le stockage](avere-vfxt-add-storage.md)). Définissez ce champ sur **false** si vous ne souhaitez pas créer de nouveau conteneur.

* **Compte de stockage** : si vous créez un nouveau conteneur d'objets Blob Azure, entrez le nom du compte de stockage. Le compte de stockage doit être un compte V2 standard à usage général configuré avec un stockage localement redondant et le niveau d'accès chaud. L'article [Configurer le stockage](avere-vfxt-add-storage.md#azure-storage-cloud-core-filer) contient des informations détaillées sur les exigences du compte de stockage.

## <a name="validation-and-purchase"></a>Validation et achat

La troisième page récapitule la configuration et valide les paramètres. Au terme de la validation, cliquez sur le bouton **OK** pour continuer. 

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


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Créer un point de terminaison de stockage (si vous utilisez Stockage Blob Azure)

Si vous utilisez Stockage Blob Azure pour le stockage de vos données back-end, vous devez créer un point de terminaison de service de stockage dans votre réseau virtuel. Ce [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) conserve le trafic des objets blob Azure au niveau local au lieu de l’acheminer par Internet.

1. Dans le portail, cliquez sur **Réseaux virtuels** à gauche.
1. Sélectionnez le réseau virtuel pour votre contrôleur. 
1. Cliquez sur **Points de terminaison de service** à gauche.
1. Cliquez sur **Ajouter** dans la partie supérieure.
1. Conservez le service ``Microsoft.Storage`` et choisissez le sous-réseau du contrôleur.
1. Dans la partie inférieure, cliquez sur **Ajouter**.

  ![Capture d’écran du portail Azure montrant les étapes de création du point de terminaison de service](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Étape suivante

Maintenant que le cluster est en cours d'exécution et que vous connaissez son adresse IP de gestion, vous pouvez [vous connecter à l'outil de configuration du cluster](avere-vfxt-cluster-gui.md) pour activer la prise en charge, ajouter du stockage si nécessaire et personnaliser d'autres paramètres du cluster.
