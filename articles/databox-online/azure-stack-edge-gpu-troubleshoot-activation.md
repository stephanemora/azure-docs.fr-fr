---
title: Utiliser le portail Azure pour résoudre des erreurs d’activation liées à Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit comment résoudre les problèmes liés à l’activation d’Azure Stack Edge Pro avec GPU et au coffre de clés.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 1f6729fc0a723a21f66380a397a222bef23cba9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750318"
---
# <a name="troubleshoot-activation-or-secret-deletion-issues-on-azure-key-vault-for-your-azure-stack-edge-pro-gpu-device"></a>Résoudre les problèmes d’activation ou de suppression du secret sur Azure Key Vault pour votre appareil Azure Stack Edge Pro avec GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment résoudre des erreurs d’activation survenant sur votre appareil Azure Stack Edge Pro avec GPU. 


## <a name="activation-errors"></a>Erreurs d’activation

Le tableau suivant résume les erreurs liées à l’activation d’appareil et la résolution recommandée correspondante.

| Message d’erreur| Résolution recommandée |
|------------------------------------------------------|--------------------------------------|
| Si le coffre Azure Key Vault utilisé pour l’activation est supprimé avant l’activation de l’appareil avec la clé d’activation, vous recevez cette erreur. <br> ![Erreur de coffre de clés 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Si le coffre de clés a été supprimé, vous pouvez le restaurer si le coffre est dans une période de protection contre la suppression définitive. Suivez les étapes décrites dans [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). <br>Si la période de protection contre la suppression définitive s’est écoulée, vous devez créer un coffre de clés à l’aide du panneau [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). |
| Si Azure Key Vault est supprimé après l’activation de l’appareil, et que vous essayez alors d’effectuer une opération impliquant un chiffrement (par exemple **Ajouter un utilisateur**, **Ajouter un partage** ou **Configurer le calcul**), vous recevez cette erreur. <br> ![Erreur de coffre de clés 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Si le coffre de clés a été supprimé, vous pouvez le restaurer s’il est dans une période de protection contre la suppression définitive. Suivez les étapes décrites dans [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). <br>Si la période de protection contre la suppression définitive s’est écoulée, vous devez créer un coffre de clés comme décrit dans [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault). |
| Si la génération de la clé d’activation échoue en raison d’une erreur, vous recevez cette erreur. La notification comprend plus de détails. <br> ![Erreur de coffre de clés 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Assurez-vous que les ports et les URL spécifiés dans [Accès à Azure Key Vault derrière un pare-feu](../key-vault/general/access-behind-firewall.md) sont ouverts sur votre pare-feu afin de pouvoir accéder au coffre de clés. Patientez quelques minutes, puis recommencez l’opération. Si le problème persiste, contactez le support technique Microsoft. |
| Si l’utilisateur dispose d’autorisations en lecture seule, il n’est pas autorisé à générer une clé d’activation et cette erreur est affichée. <br> ![Erreur de coffre de clés 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Cela peut être dû au fait que vous ne disposez pas du droit d’accès ou que `Microsoft.KeyVault` n’est pas inscrit.<li>Vérifiez que vous disposez d’un accès propriétaire ou contributeur au niveau du groupe de ressources utilisé pour votre ressource Azure Stack Edge.</li><li>Assurez-vous que le fournisseur de ressources `Microsoft.KeyVault` est inscrit. Pour inscrire un fournisseur de ressources, accédez à l’abonnement utilisé pour la ressource Azure Stack Edge. Accédez à **Fournisseurs de ressources**, recherchez *Microsoft.KeyVault*, sélectionnez et cliquez sur **Inscrire**. Pour plus d’informations, consultez [Inscrire les fournisseurs de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).</li> |


## <a name="unregistered-resource-provider-errors"></a>Erreurs liées à des fournisseurs de ressources désinscrits

| Message d’erreur| Résolution recommandée |
|------------------------------------------------------|--------------------------------------|
| Si le fournisseur de ressources Key Vault n’est pas inscrit, vous verrez une erreur lors de la création d’un coffre de clés pendant la génération de la clé d’activation. <br> <!--![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | La clé d’activation ne sera pas générée. <br>Assurez-vous que le fournisseur de ressources `Microsoft.KeyVault` est inscrit. Pour inscrire un fournisseur de ressources, accédez à l’abonnement utilisé pour la ressource Azure Stack Edge. Accédez à **Fournisseurs de ressources**, recherchez *Microsoft.KeyVault*, sélectionnez et cliquez sur **Inscrire**. <br>Pour plus d’informations, consultez [Inscrire les fournisseurs de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).</li> |
| Si le fournisseur de ressources de stockage n’est pas inscrit, vous verrez une erreur lors de la création d’un compte de stockage pour les journaux d’audit. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Cette erreur n’est pas une erreur bloquante et la clé d’activation sera générée. <br>Le fournisseur de ressources de stockage est généralement inscrit automatiquement, mais si ce n’est pas le cas, suivez les étapes décrites dans [Inscrire un fournisseur de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers) pour inscrire `Microsoft.Storage` auprès de votre abonnement.  |

## <a name="key-vault-or-secret-deletion-errors"></a>Erreurs de suppression du coffre de clés ou du secret

| Message d’erreur| Résolution recommandée |
|------------------------------------------------------|--------------------------------------|
| Si la clé d’intégrité de canal dans Azure Key Vault a été supprimée et que vous essayez d’effectuer des opérations qui impliquent le chiffrement (par exemple, **Ajouter un utilisateur**, **Ajouter un partage** ou **Configurer le calcul**), cette erreur s’affiche.  |Si la clé d’intégrité du canal dans le coffre de clés a été supprimée, mais qu’elle est toujours dans la période de suppression définitive, suivez les étapes décrites dans [Annuler la suppression de clé du coffre de clés](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Si la durée de protection contre la purge est écoulée et si vous avez sauvegardé la clé, vous pouvez restaurer la clé à partir de la sauvegarde. Dans le cas contraire, vous ne pourrez pas récupérer la clé. Contactez le Support Microsoft pour les étapes suivantes. |
<!--|Supprimer ceci après avoir consulté Noopur - Le client n’a rien à faire - Si la clé d’intégrité de canal dans Azure Key Vault a été supprimée avant l’activation de l’appareil et que la clé d’activation est régénérée, cette erreur s’affiche. <br> ![Erreur de coffre de clés 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png) | La clé d’intégrité du canal sera recréée et les métadonnées seront mises à jour.
|-->

## <a name="audit-logging-errors"></a>Erreurs d’enregistrement d’audit

| Message d’erreur| Résolution recommandée |
|------------------------------------------------------|--------------------------------------|
| Si la création du paramètre de diagnostic échoue pour votre coffre de clés, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Il ne s’agit pas d’une erreur bloquante et la clé d’activation sera générée. <br> Vous pouvez [créer manuellement un paramètre de diagnostic pour stocker vos journaux d’audit](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs). |
| En cas d’échec de la création du compte de stockage, par exemple, parce qu’un compte existe déjà pour le nom que vous avez spécifié, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Vous pouvez créer manuellement un compte de stockage et le lier au paramètre de diagnostic de votre coffre de clés. Ce compte est ensuite utilisé pour stocker les journaux d’audit. <br> Pour plus d’informations, consultez [Créer un compte de stockage pour vos journaux](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs).  |
|Si l’identité managée affectée par le système pour votre ressource Azure Stack Edge est supprimée, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Vous verrez une alerte dans le panneau Sécurité pour votre ressource Azure Stack Edge. Sélectionnez cette alerte pour [créer une nouvelle identité managée par le biais du panneau Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)  |
| Si l’identité managée n’a pas accès au coffre de clés, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Vous verrez une alerte dans le panneau Sécurité pour votre ressource Azure Stack Edge. Sélectionnez cette alerte pour [accorder à l’identité managée l’accès au coffre de clés par le biais du panneau Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).  |

## <a name="resource-move-errors"></a>Erreurs de déplacement de ressources

| Message d’erreur| Résolution recommandée |
|------------------------------------------------------|--------------------------------------|
| Si la ressource de coffre de clés est déplacée entre des groupes de ressources ou des abonnements, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Le déplacement des ressources de coffre de clés est traité de la même façon que la suppression du coffre de clés. Vous pouvez restaurer le coffre de clés s’il est dans une période de protection contre la suppression définitive. Si la période de protection contre la suppression définitive s’est écoulée, vous devez créer un coffre de clés. Pour plus d’informations sur l’un des cas ci-dessus, consultez [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).    |
| Si l’abonnement que vous utilisez, est déplacé entre des locataires, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Reconfigurez l’identité managée et créez un coffre de clés. Vous pouvez également déplacer la ressource de coffre de clés, auquel cas seule l’identité managée doit être reconfigurée. Dans chacun des cas ci-dessus, consultez [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).   |
| Si la ressource de compte de stockage utilisée pour les journaux d’audit est déplacée entre des groupes de ressources ou des abonnements, aucune erreur ne s’affiche.  | Vous pouvez [créer un nouveau compte de stockage et le configurer pour stocker les journaux d’audit](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs). |

## <a name="other-errors"></a>Autres erreurs

| Message d’erreur| Résolution recommandée |
|------------------------------------------------------|--------------------------------------|
| Si les restrictions du réseau sont configurées pour le coffre de clés, cette erreur s’affiche. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Le service ne peut pas faire la différence entre la suppression du coffre de clés et le scénario du coffre de clés non accessible en raison des restrictions du réseau. Dans chaque cas, vous serez redirigé vers le panneau [Récupérer un coffre de clés](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault).     |


## <a name="next-steps"></a>Étapes suivantes

- [Installer Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-install.md).
