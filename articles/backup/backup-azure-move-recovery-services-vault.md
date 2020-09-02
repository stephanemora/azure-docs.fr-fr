---
title: Comment déplacer des coffres Azure Backup Recovery Services
description: Instructions pour déplacer un coffre Recovery Services entre des abonnements Azure et des groupes de ressources.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 0b06b12c2bb989e4c0fc4e04440fc5f452df2da3
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827118"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Déplacer un coffre Recovery Services entre des abonnements Azure et des groupes de ressources

Cet article explique comment déplacer un coffre Recovery Services configuré pour Sauvegarde Azure entre des abonnements Azure ou vers un autre groupe de ressources dans le même abonnement. Vous pouvez utiliser le portail Azure ou PowerShell pour déplacer un coffre Recovery Services.

## <a name="supported-regions"></a>Régions prises en charge

Le déplacement de ressources pour un coffre Recovery Services est pris en charge dans les régions suivantes : Australie Est, Australie Sud-Est, Canada Centre, Canada Est, Asie Sud-Est, Asie Est, USA Centre, USA Centre Nord, USA Est, USA Est 2, USA Centre Sud, USA Centre-Ouest, USA Centre-Ouest 2, USA Ouest, USA Ouest 2, Inde Centre, Inde Sud, Japon Est, Japon Ouest, Corée Centre, Corée Sud, Europe Nord, Europe Ouest, Afrique du Sud Nord, Afrique du Sud Ouest, Royaume-Uni Sud et Royaume-Uni Ouest.

## <a name="unsupported-regions"></a>Régions non prises en charge

France Centre, France Sud, Allemagne Nord-Est, Allemagne Centre, US Gov Iowa, Chine Nord, Chine Nord2, Chine Est, Chine Est2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Conditions préalables au déplacement d'un coffre Recovery Services

- Lors du déplacement du coffre entre des groupes de ressources, les groupes de ressources source et cible sont verrouillés pour empêcher les opérations d'écriture et de suppression. Pour plus d’informations, consultez cet [article](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Seul l'abonnement Administrateur dispose des autorisations pour déplacer un coffre.
- Pour déplacer des coffres entre des abonnements, il faut que l’abonnement cible se trouve dans le même locataire que l’abonnement source et son état doit être défini sur Activé.
- Vous devez être autorisé à effectuer des opérations d’écriture sur le groupe de ressources cible.
- Le déplacement du coffre change seulement le groupe de ressources. Le coffre Recovery Services se trouve au même endroit et ne peut pas être changé.
- Vous ne pouvez déplacer qu'un seul coffre Recovery Services à la fois par région.
- Si une machine virtuelle n’est pas déplacée avec le coffre Recovery Services entre des abonnements ou vers un nouveau groupe de ressources, les points de récupération actuels de la machine virtuelle restent intacts dans le coffre jusqu’à leur expiration.
- Que la machine virtuelle soit déplacée ou non avec le coffre, vous pouvez toujours la restaurer à partir de l’historique des sauvegardes conservé dans le coffre.
- Azure Disk Encryption exige que le coffre de clés et les machines virtuelles se trouvent dans la même région et le même abonnement Azure.
- Pour déplacer une machine virtuelle avec des disques managés, consultez cet [article](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Les options de déplacement des ressources déployées avec le modèle classique diffèrent selon que vous déplacez les ressources au sein d’un abonnement ou vers un nouvel abonnement. Pour plus d’informations, consultez cet [article](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Les stratégies de sauvegarde définies pour le coffre sont conservées après le déplacement du coffre entre des abonnements ou vers un groupe de ressources.
- Vous pouvez uniquement déplacer un coffre contenant l’un des types d’éléments de sauvegarde suivants. Vous devez arrêter les éléments de sauvegarde de types non répertoriés ci-dessous et supprimer les données définitivement avant le déplacement du coffre.
  - Machines virtuelles Azure
  - Agent Microsoft Azure Recovery Services (MARS)
  - Serveur Sauvegarde Microsoft Azure (MABS)
  - Data Protection Manager (DPM)
- Si vous déplacez un coffre contenant des données de sauvegarde de machine virtuelle entre des abonnements, vous devez déplacer vos machines virtuelles dans le même abonnement et utiliser le même nom de groupe de ressources de machine virtuelle cible (celui de l’ancien abonnement) pour continuer les sauvegardes.

> [!NOTE]
> Le déplacement de coffres Recovery Services pour Sauvegarde Azure d’une région Azure à une autre n’est pas pris en charge.<br><br>
> Si vous avez configuré des machines virtuelles (IaaS Azure, Hyper-V, VMware) ou des machines physiques pour la reprise d’activité avec **Azure Site Recovery**, l’opération de déplacement est bloquée. Si vous souhaitez déplacer des coffres pour Azure Site Recovery, consultez [cet article](../site-recovery/move-vaults-across-regions.md) pour en savoir plus sur le déplacement manuel des coffres.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Utiliser le portail Azure pour déplacer un coffre Recovery Services vers un autre groupe de ressources

Pour déplacer un coffre Recovery Services et ses ressources associées vers un autre groupe de ressources :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Ouvrez la liste des **coffres Recovery Services** et sélectionnez le coffre à déplacer. Quand le tableau de bord du coffre s’ouvre, il s’affiche comme illustré dans l’image suivante.

   ![Ouvrir le coffre Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Si vous ne voyez pas les informations **Essentials** pour votre coffre, sélectionnez l’icône de liste déroulante. Vous devez maintenant voir les informations Essentials pour votre coffre.

   ![Onglet Informations Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Dans le menu Vue d’ensemble du coffre, sélectionnez **Modifier** à côté du **groupe de ressources** pour ouvrir le volet **Déplacer des ressources**.

   ![Changer le groupe de ressources](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Dans le volet **Déplacer des ressources**, pour le coffre sélectionné, il est recommandé de déplacer les ressources associées facultatives en cochant la case comme illustré dans l’image suivante.

   ![Déplacer un abonnement](./media/backup-azure-move-recovery-services/move-resource.png)

5. Pour ajouter le groupe de ressources cible, dans la liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant ou l’option **Créer un groupe**.

   ![Créer une ressource](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Après avoir ajouté le groupe de ressources, confirmez l’option **Je comprends que les outils et les scripts associés aux ressources déplacées ne fonctionnent pas tant que je ne les mets pas à jour pour utiliser de nouveaux ID de ressource**, puis sélectionnez **OK** pour effectuer le déplacement du coffre.

   ![Message de confirmation](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Utiliser le portail Azure pour déplacer un coffre Recovery Services vers un autre abonnement

Vous pouvez déplacer un coffre Recovery Services et ses ressources associées vers un autre abonnement.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Ouvrez la liste des coffres Recovery Services et sélectionnez le coffre à déplacer. Quand le tableau de bord du coffre s’ouvre, il apparaît comme illustré dans l’image suivante.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Si vous ne voyez pas les informations **Essentials** pour votre coffre, sélectionnez l’icône de liste déroulante. Vous devez maintenant voir les informations Essentials pour votre coffre.

    ![Onglet Informations Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Dans le menu Vue d’ensemble du coffre, sélectionnez **Modifier** à côté d’**Abonnement**  pour ouvrir le volet **Déplacer des ressources**.

   ![Changer l’abonnement](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Sélectionnez les ressources à déplacer : nous vous recommandons d’utiliser ici l’option **Sélectionner tout** pour sélectionner toutes les ressources facultatives listées.

   ![Déplacer une ressource](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement cible où vous voulez déplacer le coffre.
6. Pour ajouter le groupe de ressources cible, dans la liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant ou l’option **Créer un groupe**.

   ![Ajouter un abonnement](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Sélectionnez l’option **Je comprends que les outils et les scripts associés aux ressources déplacées ne fonctionnent pas tant que je ne les mets pas à jour pour utiliser de nouveaux ID de ressource;** , puis sélectionnez **OK**.

> [!NOTE]
> La sauvegarde entre abonnements (le coffre RS et les machines virtuelles protégées se trouvent dans des abonnements différents) n’est pas un scénario pris en charge. En outre, pendant l’opération de déplacement du coffre, vous ne pouvez pas modifier l’option de redondance du stockage de stockage localement redondant (LRS) en stockage globalement redondant (GRS) et inversement.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Utiliser PowerShell pour déplacer un coffre Recovery Services

Pour déplacer un coffre Recovery Services vers un autre groupe de ressources, utilisez l’applet de commande `Move-AzureRMResource`. `Move-AzureRMResource` nécessite le nom de la ressource et le type de ressource. Vous pouvez les obtenir avec l’applet de commande `Get-AzureRmRecoveryServicesVault`.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Pour déplacer les ressources vers un autre abonnement, incluez le paramètre `-DestinationSubscriptionId`.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Après l’exécution des applets de commande ci-dessus, vous devez confirmer que vous voulez déplacer les ressources spécifiées. Tapez **O** pour le confirmer. Après une validation réussie, la ressource est déplacée.

## <a name="use-cli-to-move-recovery-services-vault"></a>Utiliser l'interface CLI pour déplacer un coffre Recovery Services

Pour déplacer un coffre Recovery Services vers un autre groupe de ressources, utilisez l’applet de commande suivante :

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Pour déplacer des ressources vers un nouvel abonnement, spécifiez le paramètre `--destination-subscription-id`.

## <a name="post-migration"></a>Après la migration

1. Définissez/vérifiez les contrôles d’accès pour les groupes de ressources.  
2. La fonctionnalité de création de rapports et d’analyse de Sauvegarde doit être reconfigurée pour le coffre une fois le déplacement terminé. La configuration antérieure est perdue lors de l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements.

Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).
