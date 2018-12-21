---
title: Utiliser une image de la Place de marché Microsoft Azure pour créer une machine virtuelle Terraform pour Linux avec une identité managée
description: Utilisez une image de la Place de marché afin de créer une machine virtuelle Terraform pour Linux avec une identité managée et la gestion de l’état à distance pour déployer facilement des ressources sur Azure.
services: terraform
ms.service: terraform
keywords: terraform, devops, MSI, machine virtuelle, état à distance, azure
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: 6fef56d780fe664e79f66fa23be526aec71d7e7b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971162"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Utiliser une image de la Place de marché Microsoft Azure pour créer une machine virtuelle Terraform pour Linux avec des identités managées pour des ressources Azure

Cet article explique comment utiliser une [image de la Place de marché Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) pour créer une machine virtuelle Ubuntu Linux (16.04 LTS) avec la dernière version de [Terraform](https://www.terraform.io/intro/index.html) installée et configurée à l’aide des [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Cette image configure également un backend distant pour activer la gestion de [l’état à distance](https://www.terraform.io/docs/state/remote.html) à l’aide de Terraform. 

L’image de la Place de marché Terraform facilite la prise en main de Terraform sur Azure, sans avoir besoin d’installer et de configurer Terraform manuellement. 

Cette image de machine virtuelle Terraform ne génère pas de frais logiciels. Vous payez uniquement les frais d’utilisation matérielle Azure en fonction de la taille de la machine virtuelle approvisionnée. Pour plus de détails sur le calcul des frais, consultez la page [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Prérequis
Pour pouvoir créer une machine virtuelle Terraform pour Linux, vous devez avoir un abonnement Azure. Si vous n’en avez pas déjà un, consultez [Créez votre compte Azure gratuit](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Créer une machine virtuelle Terraform 

Voici les étapes à suivre pour créer une instance de machine virtuelle Terraform pour Linux : 

1. Dans le portail Azure, accédez à la liste [Créer une ressource](https://ms.portal.azure.com/#create/hub).

2. Dans la barre de recherche **Rechercher sur le Marketplace**, recherchez **Terraform**. Sélectionnez le modèle **Terraform**. 

3. Sur l’onglet des détails Terraform en bas à droite, sélectionnez le bouton **Créer**.

    ![Créer une machine virtuelle Terraform](media/terraformmsi.png)

4. Les sections suivantes décrivent les entrées pour chacune des étapes de l’Assistant à suivre pour créer la machine virtuelle Terraform pour Linux. La section suivante répertorie les entrées requises pour configurer chacune de ces étapes.

## <a name="details-on-the-create-terraform-tab"></a>Détails de l’onglet Créer Terraform

Entrez les informations suivantes sur l’onglet **Créer Terraform** :

1. **Concepts de base**
    
   * **Nom** : nom de votre machine virtuelle Terraform.
   * **Nom d’utilisateur** : premier ID de connexion du compte.
   * **Mot de passe** : premier mot de passe du compte. (Vous pouvez utiliser une clé publique SSH au lieu d’un mot de passe).
   * **Abonnement**: abonnement sur lequel la machine doit être créée et facturée. Vous devez disposer des privilèges de création de ressources pour cet abonnement.
   * **Groupe de ressources** : nouveau groupe de ressources ou groupe de ressources existant.
   * **Emplacement** : centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau.

2. **Paramètres supplémentaires**

   * **Taille**: taille de la machine virtuelle. 
   * **Type de disque de machine virtuelle** : disque SSD ou HDD.

3. **Récapitulatif Terraform**

   * Vérifiez que toutes les informations que vous avez saisies sont correctes. 

4. **Acheter**

   * Pour démarrer le processus d’approvisionnement, sélectionnez **Acheter**. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle ne génère pas de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l’étape de définition de la taille.

L’image de la machine virtuelle Terraform effectue les étapes suivantes :

* Crée une machine virtuelle à laquelle le système assigne une identité basée sur l’image d’Ubuntu 16.04 LTS.
* Installe l’extension MSI sur la machine virtuelle pour permettre l’envoi de jetons OAuth pour les ressources Azure.
* Assigne des autorisations RBAC à l’identité gérée pour octroyer des droits de propriétaire sur le groupe de ressources.
* Crée un dossier de modèle Terraform (tfTemplate).
* Préconfigure l’état à distance de Terraform avec le backend Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Utiliser et configurer la machine virtuelle Terraform pour Linux

Une fois la machine virtuelle créée, vous pouvez vous y connecter avec SSH. Utilisez les informations d’identification de compte que vous avez définies dans la section Paramètres de base de l’étape 3 de l’interface de l’interpréteur de commandes texte. Sous Windows, vous pouvez télécharger un outil client SSH tel que [Putty](http://www.putty.org/).

Après vous être connecté à la machine virtuelle à l’aide de SSH, vous devez accorder les autorisations de contributeur sur tout l’abonnement aux identités managées pour les ressources Azure sur la machine virtuelle. 

Les autorisations de collaborateur permettent au service MSI sur la machine virtuelle d’utiliser Terraform pour créer des ressources en dehors du groupe de ressources de la machine virtuelle. Vous pouvez facilement effectuer cette action en exécutant un script une seule fois. Utilisez la commande suivante :

`. ~/tfEnv.sh`

Le script précédent utilise la méthode de [connexion interactive avec AZ CLI v 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) pour s’authentifier sur Azure et assigner l’autorisation de contributeur d’identité managée de machine virtuelle sur l’ensemble de l’abonnement. 

 Un backend d’état à distance Terraform est créé sur la machine virtuelle. Pour l’activer dans votre déploiement Terraform, vous devez copier le fichier remoteState.tf du répertoire tfTemplate à la racine des scripts Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Pour plus d’informations sur la gestion de l’état à distance, consultez [cette page sur l’état à distance Terraform](https://www.terraform.io/docs/state/remote.html). La clé d’accès de stockage est exposée dans ce fichier et doit être exclue avant la validation des fichiers de configuration Terraform dans le contrôle de code source.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à créer une machine virtuelle Terraform pour Linux sur Azure. Pour en savoir plus sur Terraform sur Azure, consultez les ressources supplémentaires suivantes : 

 [Hub Terraform sur Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentation Terraform Azure Provider](https://aka.ms/terraform)  
 [Source Terraform Azure Provider](https://aka.ms/tfgit)  
 [Modules Terraform Azure](https://aka.ms/tfmodules)
 

















