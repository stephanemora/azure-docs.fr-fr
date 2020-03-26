---
title: Tutoriel - Valider un réseau hub-and-spoke dans Azure avec Terraform
description: Didacticiel pour valider une topologie de réseau hub-and-spoke avec tous les réseaux virtuels connectés entre eux.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159191"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Tutoriel : Valider un réseau hub-and-spoke dans Azure avec Terraform

Dans cet article, vous alles exécuter les fichiers Terraform créés dans l’article précédent de cette série. Le résultat est une validation de la connectivité entre les réseaux virtuels de démonstration.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Utiliser HCL (HashiCorp Language) pour implémenter le réseau virtuel hub dans la topologie hub-and-spoke
> * Utiliser Terraform Plan pour vérifier les ressources à déployer
> * Utilisez Terraform Apply pour créer les ressources dans Azure
> * Vérifier la connectivité entre les différents réseaux
> * Utiliser Terraform pour détruire toutes les ressources

## <a name="prerequisites"></a>Prérequis

1. [Créez une topologie de réseau hybride hub-and-spoke avec Terraform dans Azure](./terraform-hub-spoke-introduction.md).
1. [Créez un réseau virtuel local avec Terraform dans Azure](./terraform-hub-spoke-on-prem.md).
1. [Créez un réseau virtuel hub avec Terraform dans Azure](./terraform-hub-spoke-hub-network.md).
1. [Créez une appliance de réseau virtuel hub avec Terraform dans Azure](./terraform-hub-spoke-hub-nva.md).
1. [Créez un réseau virtuel spoke avec Terraform dans Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Vérifier votre configuration

Après avoir rempli les [prérequis](#prerequisites), vérifiez que les fichiers de configuration appropriés sont présents.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Déplacez-vous dans le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Déplacez-vous dans le nouveau répertoire :

    ```bash
    cd hub-spoke
    ```

1. Exécutez la commande `ls` pour vérifier que les fichiers de configuration `.tf` créés dans les didacticiels précédents sont répertoriés :

    ![Fichiers de configuration de démonstration de Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Déployer les ressources

1. Initialiser le fournisseur Terraform :
    
    ```bash
    terraform init
    ```
    
    ![Exemples de résultats de la commande « terraform init »](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Exécutez la commande `terraform plan` pour voir l’effet du déploiement avant l’exécution :

    ```bash
    terraform plan
    ```
    
    ![Exemples de résultats de la commande « terraform plan »](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Déployer la solution :

    ```bash
    terraform apply
    ```
    
    Entrez `yes` lorsque vous êtes invité à confirmer le déploiement.

    ![Exemples de résultats de la commande « terraform apply »](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Tester le réseau virtuel hub et les réseaux virtuels spoke

Cette section explique comment tester la connectivité entre l’environnement local simulé et le réseau virtuel hub.

1. Dans le portail Azure, accédez au groupe de ressources **onprem-vnet-rg**.

1. Sous l’onglet **onprem-vnet-rg**, sélectionnez la machine virtuelle nommée **onprem-vm**.

1. Sélectionnez **Connecter**.

1. En regard du texte **Connexion avec un compte local de machine virtuelle**, copiez la commande **ssh** dans le Presse-papiers.

1. Depuis un invite Linux, exécutez `ssh` pour vous connecter à l’environnement local simulé. Utilisez le mot de passe spécifié dans le fichier de paramètres `on-prem.tf`.

1. Exécutez la commande `ping` pour tester la connectivité à la machine virtuelle jumpbox dans le réseau virtuel hub :

   ```bash
   ping 10.0.0.68
   ```

1. Exécutez la commande `ping` pour tester la connectivité aux machines virtuelles jumpbox dans chaque spoke :

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Pour quitter la session ssh sur la machine virtuelle **onprem-vm**, entrez `exit` et appuyez sur &lt;Entrée >.

## <a name="troubleshoot-vpn-issues"></a>Résoudre des problèmes de VPN

Pour plus d’informations sur la résolution de problèmes de VPN, voir l’article [Résoudre les problèmes d’une connexion VPN hybride](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin des ressources créées dans la série de didacticiels, supprimez-les.

1. Supprimez les ressources déclarées dans le plan :

    ```bash
    terraform destroy
    ```

    Entrez `yes` lorsque vous êtes invité à confirmer la suppression des ressources.

1. Remplacez les répertoires par le répertoire parent :

    ```bash
    cd ..
    ```

1. Supprimez le répertoire `hub-scope` (avec tous ses fichiers) :

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [En savoir plus sur l’utilisation de Terraform dans Azure](/azure/terraform)