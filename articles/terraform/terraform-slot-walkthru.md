---
title: Emplacement de déploiement fournisseur Terraform avec Azure
description: Didacticiel sur l’emplacement de déploiement fournisseur Terraform avec Azure
keywords: terraform, devops, machine virtuelle, Azure, emplacements de déploiement
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Utilisation de Terraform pour approvisionner une infrastructure avec les emplacements de déploiement Azure

Les [emplacements de déploiement Azure](/azure/app-service/web-sites-staged-publishing) vous permettent de permuter entre différentes versions de votre application (de production et de préproduction, par exemple) pour minimiser l’impact des déploiements défaillants. Cet article illustre un exemple d’utilisation d’emplacements de déploiement en vous guidant à travers le déploiement de deux applications via GitHub et Azure. Une de ces applications est hébergée dans un « emplacement de production », tandis que la deuxième est hébergée dans un « emplacement de préproduction ». (Les noms « production » et « intermédiaire ». Il peut s’agir de tout type d’emplacement requis dans le cadre de votre scénario.) Une fois que vous avez configuré vos emplacements de déploiement, vous pouvez utiliser Terraform pour permuter entre les deux emplacements en fonction de vos besoins.

## <a name="prerequisites"></a>Prérequis


- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Compte GitHub** : un compte [GitHub](http://www.github.com) est nécessaire pour effectuer une duplication (fork) et utiliser le référentiel GitHub de test.

## <a name="create-and-apply-the-terraform-plan"></a>Créer et appliquer le plan Terraform

1. Accédez au [portail Azure](http://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview)et, si ce n’est pas déjà fait, sélectionnez **Bash** en tant qu’environnement.

    ![Invite Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Remplacez le répertoire par le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `deploy`.

    ```bash
    mkdir deploy
    ```

1. Créez un répertoire nommé `swap`.

    ```bash
    mkdir swap
    ```

1. Vérifiez que les deux répertoires ont été créés avec succès à l’aide de la commande bash `ls`.

    ![Cloud Shell après la création des répertoires](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Remplacez le répertoire par le répertoire `deploy`.

    ```bash
    cd deploy
    ```

1. À l’aide de l’[éditeur VI](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), créez un fichier nommé `deploy.tf`, qui contiendra la [configuration Terraform](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Passez en mode insertion en appuyant sur la touche de la lettre `i`.

1. Collez le code suivant dans l’éditeur :

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Appuyez sur la touche  **&lt;Échap>** pour quitter le mode insertion.

1. Enregistrez le fichier et quittez l’éditeur VI en entrant la commande suivante, puis en appuyant sur **&lt;Entrée>** :

    ```bash
    :wq
    ```

1. Une fois que le fichier a été créé, vous pouvez vérifier son contenu.

    ```bash
    cat deploy.tf
    ```

1. Initialisez Terraform.

    ```bash
    terraform init
    ```

1. Créez le plan Terraform.

    ```bash
    terraform plan
    ```

1. Approvisionnez les ressources définies dans le fichier de configuration `deploy.tf`. (Confirmez l’action en entrant `yes` à l’invite.)

    ```bash
    terraform apply
    ```

1. Fermez la fenêtre Cloud Shell.

1. Dans le menu principal du portail Azure, sélectionnez **Groupes de ressources**.

    ![Groupes de ressources dans le portail Azure](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Dans l’onglet **Groupes de ressources**, sélectionnez **slotDemoResourceGroup**.

    ![Groupe de ressources créé par Terraform](./media/terraform-slot-walkthru/resource-group.png)

Une fois que vous avez terminé, vous pouvez voir toutes les ressources créées par Terraform.

![Ressources créées par Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Dupliquer (fork) le projet de test

Avant de pouvoir tester la création et permuter entre les emplacements de déploiement, vous devez dupliquer (fork) le projet de test à partir de GitHub.

1. Accédez au [référentiel awesome-terraform sur GitHub](https://github.com/Azure/awesome-terraform).

1. Dupliquez (fork) le **référentiel awesome-terraform**.

    ![Dupliquer (fork) le référentiel awesome-terraform GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Suivez les invites à l’écran pour effectuer la duplication (fork) vers votre environnement.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Effectuer le déploiement dans les emplacements de déploiement à partir de GitHub

Une fois que vous avez dupliqué (fork) le référentiel du projet de test, configurez les emplacements de déploiement en procédant comme suit :

1. Dans le menu principal du portail Azure, sélectionnez **Groupes de ressources**.

1. Sélectionnez **slotDemoResourceGroup**.

1. Sélectionnez **slotAppService**.

1. Sélectionnez **Options de déploiement**.

    ![Options de déploiement pour une ressource App Service](./media/terraform-slot-walkthru/deployment-options.png)

1. Dans l’onglet **Option de déploiement**, sélectionnez **Choisir une source**, puis **GitHub**.

    ![Sélectionner une source de déploiement](./media/terraform-slot-walkthru/select-source.png)

1. Une fois qu’Azure a établi la connexion et affiché toutes les options, sélectionnez **Autorisation**.

1. Dans l’onglet **Autorisation**, sélectionnez **Autoriser** et fournissez les informations d’identification requises par Azure pour accéder à votre compte GitHub. 

1. Une fois vos informations d’identification GitHub validées par Azure, vous recevez un message indiquant que le processus d’autorisation est terminé. Sélectionnez **OK** pour fermer l’onglet **Autorisation**.

1. Sélectionnez **Choisir votre organisation**, puis le nom de votre organisation.

1. Sélectionnez **Choisir un projet**.

1. Dans l’onglet **Choisir un projet**, sélectionnez le projet **awesome-terraform**.

    ![Choisir le projet awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Sélectionnez **Choisir une branche**.

1. Dans l’onglet **Choisir une branche**, sélectionnez **maître**.

    ![Choisir la branche maîtresse](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Dans l’onglet **Option de déploiement**, sélectionnez **OK**.

À ce stade, vous avez déployé l’emplacement de production. Pour déployer l’emplacement de préproduction, effectuez toutes les étapes précédentes de cette section en apportant uniquement les modifications suivantes :

- À l’étape 3, sélectionnez la ressource **slotAppServiceSlotOne**.

- À l’étape 13, sélectionnez la branche « travail » au lieu de la branche maîtresse.

    ![Choisir la branche de travail](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Tester les déploiements d’applications

Dans les sections précédentes, vous avez configuré deux emplacements, **slotAppService** et **slotAppServiceSlotOne**, pour déployer les applications à partir de différentes branches dans GitHub. Nous allons maintenant afficher un aperçu des applications web pour vérifier qu’elles ont été déployées avec succès.

Effectuez les étapes suivantes deux fois, en sélectionnant **slotAppService** à l’étape 3 la première fois, puis **slotAppServiceSlotOne** la deuxième fois :

1. Dans le menu principal du portail Azure, sélectionnez **Groupes de ressources**.

1. Sélectionnez **slotDemoResourceGroup**.

1. Sélectionnez **slotAppService** ou **slotAppServiceSlotOne**.

1. Dans la page de vue d’ensemble, sélectionnez **URL**.

    ![Sélectionner l’URL dans l’onglet de vue d’ensemble pour effectuer le rendu de l’application](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Azure peut mettre plusieurs minutes pour créer et déployer le site à partir de GitHub.
>
>

Pour l’application web **slotAppService**, une page bleue avec le titre de page **Slot Demo App 1** s’affiche. Pour l’application web **slotAppServiceSlotOne**, une page verte avec le titre de page **Slot Demo App 2** s’affiche.

![Afficher un aperçu des applications pour vérifier qu’elles ont été correctement déployées](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Permuter entre les deux emplacements de déploiement

Pour tester la permutation entre les deux emplacements de déploiement, procédez comme suit :
 
1. Basculez vers l’onglet de navigateur exécutant **slotAppService** (l’application avec la page bleue). 

1. Revenez au portail Azure dans un onglet séparé.

1. Ouvrez l’invite de commandes Cloud Shell.

1. Remplacez le répertoire par le répertoire **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. À l’aide de l’éditeur VI, créez un fichier nommé `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Passez en mode insertion en appuyant sur la touche de la lettre `i`.

1. Collez le code suivant dans l’éditeur :

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Appuyez sur la touche  **&lt;Échap>** pour quitter le mode insertion.

1. Enregistrez le fichier et quittez l’éditeur VI en entrant la commande suivante, puis en appuyant sur **&lt;Entrée>** :

    ```bash
    :wq
    ```

1. Initialisez Terraform.

    ```bash
    terraform init
    ```

1. Créez le plan Terraform.

    ```bash
    terraform plan
    ```

1. Approvisionnez les ressources définies dans le fichier de configuration `swap.tf`. (Confirmez l’action en entrant `yes` à l’invite.)

    ```bash
    terraform apply
    ```

1. Une fois que Terraform a permuté les emplacements, revenez dans l’onglet de navigateur utilisé pour le rendu de l’application web **slotAppService** et actualisez la page. 

L’application web dans votre emplacement de préproduction **slotAppServiceSlotOne** a été permutée avec celle de l’emplacement de production et le rendu affiche à présent une page verte. 

![Les emplacements de déploiement ont été permutés](./media/terraform-slot-walkthru/slots-swapped.png)

Pour revenir à la version de production d’origine de l’application, réappliquez le plan Terraform créé à partir du fichier de configuration `swap.tf`.

```bash
terraform apply
```

Une fois la permutation effectuée, la configuration d’origine est affichée.