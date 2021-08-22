---
title: Créer une préférence personnalisée dans Azure Automanage pour machines virtuelles
description: Découvrez comment modifier la configuration de l’environnement dans Azure Automanage, et comment définir vos propres préférences.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: edd82086dba6f603c50edd23c3f3757b95dcdb9d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468361"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Créer une préférence personnalisée dans Azure Automanage pour machines virtuelles

La fonctionnalité Azure Automanage des bonnes pratiques pour les machines virtuelles comporte des environnements par défaut qui peuvent être modifiés si nécessaire. Cet article explique comment définir vos propres préférences quand vous activez l’autogestion sur une machine virtuelle nouvelle ou déjà existante.

Nous prenons actuellement en charge les personnalisations de [Sauvegarde Azure](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) et de [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Vous ne pouvez pas changer l’environnement ou les préférences sur votre machine virtuelle tant qu’Automanage est activé. Vous devez désactiver Automanage pour cette machine virtuelle, puis réactiver Automanage avec l’environnement et les préférences de configuration souhaités.


## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce tutoriel. Veuillez passer à un abonnement avec paiement à l’utilisation.

> [!IMPORTANT]
> L’autorisation Azure RBAC suivante est nécessaire pour activer Automanage : Rôle de **Propriétaire**, ou rôle de **Contributeur** associé au rôle d’**Administrateur de l’accès utilisateur**.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Activer l’Autogestion pour machines virtuelles sur une machine virtuelle existante

1. Dans la barre de recherche, recherchez et sélectionnez **Automanage - Azure machine best practices** (Automanage - Bonnes pratiques pour les machines virtuelles Azure).

2. Sélectionnez l’option **Enable on existing VM** (Activer sur une machine virtuelle existante).

3. Dans le panneau **Sélectionner des machines** :
    1. Filtrez la liste des machines virtuelles selon votre **abonnement** et votre **groupe de ressources**.
    1. Cochez la case des machines virtuelles que vous souhaitez intégrer.
    1. Cliquez sur le bouton **Sélectionner**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Sélectionnez une machine virtuelle existante dans la liste des machines virtuelles disponibles.":::

    > [!NOTE]
    > Cliquez sur **Show ineligible machines** (Afficher les machines non éligibles) pour voir la liste des machines non prises en charge et la raison associée. 

4. Sous **Configuration**, cliquez sur **Compare Environments** (Comparer les environnements).

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Comparer les environnements.":::

5. Dans le panneau **Détails de l’environnement**, sélectionnez un environnement dans le menu déroulant : *Dev/Test* pour les tests, *Prod* pour la production, puis cliquez sur **OK**

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Parcourir l’environnement de production.":::

6. Après avoir sélectionné votre environnement, vous pouvez sélectionner **Configuration preferences** (Préférences de configuration). Par défaut, la préférence relative aux bonnes pratiques Azure est utilisée. Cette préférence contient et utilise les paramètres recommandés pour chaque service. Modifiez ces paramètres en créant une préférence personnalisée : 
    1. Cliquez sur **Créer des préférences**.
    1. Dans le panneau **Créer une préférence de configuration**, sous l’onglet De base, entrez les informations suivantes :
        1. Abonnement
        1. Resource group
        1. Nom de la préférence
        1. Région

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Entrez les préférences de configuration.":::

7. Modifiez les préférences de configuration souhaitées.
        
    > [!NOTE]
    > Quand vous changez les configurations d’un environnement, seules les modifications qui s’inscrivent dans le respect de nos bonnes pratiques sont autorisées.

8. Passez en revue les détails de votre configuration.
9. Cliquez sur le bouton **Créer**.

10. Cliquez sur le bouton **Activer**.


## <a name="disable-automanage-for-vms"></a>Désactiver l’Autogestion pour machines virtuelles

Vous pouvez cesser rapidement d’utiliser l’Autogestion Azure pour machines virtuelles en désactivant la gestion automatique.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Désactivation de l’Autogestion sur une machine virtuelle":::

1. Accédez à la page **Autogestion – Bonnes pratiques pour les machines virtuelles** qui liste toutes vos machines virtuelles qui sont gérées automatiquement.
1. Cochez la case des machines virtuelles que vous souhaitez désactiver.
1. Cliquez sur le bouton **Désactiver la gestion automatique**.
1. Lisez attentivement le message dans la fenêtre contextuelle qui s’affiche avant d’accepter de **Désactiver**.


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé un nouveau groupe de ressources pour essayer l’Autogestion Azure pour machines virtuelles, et que vous n’en avez plus besoin, vous pouvez le supprimer. La suppression du groupe supprime également la machine virtuelle, ainsi que toutes les autres ressources se trouvant dans le groupe.

L’Autogestion Azure crée des groupes de ressources par défaut dans lesquels stocker les ressources. Vérifiez les groupes de ressources qui suivent la convention de nommage « DefaultResourceGroupRegionName » ou « AzureBackupRGRegionName » afin de nettoyer toutes les ressources.

1. Sélectionnez le **groupe de ressources**.
1. Dans la page du groupe de ressources, sélectionnez **Supprimer**.
1. Lorsque vous y êtes invité, confirmez le nom du groupe de ressources, puis sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes 

Pour obtenir les réponses aux questions les plus fréquemment posées, consultez notre FAQ. 

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ)](faq.yml)