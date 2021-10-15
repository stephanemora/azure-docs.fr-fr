---
title: Intégration d’Azure Key Vault avec la ressource Azure Stack Edge et l’activation de l’appareil
description: Décrit comment Azure Key Vault est associé à la gestion des secrets lors de l’activation d’un appareil Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 67cddd1839e666d4908706a1bbdaccbbd3565704
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351429"
---
# <a name="manage-azure-stack-edge-secrets-using-azure-key-vault"></a>Gérer les secrets d’Azure Stack Edge à l’aide d’Azure Key Vault 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault est intégré à la ressource Azure Stack Edge pour la gestion des secrets. Cet article fournit des détails sur la création d’un Azure Key Vault pour la ressource Azure Stack Edge pendant l’activation de l’appareil et qui est ensuite utilisé pour la gestion des secrets. 


## <a name="about-key-vault-and-azure-stack-edge"></a>À propos du coffre de clés et d’Azure Stack Edge

Le service cloud Azure Key Vault peut être utilisé pour stocker en toute sécurité les jetons, mots de passe, certificats, clés d’API et autres secrets, et en contrôler étroitement l’accès. Key Vault simplifie la création et le contrôle des clés de chiffrement utilisées pour chiffrer vos données. 

Pour le service Azure Stack Edge, l’intégration au coffre de clés offre les avantages suivants :

- Stocke les secrets des clients. L’un des secrets utilisés pour le service Azure Stack Edge est la clé d’intégrité de canal (CIK). Cette clé vous permet de chiffrer vos secrets et est stockée en toute sécurité dans le coffre de clés. Les secrets de l’appareil, tels que la clé de récupération BitLocker et le mot de passe de l’utilisateur du contrôleur de gestion de la carte de base (BMC), sont également stockés dans le coffre de clés. 

    Pour plus d’informations, consultez la rubrique [Stocker en toute sécurité les secrets et clés](../key-vault/general/overview.md#securely-store-secrets-and-keys).
- Transmet les secrets chiffrés du client à l’appareil.
- Affiche les secrets de l’appareil pour faciliter l’accès en cas de défaillance de l’appareil.


## <a name="generate-activation-key-and-create-key-vault"></a>Générer une clé d’activation et créer un coffre de clés

Un coffre de clés est créé pour la ressource Azure Stack Edge pendant le processus de génération de clé d’activation. Le coffre de clés est créé dans le même groupe de ressources que celui où se trouve la ressource Azure Stack Edge. Une autorisation de Contributeur est requise pour le coffre de clés. 

### <a name="prerequisites-for-key-vault"></a>Conditions préalables pour le coffre de clés

Avant la création du coffre de clés lors de l’activation, les conditions préalables suivantes doivent être remplies :

- Inscrivez le fournisseur de ressources *Microsoft.KeyVault* avant de créer la ressource Azure Stack Edge. Le fournisseur de ressources est automatiquement inscrit si vous disposez d’un accès propriétaire ou contributeur à l’abonnement. Le coffre de clés est créé dans le même abonnement et le même groupe de ressources que la ressource Azure Stack Edge. 

- Lorsque vous créez une ressource Azure Stack Edge, une identité managée affectée par le système est également créée ; elle persiste pendant toute la durée de vie de la ressource et communique avec le fournisseur de ressources sur le cloud. 

    Lorsque l’identité managée est activée, Azure crée une identité approuvée pour la ressource Azure Stack Edge.

### <a name="key-vault-creation"></a>Création d’un coffre de clés

Une fois que vous avez créé la ressource, vous devez l’activer avec l’appareil. Pour ce faire, vous allez générer une clé d’activation à partir du portail Azure. 

Lorsque vous générez une clé d’activation, les événements suivants se produisent : 

![Flux de génération de la clé d’activation](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-1.png)

- Vous demandez une clé d’activation dans le portail Azure. La demande est ensuite envoyée au fournisseur de ressources du coffre de clés. 
- Un coffre de clés de niveau standard avec une stratégie d’accès est créé et est verrouillé par défaut. 
    - Ce coffre de clés utilise le nom par défaut ou un nom personnalisé de 3 à 24 caractères que vous avez spécifié. Vous ne pouvez pas utiliser un coffre de clés qui est déjà en cours d’utilisation. 
    - Les détails du coffre de clés sont stockés dans le service. Ce coffre de clés est utilisé pour la gestion des secrets et persiste tant que la ressource Azure Stack Edge existe. 

        ![Key Vault créé pendant la génération de la clé d’activation](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)
- Un verrou de ressource est activé sur le coffre de clés pour éviter toute suppression accidentelle. Une suppression réversible est également activée sur le coffre de clés, ce qui permet la restauration du coffre de clés dans un délai de 90 jours en cas de suppression accidentelle. Pour plus d’informations, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](../key-vault/general/soft-delete-overview.md).
- Une identité managée affectée par le système, qui a été créée lorsque vous avez créé la ressource Azure Stack Edge, est désormais activée.
- Une clé d’intégrité de canal (CIK) est générée et placée dans le coffre de clés. Les détails de la CIK sont affichés dans le service.
- Un compte de stockage redondant interzone (ZRS) est également créé dans la même étendue que la ressource Azure Stack Edge, et un verrou est placé sur le compte. 
    - Ce compte est utilisé pour stocker les journaux d’audit. 
    - La création du compte de stockage est un processus long et prend quelques minutes.
    - Le compte de stockage est étiqueté avec le nom du coffre de clés.
- Un paramètre de diagnostic est ajouté au coffre de clés et la journalisation est activée. 
- L’identité managée est ajoutée à la stratégie d’accès du coffre de clés pour permettre l’accès au coffre de clés, car l’appareil utilise le coffre de clés pour stocker et récupérer des secrets. 
- Le coffre de clés authentifie la demande à l’aide de l’identité managée pour générer la clé d’activation. La clé d’activation est renvoyée au portail Azure. Vous pouvez ensuite copier cette clé et l’utiliser dans l’interface utilisateur locale pour activer votre appareil.

> [!NOTE]
> - Si vous disposiez d’une ressource Azure Stack Edge existante avant l’intégration d’Azure Key Vault à la ressource Azure Stack Edge, vous n’êtes pas affecté. Vous pouvez continuer à utiliser votre ressource Azure Stack Edge existante.
> -  La création du coffre de clés et du compte de stockage augmente le coût global des ressources. Pour plus d’informations sur les transactions autorisées et les frais correspondants, consultez les pages de [tarification pour Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) et de [tarification pour un compte de stockage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si vous rencontrez des problèmes liés au coffre de clés et à l’activation d’appareil, consultez [Résoudre les problèmes d’activation d’appareil](azure-stack-edge-gpu-troubleshoot-activation.md).


## <a name="view-key-vault-properties"></a>Afficher les propriétés du coffre de clés

Une fois la clé d’activation générée et le coffre des clés créé, vous souhaiterez peut-être accéder au coffre des clés pour consulter les secrets, les stratégies d’accès, les diagnostics et les insights. La procédure suivante décrit chacune de ces opérations.

### <a name="view-secrets"></a>Afficher les secrets

Une fois la clé d’activation générée et le coffre de clés créé, vous souhaiterez peut-être accéder au coffre de clés. 

Pour accéder au coffre de clés et afficher les secrets, procédez comme suit :

1. Dans le portail Azure, sur la page de votre ressource Azure Stack Edge, accédez à **Sécurité**. 
1. Dans le volet de droite, sous **Sécurité**, vous pouvez voir les **secrets**. 
1. Vous pouvez également accéder au coffre de clés associé à votre ressource Azure Stack Edge. Sélectionnez **Nom du coffre de clés**. 

    ![Accéder au coffre de clés de l’appareil](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Pour afficher les secrets stockés dans votre coffre de clés, accédez à **Secrets**. La clé d’intégrité de canal, la clé de récupération BitLocker et les mots de passe d’utilisateur du contrôleur de gestion de la carte de base (BMC) sont stockés dans le coffre de clés. Si l’appareil tombe en panne, le portail permet d’accéder facilement à la clé de récupération BitLocker et au mot de passe d’utilisateur du BMC.
    
    ![Afficher les secrets des appareils dans le coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-secrets-1.png)

### <a name="view-managed-identity-access-policies"></a>Afficher les stratégies d’accès de l’identité managée

Pour accéder aux stratégies d’accès de votre coffre de clés et de votre identité managée, procédez comme suit :

1. Dans le portail Azure, sur la page de votre ressource Azure Stack Edge, accédez à **Sécurité**. 
1. Sélectionnez le lien correspondant à **Nom du coffre de clés** pour accéder au coffre de clés associé à votre ressource Azure Stack Edge. 

    ![Accéder au coffre de clés de l’appareil](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Pour afficher les stratégies d’accès associées à votre coffre de clés, accédez à **Stratégies d’accès**. Vous pouvez voir que l’identité managée a reçu un accès. Sélectionnez **Autorisations du secret**. Vous pouvez voir que l’accès de l’identité managée est limité uniquement aux opérations **Get** et **Set** du secret. 
    
    ![Afficher les stratégies d’accès du coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-access-policies-1.png)

    
### <a name="view-audit-logs"></a>Afficher les journaux d’audit

Pour accéder au coffre de clés et afficher les paramètres de diagnostic et les journaux d’audit, procédez comme suit :

1. Dans le portail Azure, sur la page de votre ressource Azure Stack Edge, accédez à **Sécurité**. 
1. Sélectionnez le lien correspondant à **Nom du coffre de clés** pour accéder au coffre de clés associé à votre ressource Azure Stack Edge. 

    ![Accéder au coffre de clés de l’appareil](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Pour afficher les paramètres de diagnostic associés à votre coffre de clés, accédez à **Paramètres de diagnostic**. Ce paramètre vous permet de surveiller comment et quand vos coffres de clés sont accessibles, et par qui. Vous pouvez voir qu’un paramètre de diagnostic a été créé. Les journaux circulent dans le compte de stockage qui a également été créé. Des événements d’audit sont également créés dans le coffre de clés.
    
    ![Afficher les paramètres de diagnostic du coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-diagnostics-settings-1.png)

Si vous avez configuré une autre cible de stockage pour les journaux sur le coffre de clés, vous pouvez consulter les journaux directement dans ce compte de stockage.

### <a name="view-insights"></a>Afficher les informations 

Pour accéder aux insights relatifs au coffre de clés, y compris les opérations effectuées sur le coffre de clés, procédez comme suit :

1. Dans le portail Azure, sur la page de votre ressource Azure Stack Edge, accédez à **Sécurité**. 
1. Sélectionnez le lien correspondant à **Diagnostics du coffre de clés**. 

    ![Accéder au coffre de clés de l’appareil](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. Le panneau **Insights** donne une vue d’ensemble des opérations effectuées sur le coffre de clés.

    ![Afficher les insights de votre coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-insights-1.png) 

### <a name="view-managed-identity-status"></a>Afficher l’état de l’identité managée

Pour afficher l’état de l’identité managée affectée par le système associée à votre ressource Azure Stack Edge, procédez comme suit :

1. Dans le portail Azure, sur la page de votre ressource Azure Stack Edge, accédez à **Sécurité**. 
1. Dans le volet de droite, accédez à **Identité managée affectée par le système** pour voir si l’identité managée affectée par le système est activée ou désactivée.

    ![Accéder au coffre de clés de l’appareil](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

### <a name="view-key-vault-locks"></a>Afficher les verrous du coffre de clés

Pour accéder au coffre de clés et afficher les verrous, procédez comme suit :

1. Dans le portail Azure, sur la page de votre ressource Azure Stack Edge, accédez à **Sécurité**. 
1. Sélectionnez le lien correspondant à **Nom du coffre de clés** pour accéder au coffre de clés associé à votre ressource Azure Stack Edge. 

    ![Accéder au coffre de clés de l’appareil](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)
1. Pour afficher les verrous sur votre coffre de clés, accédez à **Verrous**. Pour éviter toute suppression accidentelle, un verrou de ressource est activé sur le coffre de clés. 
    
    ![Afficher les verrous sur votre coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-locks-1.png)


## <a name="regenerate-activation-key"></a>Régénérer la clé d’activation

Dans certains cas, vous devrez peut-être régénérer la clé d’activation. Lorsque vous régénérez une clé d’activation, les événements suivants se produisent :

1. Vous demandez la régénération d’une clé d’activation dans le portail Azure. 
1. La clé d’activation est renvoyée au portail Azure. Vous pouvez ensuite copier cette clé et l’utiliser. 

Le coffre de clés n’est pas accessible lorsque vous régénérez la clé d’activation. 

## <a name="recover-device-secrets"></a>Récupérer les secrets de l’appareil

Si la CIK est supprimée par inadvertance ou si des secrets (par exemple, le mot de passe d’utilisateur du BMC) sont devenus obsolètes dans le coffre de clés, vous devez transmettre par push les secrets à partir de l’appareil pour mettre à jour les secrets du coffre de clés. 

Pour synchroniser les secrets de l’appareil, procédez comme suit :  

1. Sur le portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Sécurité**.
1. Dans le volet de droite, dans la barre de commande supérieure, sélectionnez **Synchroniser les secrets de l’appareil**.
1. Les secrets de l’appareil sont transmis par push au coffre de clés pour restaurer ou mettre à jour les secrets dans le coffre de clés. Une notification s’affiche lorsque la synchronisation est terminée.

    ![Synchroniser les secrets de l’appareil sur votre coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/sync-device-secrets-1.png)

## <a name="delete-key-vault"></a>Supprimer un coffre de clés

Il existe deux façons de supprimer le coffre de clés associé à la ressource Azure Stack Edge :

- Supprimer la ressource Azure Stack Edge et choisir de supprimer le coffre de clés associé en même temps.
- Suppression accidentelle du coffre de clés directement.

Lorsque votre ressource Azure Stack Edge est supprimée, le coffre de clés est également supprimé avec la ressource. Vous êtes invité à confirmer l’opération. Si vous stockez d’autres clés dans ce coffre de clés et que vous n’avez pas l’intention de le supprimer, vous pouvez choisir de ne pas donner votre consentement. Seule la ressource Azure Stack Edge est supprimée, laissant le coffre de clés intact. 

Procédez comme suit pour supprimer la ressource de Azure Stack Edge et le coffre de clés associé :

1. Dans le portail Azure, accédez à votre ressource Azure Stack Edge, puis à **Vue d’ensemble**.
1. Dans le volet de droite, sélectionnez **Supprimer**. Cette action supprimera la ressource Azure Stack Edge.

   ![Supprimer la ressource Azure Stack Edge et le coffre de clés associé](media/azure-stack-edge-gpu-activation-key-vault/delete-azure-stack-edge-resource-1.png)  

1. Un panneau de confirmation s’affiche. Saisissez le nom de votre ressource Azure Stack Edge. Pour confirmer la suppression du coffre de clés associé, saisissez **Oui**.

    ![Confirmer la suppression de la ressource Azure Stack Edge et du coffre de clés associé](media/azure-stack-edge-gpu-activation-key-vault/confirm-delete-azure-stack-edge-resource-1.png)   
1. Sélectionnez **Supprimer**.

La ressource Azure Stack Edge et le coffre de clés sont supprimés.

Le coffre de clés peut être supprimé accidentellement lorsque la ressource Azure Stack Edge est en cours d’utilisation. Dans ce cas, une alerte critique est déclenchée dans la page **Sécurité** de votre ressource Azure Stack Edge. Vous pouvez accéder à cette page pour récupérer votre coffre de clés. 


## <a name="recover-key-vault"></a>Récupérer le coffre de clés

Vous pouvez récupérer le coffre de clés associé à votre ressource Azure Stack Edge s’il a été supprimé accidentellement ou vidé. Si ce coffre de clés a été utilisé pour stocker d’autres clés, vous devrez récupérer ces clés en restaurant le coffre de clés.

- Dans les 90 jours suivant la suppression, vous pouvez restaurer le coffre de clés qui a été supprimé.
- Si la période de protection contre le vidage de 90 jours est déjà écoulée, vous ne pouvez pas restaurer le coffre de clés. Vous devez alors créer un nouveau coffre de clés.

Dans les 90 jours suivant la suppression, procédez comme suit pour récupérer votre coffre de clés :

- Dans le portail Azure, accédez à la page **Sécurité** de votre ressource Azure Stack Edge. Vous verrez une notification indiquant que le coffre de clés associé à votre ressource a été supprimé. Vous pouvez sélectionner la notification ou sélectionner **Reconfigurer** à côté du nom du coffre de clés sous **Préférences de sécurité** pour récupérer votre coffre de clés.

    ![Accéder à la page Sécurité](media/azure-stack-edge-gpu-activation-key-vault/security-page-1.png) 

- Dans le panneau **Récupérer un coffre de clés**, sélectionnez **Configurer**. Les opérations suivantes sont effectuées dans le cadre de la récupération :  

    ![Étapes de récupération](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-2.png) 

    - Un coffre de clés est récupéré avec le même nom, et un verrou est placé sur la ressource du coffre de clés. 
    
        > [!NOTE]
        > Si votre coffre de clés est supprimé et que la période de protection contre le vidage de 90 jours n’est pas écoulée, alors le nom du coffre de clés ne peut pas être utilisé pour créer un nouveau coffre de clés pendant cette période.
    - Un compte de stockage est créé pour stocker les journaux d’audit. 
    - L’accès au coffre de clés est accordé à l’identité managée affectée par le système.
    - Les secrets de l’appareil sont transmis par push au coffre de clés. 
    
    Sélectionnez **Configurer**. 
 
    ![Panneau Récupérer le coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-1.png)  

    Le coffre de clés est récupéré et, une fois la récupération terminée, une notification s’affiche à cet effet.

Si le coffre de clés est supprimé et que la période de protection contre le vidage de 90 jours s’est écoulée, vous avez la possibilité de créer un nouveau coffre de clés en suivant la [procédure de récupération de clé](#recover-key-vault) décrite ci-dessus. Dans ce cas, vous devrez donner un nouveau nom à votre coffre de clés. Un nouveau compte de stockage est créé, l’identité managée se voit accorder l’accès à ce coffre de clés et les secrets de l’appareil sont transmis par push à ce coffre de clés.
  
<!--To recover the key vault using the follow these steps to [Recover your key vault](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates).-->

## <a name="recover-managed-identity-access"></a>Récupérer l’accès à l’identité managée

Si la stratégie d’accès de l’identité managée affectée par le système est supprimée, une alerte est déclenchée lorsque l’appareil est incapable de resynchroniser les secrets du coffre de clés. Si l’identité managée n’a pas accès au coffre de clés, une nouvelle alerte d’appareil est générée. Sélectionnez l’alerte dans chaque cas pour ouvrir le panneau **Récupérer un coffre de clés** et reconfigurer. Ce processus doit restaurer l’accès de l’identité managée. 

![Accorder à l’identité managée l’accès au flux de coffre de clés](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-2.png)


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la façon de [Générer une clé d’activation](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).
- [Résoudre les erreurs de coffre de clés](azure-stack-edge-gpu-troubleshoot-activation.md) sur votre appareil Azure Stack Edge.
