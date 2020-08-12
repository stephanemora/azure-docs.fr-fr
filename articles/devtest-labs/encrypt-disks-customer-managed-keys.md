---
title: Chiffrer des disques de système d’exploitation avec des clés gérées par le client dans Azure DevTest Labs
description: Découvrez la procédure de chiffrement de disques de système d’exploitation avec des clés gérées par le client dans Azure DevTest Labs.
ms.topic: article
ms.date: 07/28/2020
ms.openlocfilehash: b9eb401521f6bd81efe3238dc05d07e4554c4f62
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542415"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Chiffrer des disques de système d’exploitation avec des clés gérées par le client dans Azure DevTest Labs
Le chiffrement côté serveur protège vos données et vous aide à honorer les engagements de votre entreprise en matière de sécurité et de conformité. SSE chiffre automatiquement vos données stockées sur des disques managés dans Azure (système d’exploitation et disques de données) au repos par défaut lors de leur conservation dans le cloud. En savoir plus sur le [chiffrement de disque](../virtual-machines/windows/disk-encryption.md) dans Azure. 

Dans DevTest Labs, tous les disques de système d’exploitation et les disques de données créés dans le cadre d’un labo sont chiffrés à l’aide de clés gérées par la plateforme. Toutefois, en tant que propriétaire de labo, vous pouvez choisir de chiffrer des disques de système d’exploitation de machine virtuelle à l’aide de vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, vous pouvez spécifier une **clé gérée par le client** à utiliser pour le chiffrement de données dans les disques de système d’exploitation du labo. Pour en savoir plus sur le chiffrement côté serveur (SSE) avec des clés gérées par le client et d’autres types de chiffrements de disque managés, consultez [Clés gérées par le client](../virtual-machines/windows/disk-encryption.md#customer-managed-keys). Consultez également [Restrictions liées à l’utilisation de clés gérées par le client](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#restrictions).


> [!NOTE]
> - Actuellement, le chiffrement de disque avec une clé gérée par le client n’est pris en charge que pour les disques de système d’exploitation dans DevTest Labs. 
> 
> - Le paramètre s’applique aux disques de système d’exploitation créés récemment dans le labo. Si vous choisissez de modifier le chiffrement de disque défini à un moment donné, les anciens disques du labo continueront à être chiffrés à l’aide de l’ensemble de chiffrement de disque précédent. 

La section suivante explique comment le propriétaire d’un labo peut configurer le chiffrement à l’aide d’une clé gérée par le client.

## <a name="pre-requisites"></a>Conditions préalables

1. Si vous n’avez pas défini de chiffrement de disque, suivez cet article pour [configurer un Key Vault et un ensemble de chiffrement de disque](../virtual-machines/windows/disks-enable-customer-managed-keys-portal.md#set-up-your-azure-key-vault). Notez les conditions suivantes pour l’ensemble de chiffrement de disque : 

    - L’ensemble de chiffrement de disque doit être **dans la même région et le même abonnement que votre labo**. 
    - Vérifiez que vous (propriétaire du labo) disposez au moins d’un **accès en lecture** à l’ensemble de chiffrement de disque qui sera utilisé pour chiffrer les disques du système d’exploitation du labo.  
2. Pour que le labo gère le chiffrement de tous les disques de système d’exploitation du labo, le propriétaire du labo doit explicitement accorder à l’**identité attribuée par le système** du laboratoire l’autorisation d’accéder à l’ensemble de chiffrement de disque. Le propriétaire du labo peut le faire en procédant comme suit :

    > [!IMPORTANT]
    > Vous devez effectuer ces étapes pour les labos créés le ou après le 01/08/2020. Aucune action n’est requise pour les labos créés avant cette date.

    1. Assurez-vous que vous êtes membre du [rôle Administrateur d’accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator) au niveau de l’abonnement Azure afin de pouvoir gérer l’accès des utilisateurs aux ressources Azure. 
    1. Sur la page **Ensemble de chiffrement de disque**, sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche. 
    1. Sélectionnez **+ Ajouter** dans la barre d’outils, puis **Ajouter une attribution de rôle**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Ajouter la gestion de rôle - Menu":::
    1. Sur la page **Ajouter une attribution de rôle**, sélectionnez le rôle **Lecteur** ou un rôle qui autorise plus d’accès. 
    1. Tapez le nom du labo pour lequel l’ensemble de chiffrement de disque sera utilisé et sélectionnez le nom du labo (identité affectée par le système pour le labo) dans la liste déroulante. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Sélectionner l’identité gérée par le système du labo":::        
    1. Sélectionnez **Enregistrer** dans la barre d’outils. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Enregistrer l’attribution de rôle":::
3. Ajoutez l’**identité affectée par le système** du labo au rôle **Contributeur de machine virtuelle** en utilisant la page **Contrôle**  -> **d’accès (IAM) de l’abonnement**. Les étapes sont similaires à celles de la procédure précédente. 

    > [!IMPORTANT]
    > Vous devez effectuer ces étapes pour les labos créés le ou après le 01/08/2020. Aucune action n’est requise pour les labos créés avant cette date.

    1. Accédez à la page **Abonnement** dans le portail Azure. 
    1. Sélectionnez **Contrôle d’accès (IAM)** . 
    1. Sélectionnez **+ Ajouter** dans la barre d’outils, puis **Ajouter une attribution de rôle**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Abonnement -> page Contrôle d’accès (IAM)":::
    1. Sur la page **Ajouter une attribution de rôle**, sélectionnez **Contributeur de machine virtuelle** pour le rôle.
    1. Tapez le nom du labo et sélectionnez le **nom du labo** (identité affectée par le système pour le labo) dans la liste déroulante. 
    1. Sélectionnez **Enregistrer** dans la barre d’outils. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Chiffrer des disques de système d’exploitation de labo avec une clé gérée par le client 

1. Sur la page d’accueil de votre labo dans le portail Azure, sélectionnez **Configuration et stratégies** dans le menu de gauche. 
1. Sur la page **Configuration et stratégies**, sélectionnez **Disques (version préliminaire)** dans la section **Chiffrement**. Par défaut, **Type de chiffrement** est défini sur **Chiffrement au repos avec la clé gérée par la plateforme**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Onglet Disques de la page Configuration et stratégies":::
1. Pour **Type de chiffrement**, sélectionnez **Chiffrement au repos avec la clé gérée par le client** dans la liste déroulante. 
1. Pour **Ensemble de chiffrement de disque**, sélectionnez l’ensemble de chiffrement de disque créé précédemment. C’est le même ensemble de chiffrement de disque que celui auquel l’identité affectée par le système du labo peut accéder.
1. Sélectionnez **Enregistrer** dans la barre d’outils. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Activer le chiffrement avec des clés gérées par le client":::
1. Dans la zone de message comportant le texte suivant : *Ce paramètre s’applique aux machines créées récemment dans le labo. L’ancien disque du système d’exploitation reste chiffré avec l’ancien ensemble de chiffrement de disque*, sélectionnez **OK**. 

    Une fois configurés, les disques du système d’exploitation du labo sont chiffrés avec la clé gérée par le client fournie lors de l’utilisation de l’ensemble de chiffrement de disque. 

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants : 

- [Azure Disk Encryption](../virtual-machines/windows/disk-encryption.md). 
- [Clés gérées par le client](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 