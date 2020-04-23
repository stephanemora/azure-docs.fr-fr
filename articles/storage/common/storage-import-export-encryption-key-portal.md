---
title: Utiliser le Portail Microsoft Azure pour configurer des clés gérées par le client pour le service Import/Export
description: Découvrez comment utiliser le portail Azure afin de configurer des clés gérées par le client avec Azure Key Vault pour le service Azure Import/Export. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456496"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Utiliser des clés gérées par le client dans Azure Key Vault pour le service Import/Export

Azure Import/Export protège les clés BitLocker utilisées pour verrouiller les lecteurs à l’aide d’une clé de chiffrement. Par défaut, les clés BitLocker sont chiffrées avec des clés gérées par Microsoft. Pour un contrôle supplémentaire sur les clés de chiffrement, vous pouvez également fournir des clés gérées par le client.

Les clés gérées par le client doivent être créées et stockées dans Azure Key Vault. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../../key-vault/general/overview.md)

Cet article explique comment utiliser des clés gérées par le client avec le service Import/Export dans le [portail Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les points suivants :

1. Vous avez créé une tâche d’importation ou d’exportation conformément aux instructions de :

    - [Création d’une tâche d’importation pour les objets BLOB](storage-import-export-data-to-blobs.md).
    - [Création d’une tâche d’importation pour les fichiers](storage-import-export-data-to-files.md).
    - [Création d’une tâche d’exportation pour les objets BLOB](storage-import-export-data-from-blobs.md)

2. Vous disposez d’une instance Azure Key Vault existante avec une clé que vous pouvez utiliser pour protéger votre clé BitLocker. Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../../key-vault/secrets/quick-create-portal.md).

    - Les options **Suppression réversible** et **Ne pas vider** sont définies sur votre instance Key Vault existante. Ces propriétés ne sont pas activées par défaut. Pour activer ces propriétés, consultez les sections intitulées **Activation de la suppression réversible** et **Activation de la protection contre le vidage** dans l’un des articles suivants :

        - [Guide pratique pour utiliser la suppression réversible avec Azure Power​Shell](../../key-vault/general/soft-delete-powershell.md).
        - [Guide pratique pour utiliser la suppression réversible avec Azure CLI](../../key-vault/general/soft-delete-cli.md).
    - Le coffre de clés existant doit avoir une clé RSA d’une taille de 2048 ou plus. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - Le coffre de clés doit se trouver dans la même région que le compte de stockage pour vos données.  
    - Si vous n’avez pas d’instances Azure Key Vault existantes, vous pouvez également en créer de façon intégrée comme décrit dans la section suivante.

## <a name="enable-keys"></a>Activer les clés

La configuration de la clé gérée par le client pour votre service Import/Export est facultative. Par défaut, le service Import/Export utilise une clé gérée par Microsoft pour protéger votre clé BitLocker. Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez au panneau **Vue d’ensemble** de votre tâche d’importation.
2. Dans le volet droit, sélectionnez **Choisir la méthode de chiffrement de vos clés BitLocker**.

    ![Choisir l’option de chiffrement](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Dans le panneau **Chiffrement**, vous pouvez afficher et copier la clé BitLocker de l’appareil. Sous **Type de chiffrement**, vous pouvez choisir la façon dont vous souhaitez protéger votre clé BitLocker. Par défaut, une clé gérée par Microsoft est utilisée.

    ![Afficher la clé BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Vous avez la possibilité de spécifier une clé gérée par le client. Après avoir sélectionné la clé gérée par le client, **Sélectionnez un coffre de clés et une clé**.

    ![Sélectionner la clé gérée par le client](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Dans le panneau **Sélectionner une clé dans Azure Key Vault**, l’abonnement est automatiquement renseigné. Pour **Coffre de clés**, vous pouvez sélectionner un coffre de clés existant dans la liste déroulante.

    ![Sélectionner ou créer un Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Vous pouvez également sélectionner **Créer** pour créer un coffre de clés. Dans le panneau **Créer un coffre de clés**, saisissez le groupe de ressources et le nom du coffre de clés. Acceptez toutes les autres valeurs par défaut. Sélectionnez **Vérifier + créer**.

    ![Créer un coffre Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Passez en revue les informations associées à votre coffre de clés, puis sélectionnez **Créer**. Patientez quelques minutes avant la fin de la création du coffre de clés.

    ![Créer un Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Dans **Sélectionner une clé dans Azure Key Vault**, vous pouvez sélectionner une clé dans le coffre de clés existant.

9. Si vous avez créé un coffre de clés, sélectionnez **Créer** pour créer une clé. La taille de la clé RSA peut être supérieure ou égale à 2048.

    ![Créer une nouvelle clé dans Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Si la suppression réversible et la protection contre le vidage ne sont pas activées lorsque vous créez le coffre de clés, le coffre de clés est mis à jour pour que la suppression réversible et la protection contre le vidage soient activées.

10. Indiquez le nom de votre clé, acceptez les autres valeurs par défaut, puis sélectionnez **Créer**.

    ![Créer une nouvelle clé](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Sélectionnez la **Version**, puis choisissez **Sélectionner**. Vous êtes averti qu’une clé est créée dans votre coffre de clés.

    ![Nouvelle clé créée dans le coffre de clés](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Dans le panneau **Chiffrement**, vous pouvez voir le coffre de clés et la clé sélectionnée pour votre clé gérée par le client.

## <a name="disable-keys"></a>Désactiver les clés

Vous pouvez uniquement désactiver les clés gérées par Microsoft et passer à des clés gérées par le client à n’importe quel moment de la tâche d’importation/exportation. Toutefois, vous ne pouvez pas désactiver la clé gérée par le client une fois que vous l’avez créée.

## <a name="troubleshoot-customer-managed-key-errors"></a>Résoudre les erreurs pour les clés gérées par le client

Si vous recevez des erreurs liées à votre clé gérée par le client, utilisez le tableau suivant pour résoudre les problèmes :

| Code d'erreur     |Détails     | Récupérable ?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Une clé gérée par le client a été appliquée, mais l’accès à la clé est actuellement révoqué. Pour plus d’informations, consultez [Activer l’accès à la clé](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Oui, vérifier si : <ol><li>Le coffre de clés contient toujours le fichier MSI dans la stratégie d’accès.</li><li>La stratégie d’accès fournit des autorisations pour recevoir, envelopper, désenvelopper.</li><li>Si le coffre de clés se trouve dans un réseau virtuel derrière le pare-feu, vérifiez si **Autoriser les services de confiance Microsoft** est activé.</li></ol>                                                                                            |
| CmkErrorDisabled      | A appliqué une clé gérée par le client, mais la clé est désactivée. Pour plus d’informations, consultez [Activer la clé](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Oui, en activant la version de clé     |
| CmkErrorNotFound      | A appliqué une clé gérée par le client, mais impossible de trouver la clé. <br>Si la clé est supprimée et purgée après la période de rétention, vous ne pouvez pas récupérer la clé. Si vous avez sauvegardé la clé, vous pouvez restaurer la clé pour résoudre ce problème. | Non, la clé a été supprimée et est également purgée après la période de rétention. <br>Oui, uniquement si le client dispose de la clé sauvegardée et la restaure.  |
| CmkErrorVaultNotFound | Application d’une clé gérée par le client, mais impossible de trouver le coffre de clés associé à la clé.<br>Si vous avez supprimé le coffre de clés, vous ne pouvez pas récupérer la clé gérée par le client.  Si vous avez migré le coffre de clés vers un autre locataire, consultez [Modifier l’ID de client du coffre de clés après un déplacement d’abonnement](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). |   Non, si le client a supprimé le coffre de clés.<br> Oui, si le coffre de clés a subi une migration de locataire ; effectuez l’une des opérations suivantes : <ol><li>Replacez le coffre de clés sur l’ancien locataire.</li><li>Définissez Identity = None, puis à nouveau sur Identity = SystemAssigned, ce qui supprime et recrée l’identité</li></ol>|

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
