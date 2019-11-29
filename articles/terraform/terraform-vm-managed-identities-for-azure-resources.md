---
title: 'Tutoriel : Créer une machine virtuelle Linux avec une identité managée à partir de l’image Place de marché Azure à l’aide de Terraform'
description: Créez une machine virtuelle Terraform pour Linux avec une identité managée et une gestion de l’état à distance à l’aide de l’image Place de marché Azure
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 6bc6bec2a13dcd5747823de739d1dd11c6027091
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158980"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Didacticiel : Créer une machine virtuelle Linux avec une identité managée à partir de l’image Place de marché Azure à l’aide de Terraform

Cet article explique comment utiliser une [image de la Place de marché Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) pour créer une machine virtuelle Ubuntu Linux (16.04 LTS) avec la dernière version de [Terraform](https://www.terraform.io/intro/index.html) installée et configurée à l’aide des [identités managées pour les ressources Azure](/azure/active-directory/managed-service-identity/overview). Cette image configure également un backend distant pour activer la gestion de [l’état à distance](https://www.terraform.io/docs/state/remote.html) à l’aide de Terraform. 

L’image de la Place de marché Terraform facilite la prise en main de Terraform sur Azure, sans avoir besoin d’installer et de configurer Terraform manuellement. 

Cette image de machine virtuelle Terraform ne génère pas de frais logiciels. Vous payez uniquement les frais d’utilisation matérielle Azure en fonction de la taille de la machine virtuelle provisionnée. 

Pour plus d’informations sur le calcul des frais, consultez la page [Tarifs des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Prérequis
Pour pouvoir créer une machine virtuelle Terraform pour Linux, vous devez avoir un abonnement Azure. Si vous n’en avez pas déjà un, consultez [Créez votre compte Azure gratuit](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Créer une machine virtuelle Terraform 

Voici les étapes à suivre pour créer une instance de machine virtuelle Terraform pour Linux : 

1. Dans le portail Azure, accédez à la liste [Créer une ressource](https://ms.portal.azure.com/#create/hub).

1. Dans la barre de recherche **Rechercher sur le Marketplace**, recherchez **Terraform**. 

1. Sélectionnez le bouton **Créer**. 

1. Les sections suivantes décrivent les entrées pour chacune des étapes de l’Assistant à suivre pour créer la machine virtuelle Terraform pour Linux. La section suivante répertorie les entrées requises pour configurer chacune de ces étapes.

## <a name="details-on-the-create-terraform-tab"></a>Détails de l’onglet Créer Terraform

Entrez les informations suivantes sur l’onglet **Créer Terraform** :

1. **Concepts de base**
    
   * **Nom** : nom de votre machine virtuelle Terraform.
   * **Nom d’utilisateur** : premier ID de connexion du compte.
   * **Mot de passe** : premier mot de passe du compte. (Vous pouvez utiliser une clé publique SSH au lieu d’un mot de passe).
   * **Abonnement**: abonnement sur lequel la machine doit être créée et facturée. Vous devez disposer des privilèges de création de ressources pour cet abonnement.
   * **Groupe de ressources** : nouveau groupe de ressources ou groupe de ressources existant.
   * **Emplacement** : centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau.

2. **Paramètres supplémentaires**

   * **Taille**: Taille de la machine virtuelle. 
   * **Type de disque de machine virtuelle** : disque SSD ou HDD.

3. **Récapitulatif Terraform**

   * Vérifiez que toutes les informations que vous avez saisies sont correctes. 

4. **Acheter**

   * Pour démarrer le processus d’approvisionnement, sélectionnez **Acheter**. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle ne génère pas de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l’étape de définition de la taille.

L’image de la machine virtuelle Terraform effectue les étapes suivantes :

* Crée une machine virtuelle à laquelle le système assigne une identité basée sur l’image d’Ubuntu 16.04 LTS.
* Installe l’extension des identités managées pour ressources Azure sur la machine virtuelle pour permettre l’envoi de jetons OAuth pour les ressources Azure.
* Assigne des autorisations RBAC à l’identité gérée pour octroyer des droits de propriétaire sur le groupe de ressources.
* Crée un dossier de modèle Terraform (tfTemplate).
* Préconfigure l’état à distance de Terraform avec le backend Azure.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Utiliser et configurer la machine virtuelle Terraform pour Linux

Une fois la machine virtuelle créée, procédez comme suit :

1. Connectez-vous à la machine virtuelle à l’aide de SSH. Utilisez les informations d’identification de compte que vous avez créées dans la section précédente. Sous Windows, vous pouvez télécharger un outil client SSH tel que [Putty](https://www.putty.org/).

1. Accordez des autorisations de contributeur pour l’intégralité de l’abonnement aux identités managées pour les ressources Azure sur la machine virtuelle. 

    Les autorisations de contributeur permettent aux identités managées pour les ressources Azure sur la machine virtuelle d’utiliser Terraform pour créer des ressources en dehors du groupe de ressources de la machine virtuelle. Effectuez cette action en exécutant le script suivant : 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Ce script utilise le mécanisme de [connexion interactive Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) pour s’authentifier auprès d’Azure. Ce processus affecte l’[autorisation Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour l’intégralité de l’abonnement. 

1. Un backend d’état à distance Terraform est créé sur la machine virtuelle. Pour l’activer dans votre déploiement Terraform, vous devez copier le fichier `remoteState.tf` à la racine des scripts Terraform.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Pour plus d’informations sur la gestion de l’état à distance, consultez l’[état à distance Terraform](https://www.terraform.io/docs/state/remote.html). La clé d’accès de stockage est exposée dans ce fichier. Excluez-la avant de valider les fichiers de configuration Terraform dans le contrôle de code source.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)