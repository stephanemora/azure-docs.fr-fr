---
title: Utiliser le Portail Microsoft Azure pour configurer des clés gérées par Azure Data Box
description: Découvrez comment utiliser le portail Azure afin de configurer des clés gérées par le client avec Azure Key Vault pour Azure Data Box. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 6f543b3f5c2bb7d4949c431580771c4b0d965e4d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125340"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Utiliser des clés gérées par le client dans Azure Key Vault pour Azure Data Box

Azure Data Box protège la clé de déverrouillage de l’appareil (également appelée « device password », ou « mot de passe de l’appareil »). Cette dernière est utilisée pour verrouiller l’appareil via une clé de chiffrement. Par défaut, la clé de déverrouillage de l’appareil pour un ordre Data Box est chiffrée avec une clé gérée par Microsoft. Pour un contrôle supplémentaire sur la clé de déverrouillage de l’appareil, vous pouvez également fournir une clé gérée par le client. 

Les clés gérées par le client doivent être créées et stockées dans Azure Key Vault. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../key-vault/general/overview.md).

Cet article explique comment utiliser des clés gérées par le client avec Azure Data Box dans le [portail Azure](https://portal.azure.com/). Cet article s’applique aux appareils Azure Data Box et Azure Data Box Heavy.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les points suivants :

1. Vous avez créé un ordre Azure Data Box conformément aux instructions fournies dans le didacticiel [ : Commander Azure Data Box](data-box-deploy-ordered.md).

2. Vous disposez d’une instance Azure Key Vault existante avec une clé que vous pouvez utiliser pour protéger votre clé de déverrouillage d’appareil. Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../key-vault/secrets/quick-create-portal.md).

    - Les options **Suppression réversible** et **Ne pas vider** sont définies sur votre instance Key Vault existante. Ces propriétés ne sont pas activées par défaut. Pour activer ces propriétés, consultez les sections intitulées **Activation de la suppression réversible** et **Activation de la protection contre le vidage** dans l’un des articles suivants :

        - [Guide pratique pour utiliser la suppression réversible avec Azure Power​Shell](../key-vault/general/soft-delete-powershell.md).
        - [Guide pratique pour utiliser la suppression réversible avec Azure CLI](../key-vault/general/soft-delete-cli.md).
    - Le coffre de clés existant doit avoir une clé RSA d’une taille de 2048 ou plus. Pour plus d'informations sur les clés, consultez [À propos des clés Azure Key Vault](../key-vault/keys/about-keys.md).
    - Le coffre de clés doit se trouver dans la même région que les comptes de stockage utilisés pour vos données. Vous pouvez lier plusieurs comptes de stockage à votre ressource Azure Data Box.
    - Si vous n’avez pas d’instances de coffre de clés existantes, vous pouvez également en créer de façon intégrée comme décrit dans la section suivante.

## <a name="enable-keys"></a>Activer les clés

La configuration de la clé gérée par le client pour Azure Data Box est facultative. Par défaut, Azure Data Box utilise une clé gérée par Microsoft pour protéger votre clé BitLocker. Pour activer un clé gérée par le client dans le portail Azure, procédez comme suit :

1. Accédez au panneau **Vue d’ensemble** de votre ordre Data Box.

    ![Panneau Vue d’ensemble de l’ordre Data Box](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Accédez à **Settings (Paramètres) > Encryption (Chiffrement)** . Sous **Encryption type** (Type de chiffrement), vous pouvez choisir la façon dont vous souhaitez protéger votre clé de déverrouillage d’appareil. Par défaut, une clé gérée par Microsoft est utilisée pour protéger le mot de passe de déverrouillage de votre appareil. 

    ![Choisir l’option de chiffrement](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Sélectionnez le type de chiffrement **Customer managed key** (Clé gérée par le client). Après avoir sélectionné la clé gérée par le client, **Sélectionnez un coffre de clés et une clé**.

    ![Sélectionner une clé gérée par le client](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. Dans le panneau **Sélectionner une clé dans Azure Key Vault**, l’abonnement est automatiquement renseigné. Pour **Coffre de clés**, vous pouvez sélectionner un coffre de clés existant dans la liste déroulante.

    ![Créer un coffre Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Vous pouvez également sélectionner **Créer** pour créer un coffre de clés. Dans le panneau **Créer un coffre de clés**, saisissez le groupe de ressources et le nom du coffre de clés. Assurez-vous que les options **Soft delete** (Suppression réversible) et **Purge protection** (Protection contre le vidage) sont activées. Acceptez toutes les autres valeurs par défaut. Sélectionnez **Vérifier + créer**.

    ![Créer un coffre Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Passez en revue les informations associées à votre coffre de clés, puis sélectionnez **Créer**. Patientez quelques minutes avant la fin de la création du coffre de clés.

    ![Créer un Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. Dans **Sélectionner une clé dans Azure Key Vault**, vous pouvez sélectionner une clé dans le coffre de clés existant.

    ![Créer une nouvelle clé dans Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Si vous voulez créer un coffre de clés, sélectionnez **Create new** (Créer) pour créer une clé. La taille de la clé RSA peut être supérieure ou égale à 2048.

    ![Créer une nouvelle clé dans Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Indiquez le nom de votre clé, acceptez les autres valeurs par défaut, puis sélectionnez **Créer**. 

    ![Créer une nouvelle clé](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Vous êtes averti qu’une clé est créée dans votre coffre de clés. Sélectionnez la **Version**, puis choisissez **Sélectionner**.

    ![Nouvelle clé créée dans le coffre de clés](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. Dans le volet **Encryption type** (Type de chiffrement), vous pouvez voir le coffre de clés et la clé sélectionnée pour votre clé gérée par le client.

    ![Clé et coffre de clés pour la clé gérée par le client](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Enregistrez la clé. 

    ![Enregistrer une clé gérée par le client](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    L’URL de la clé est affichée sous **Type de chiffrement**.

    ![URL de la clé gérée par le client](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Vous pouvez désactiver la clé gérée par Microsoft et passer à une clé gérée par le client à n’importe quelle étape de l’ordre Data Box. Toutefois, lorsque vous avez créé la clé gérée par le client, vous ne pouvez pas réutiliser la clé gérée par Microsoft.

## <a name="troubleshoot-errors"></a>Résoudre les erreurs

Si vous recevez des erreurs liées à votre clé gérée par le client, utilisez le tableau suivant pour résoudre les problèmes.

| Code d’erreur| Message d’erreur| Détails|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Impossible de récupérer la clé d’accès, car la clé gérée par le client est désactivée.| Oui, en activant la version de clé.|
| SsemUserErrorEncryptionKeyExpired| Impossible de récupérer la clé d’accès, car la clé gérée par le client a expiré.| Oui, en activant la version de clé.|
| SsemUserErrorKeyDetailsNotFound| Impossible de récupérer la clé d’accès, car la clé gérée par le client est introuvable.| Si vous avez supprimé le coffre de clés, vous ne pouvez pas récupérer la clé gérée par le client.  Si vous avez migré le coffre de clés vers un autre locataire, consultez [Modifier l’ID de client du coffre de clés après un déplacement d’abonnement](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). Si vous avez supprimé le coffre de clés :<ol><li>Oui, s’il s’agit de la durée de la protection contre le vidage, à l’aide des étapes de [Récupérer un coffre de clés](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Non au-delà de la durée de la protection contre le vidage.</li></ol><br>Sinon, si le coffre de clés a subi une migration de locataire, oui, il peut être récupéré à l’aide d’une des étapes ci-dessous : <ol><li>Restaurez le coffre de clés sur l’ancien locataire.</li><li>Définissez `Identity = None`, puis redéfinissez la valeur sur `Identity = SystemAssigned`. Cela supprime et recrée l’identité une fois que la nouvelle identité a été créée. Activez les autorisations `Get`, `Wrap` et `Unwrap` sur la nouvelle identité dans la stratégie d’accès du coffre de clés.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Impossible de récupérer la clé d’accès car la clé gérée par le client est révoquée.| Oui, vérifier si : <ol><li>Le coffre de clés contient toujours le fichier MSI dans la stratégie d’accès.</li><li>La stratégie d’accès fournit des autorisations pour recevoir, envelopper, désenvelopper.</li><li>Si le coffre de clés se trouve dans un réseau virtuel derrière le pare-feu, vérifiez si **Autoriser les services de confiance Microsoft** est activé.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Impossible de récupérer la clé d’accès, car le coffre de clés associé à la clé gérée par le client est introuvable. | Si vous avez supprimé le coffre de clés, vous ne pouvez pas récupérer la clé gérée par le client.  Si vous avez migré le coffre de clés vers un autre locataire, consultez [Modifier l’ID de client du coffre de clés après un déplacement d’abonnement](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). Si vous avez supprimé le coffre de clés :<ol><li>Oui, s’il s’agit de la durée de la protection contre le vidage, à l’aide des étapes de [Récupérer un coffre de clés](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Non au-delà de la durée de la protection contre le vidage.</li></ol><br>Sinon, si le coffre de clés a subi une migration de locataire, oui, il peut être récupéré à l’aide d’une des étapes ci-dessous : <ol><li>Restaurez le coffre de clés sur l’ancien locataire.</li><li>Définissez `Identity = None`, puis redéfinissez la valeur sur `Identity = SystemAssigned`. Cela supprime et recrée l’identité une fois que la nouvelle identité a été créée. Activez les autorisations `Get`, `Wrap` et `Unwrap` sur la nouvelle identité dans la stratégie d’accès du coffre de clés.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Impossible de récupérer la clé d’accès, car la clé gérée par le client est introuvable.| Oui, vérifier si : <ol><li>Le coffre de clés contient toujours le fichier MSI dans la stratégie d’accès.</li><li>L’identité est de type System assigned (Attribuée par le système).</li><li>Activez les autorisations Get, Wrap et Unwrap sur l’identité dans la stratégie d’accès du coffre de clés.</li></ol>|
| Erreur générique  | Impossible de récupérer la clé d’accès.| Il s'agit d'une erreur générique. Contactez le Support Microsoft pour résoudre l’erreur et déterminer les étapes suivantes.|


## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)