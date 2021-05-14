---
title: Déplacer des machines virtuelles Azure chiffrées d’une région à une autre à l’aide d’Azure Resource Mover
description: Découvrez comment déplacer des machines virtuelles Azure chiffrées vers une autre région en utilisant Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600690"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Tutoriel : Déplacer des machines virtuelles Azure chiffrées d’une région à une autre

Cet article explique comment déplacer des machines virtuelles Azure chiffrées vers une autre région Azure à l’aide d’[Azure Resource Mover](overview.md). 

Les machines virtuelles chiffrées peuvent être définies de l’une des manières suivantes :

- Machines virtuelles sur lesquelles Azure Disk Encryption est activé. Pour plus d’informations, consultez [Créer et chiffrer une machine virtuelle Windows à l’aide du portail Azure](../virtual-machines/windows/disk-encryption-portal-quickstart.md).
- Machines virtuelles qui utilisent des clés gérées par le client (clés CMK) pour le chiffrement au repos, ou chiffrement côté serveur. Pour plus d’informations, consultez [Utiliser le portail Azure pour activer le chiffrement côté serveur à l’aide de clés gérées par le client pour les disques managés](../virtual-machines/disks-enable-customer-managed-keys-portal.md).


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Vérifier les prérequis. 
> * Pour les machines virtuelles sur lesquelles Azure Disk Encryption est activé, copier les clés et les secrets du coffre de clés de la région source vers le coffre de clés de la région de destination.
> * Préparer le déplacement des machines virtuelles ainsi que la sélection des ressources dans la région source à partir de laquelle vous souhaitez effectuer le déplacement.
> * Résoudre des dépendances de ressources.
> * Pour les machines virtuelles sur lesquelles Azure Disk Encryption est activé, attribuer manuellement le coffre de clés de destination. Pour les machines virtuelles qui utilisent le chiffrement côté serveur avec des clés gérées par le client, attribuer manuellement un jeu de chiffrement de disque dans la région de destination.
> * Déplacer le coffre de clés ou le jeu de chiffrement de disque.
> * Préparer et déplacer le groupe de ressources source. 
> * Préparer et déplacer les autres ressources.
> * Décider si vous souhaitez abandonner ou valider le déplacement. 
> * Le cas échéant, supprimer les ressources dans la région source après le déplacement.

> [!NOTE]
> Ce tutoriel présente le parcours le plus rapide pour tester un scénario. Il utilise uniquement les options par défaut. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer. Ensuite, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prérequis

Condition requise |Détails
--- | ---
**Autorisations d’abonnement** | Vérifiez que vous avez un accès *Propriétaire* sur l’abonnement contenant les ressources que vous souhaitez déplacer.<br/><br/> *Pourquoi ai-je besoin d’un accès Propriétaire ?* La première fois que vous ajoutez une ressource pour une paire source/destination spécifique dans un abonnement Azure, Resource Mover crée une [identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types), anciennement appelée Managed Service Identify (MSI). Cette identité est approuvée par l’abonnement. Avant de pouvoir créer l’identité et lui affecter les rôles nécessaires (*Contributeur* et *Administrateur de l’accès utilisateur* dans l’abonnement source), vous devez vérifier que le compte que vous utilisez pour l’ajout des ressources dispose des autorisations *Propriétaire* sur l’abonnement. Pour plus d’informations, consultez [Rôles Administrateur d’abonnements classiques, rôles Azure et rôles Azure AD](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).
**Prise en charge des machines virtuelles** | Vérifiez que les machines virtuelles que vous souhaitez déplacer sont prises en charge, en procédant comme suit :<li>[Vérifiez](support-matrix-move-region-azure-vm.md#windows-vm-support) les machines virtuelles Windows prises en charge.<li>[Vérifiez](support-matrix-move-region-azure-vm.md#linux-vm-support) les machines virtuelles Linux et les versions du noyau prises en charge.<li>Contrôlez les paramètres de [calcul](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), de [stockage](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) et de [réseau](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) pris en charge.
**Conditions requises pour le coffre de clés (Azure Disk Encryption)** | Si Azure Disk Encryption est activé pour les machines virtuelles, vous avez besoin d’un coffre de clés dans les deux régions (source et de destination). Pour plus d’informations, consultez [Créer un coffre de clés](../key-vault/general/quick-create-portal.md).<br/><br/> Pour les coffres de clés dans les régions source et de destination, vous devez disposer des autorisations suivantes :<li>Autorisations de clé : Get et List pour les opérations de gestion des clés ; Decrypt et Encrypt pour les opérations de chiffrement<li>Autorisations de secret : Get, List et Set pour les opérations de gestion des secrets<li>Certificat : Get et List
**Jeu de chiffrement de disque (chiffrement côté serveur avec CMK)** | Si vous utilisez des machines virtuelles sur lesquelles est activé le chiffrement côté serveur avec des clés gérées par le client, vous avez besoin d’un jeu de chiffrement de disque dans les régions source et de destination. Pour plus d’informations, consultez [Créer un jeu de chiffrement de disque](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Le déplacement interrégional n’est pas pris en charge si vous utilisez un module de sécurité matériel (clés HSM) pour les clés gérées par le client.
**Quota de la région cible** | L’abonnement a besoin d’un quota suffisant pour créer les ressources que vous déplacez dans la région cible. S’il n’a pas le quota, [demandez des limites supplémentaires](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Frais de la région cible** | Renseignez-vous sur les tarifs et les frais qui sont associés à la région cible vers laquelle vous déplacez les machines virtuelles. Pour cela, utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-permissions-in-the-key-vault"></a>Vérifier les autorisations dans le coffre de clés

Si vous déplacez des machines virtuelles sur lesquelles Azure Disk Encryption est activé, vous devez exécuter un script comme cela est expliqué dans la section [Copier les clés dans le coffre de clés de destination](#copy-the-keys-to-the-destination-key-vault). Les utilisateurs qui exécutent le script doivent disposer des autorisations requises. Pour connaître ces autorisations, reportez-vous au tableau ci-dessous. Vous trouverez les options permettant de changer les autorisations en accédant au coffre de clés dans le portail Azure. Sous **Paramètres**, sélectionnez **Stratégies d’accès**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Capture d’écran du lien « Stratégies d’accès » dans le volet Paramètres du coffre de clés." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Si les autorisations utilisateur n’ont pas été définies, sélectionnez **Ajouter une stratégie d’accès**, puis définissez les autorisations. Si le compte d’utilisateur est déjà associé à une stratégie, sous **Utilisateur**, définissez les autorisations selon les instructions données dans le tableau ci-dessous.

Les machines virtuelles Azure qui utilisent Azure Disk Encryption peuvent présenter les différences suivantes. Vous devez donc définir les autorisations pour leurs composants appropriés. Les machines virtuelles peuvent avoir été définies avec :
- Une option par défaut où le disque est chiffré à l’aide de secrets uniquement.
- Une sécurité accrue qui utilise une [clé de chiffrement de clés (clé KEK)](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).

### <a name="source-region-key-vault"></a>Coffre de clés dans la région source

Pour les utilisateurs qui exécutent le script, définissez des autorisations pour les composants suivants : 

Composant | Autorisations nécessaires
--- | ---
Secrets |  *Get* <br></br> Sélectionnez **Autorisations de secret** > **Opérations de gestion des secrets**, puis sélectionnez **Get**. 
Keys <br></br> Si vous utilisez une clé KEK, vous avez besoin de ces autorisations en plus des autorisations pour les secrets. | *Get* et *Decrypt* <br></br> Sélectionnez **Autorisations de clé** > **Opérations de gestion des clés**, puis sélectionnez **Get**. Dans **Opérations de chiffrement**, sélectionnez **Decrypt**.

### <a name="destination-region-key-vault"></a>Coffre de clés dans la région de destination

Dans **Stratégies d’accès**, vérifiez que **Azure Disk Encryption pour chiffrer des volumes** est activé. 

Pour les utilisateurs qui exécutent le script, définissez des autorisations pour les composants suivants : 

Composant | Autorisations nécessaires
--- | ---
Secrets |  *Définir* <br></br> Sélectionnez **Autorisations de secret** > **Opérations de gestion des secrets**, puis sélectionnez **Set**. 
Keys <br></br> Si vous utilisez une clé KEK, vous avez besoin de ces autorisations en plus des autorisations pour les secrets. | *Get*, *Create* et *Encrypt* <br></br> Sélectionnez **Autorisations de clé** > **Opérations de gestion des clés**, puis sélectionnez **Get** et **Create**. Dans **Opérations de chiffrement**, sélectionnez **Encrypt**.

<br>

Outre les autorisations précédentes, dans le coffre de clés de destination, vous devez ajouter des autorisations pour l’[identité MSI (Managed Service Identity)](./common-questions.md#how-is-managed-identity-used-in-resource-mover) avec laquelle Resource Mover accède aux ressources Azure en votre nom. 

1. Sous **Paramètres**, sélectionnez **Ajouter des stratégies d’accès**. 
1. Dans **Sélectionner le principal**, recherchez l’identité MSI. Le nom MSI est ```movecollection-<sourceregion>-<target-region>-<metadata-region>```. 
1. Pour l’identité MSI, ajoutez les autorisations suivantes :

    Composant | Autorisations nécessaires
    --- | ---
    Secrets|  *Get* et *List* <br></br> Sélectionnez **Autorisations de secret** > **Opérations de gestion des secrets**, puis sélectionnez **Get** et **List**. 
    Keys <br></br> Si vous utilisez une clé KEK, vous avez besoin de ces autorisations en plus des autorisations pour les secrets. | *Get* et *List* <br></br> Sélectionnez **Autorisations de clé** > **Opérations de gestion des clés**, puis sélectionnez **Get** et **List**.

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copier les clés dans le coffre de clés de destination

Copiez les secrets et clés de chiffrement du coffre de clés source vers le coffre de clés de destination en exécutant un [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) que nous fournissons.

- Exécutez le script dans PowerShell. Nous vous recommandons d’utiliser la version de PowerShell la plus récente.
- Plus précisément, le script nécessite les modules suivants :
    - Az.Compute
    - Az.KeyVault (version 3.0.0)
    - Az.Accounts (version 2.2.3)

Pour exécuter le script, procédez comme suit :

1. Ouvrez le [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) dans GitHub.
1. Copiez le contenu du script dans un fichier local, puis nommez-le *Copy-keys.ps1*.
1. Exécutez le script.
1. Connectez-vous au portail Azure.
1. Dans les listes déroulantes de la fenêtre **Entrées utilisateur**, sélectionnez l’abonnement source, le groupe de ressources et la machine virtuelle source, puis sélectionnez l’emplacement cible et les coffres cibles pour le chiffrement de disque et de clé.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Capture d’écran de la fenêtre « Entrées utilisateur » où vous entrez les valeurs du script." :::

1. Sélectionnez le bouton **Sélectionner**. 
   
   Une fois l’exécution du script terminée, un message vous avertit que CopyKeys a réussi.

## <a name="prepare-vms"></a>Préparer les machines virtuelles

1. Après avoir vérifié que les machines virtuelles remplissaient les [prérequis](#prerequisites), assurez-vous que les machines virtuelles à déplacer sont allumées. Tous les disques de machine virtuelle que vous souhaitez mettre à disposition dans la région de destination doivent être attachés et initialisés dans chaque machine virtuelle.
1. Pour vérifier que les machines virtuelles disposent des certificats racines approuvés les plus récents et d’une liste de révocation de certificats (CRL) à jour, effectuez les étapes suivantes :
    - Sur les machines virtuelles Windows, installez les dernières mises à jour Windows.
    - Sur les machines virtuelles Linux, suivez les instructions du distributeur pour vérifier que les machines disposent des derniers certificats et des listes de révocation de certificats les plus récentes. 
1. Pour autoriser la connectivité sortante à partir des machines virtuelles, effectuez l’une des opérations suivantes :
    - Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, [autorisez l’accès aux URL](support-matrix-move-region-azure-vm.md#url-access).
    - Si vous utilisez des règles de groupe de sécurité réseau (NSG) pour contrôler la connexion sortante, créez ces [règles d’étiquette de service](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-the-resources-to-move"></a>Sélectionner les ressources à déplacer

- Vous pouvez sélectionner n’importe quel type de ressource pris en charge dans n’importe quel groupe de ressources de la région source sélectionnée.  
- Vous pouvez déplacer des ressources vers une région cible dans le même abonnement que la région source. Si vous voulez changer d’abonnement, vous pouvez le faire après le déplacement des ressources.

Pour sélectionner les ressources, procédez comme suit :

1. Dans le portail Azure, recherchez **resource mover**. Ensuite, sous **Services**, sélectionnez **Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Capture d’écran des résultats de la recherche pour Azure Resource Mover dans le portail Azure." :::

1. Dans le volet Azure Resource Mover - **Vue d’ensemble**, sélectionnez **Move across regions** (Déplacer d’une région à une autre).

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Capture d’écran du bouton « Move across regions » (Déplacer d’une région à une autre) pour ajouter les ressources à déplacer vers une autre région." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. Dans le volet **Déplacer des ressources**, sélectionnez l’onglet **Source + destination**. Ensuite, dans les listes déroulantes, sélectionnez l’abonnement source et la région source.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Page de sélection de la région source et de la région de destination" :::

1. Sous **Destination**, sélectionnez la région vers laquelle vous souhaitez déplacer les machines virtuelles, puis sélectionnez **Suivant**.

1. Sélectionnez l’onglet **Ressources à déplacer**, puis sélectionnez **Sélectionner des ressources**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Capture d’écran du volet « Déplacer des ressources » et du bouton « Sélectionner des ressources »." :::

1. Dans le volet **Sélectionner des ressources**, sélectionnez les machines virtuelles à déplacer. Comme mentionné dans la section [Sélectionner les ressources à déplacer](#select-the-resources-to-move), vous pouvez ajouter uniquement des ressources prises en charge pour un déplacement.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Capture d’écran du volet « Sélectionner des ressources » utilisé pour la sélection des machines virtuelles à déplacer." :::

    > [!NOTE]
    >  Dans ce tutoriel, nous sélectionnons une machine virtuelle qui utilise le chiffrement côté serveur (rayne-vm) avec une clé gérée par le client, et une machine virtuelle sur laquelle le chiffrement de disque est activé (rayne-vm-ade).

1. Sélectionnez **Terminé**.
1. Sélectionnez l’onglet **Ressources à déplacer**, puis sélectionnez **Suivant**.
1. Sélectionnez l’onglet **Revue**, puis vérifiez les paramètres de source et de destination. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Capture d’écran du volet utilisé pour revoir les paramètres de source et de destination." :::

1. Cliquez sur **Continuer** pour commencer à ajouter les ressources.
1. Sélectionnez l’icône de notifications pour suivre la progression. Une fois le processus terminé, dans le volet **Notifications**, sélectionnez **Ressources ajoutées pour le déplacement**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Capture d’écran du volet « Notifications » utilisé pour confirmer l’ajout des ressources." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Après avoir sélectionné la notification, passez en revue les ressources dans la page **Entre régions**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Capture d’écran des ressources ajoutées et présentant l’état « Préparation en attente »." :::

> [!NOTE]
> - Les ressources que vous ajoutez sont mises dans l’état *Préparation en attente*.
> - Le groupe de ressources pour les machines virtuelles est ajouté automatiquement.
> - Si vous modifiez les entrées **Configuration de destination** pour utiliser une ressource qui existe déjà dans la région de destination, l’état de la ressource passe à *Validation en attente*, car vous n’avez pas besoin de lancer un déplacement pour elle.
> - Si vous souhaitez supprimer une ressource qui a été ajoutée, la méthode à utiliser dépend de l’étape où vous vous trouvez dans le processus de déplacement. Pour plus d’informations, consultez [Gérer les collections de déplacement et les groupes de ressources](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Résoudre les erreurs de dépendance

1. Si des ressources affichent un message *Valider les dépendances* dans la colonne **Problèmes**, cliquez sur le bouton **Valider les dépendances**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Capture d’écran montrant le bouton « Valider les dépendances »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Le processus de validation démarre.
1. Si des dépendances sont trouvées, cliquez sur **Ajouter des dépendances**.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Capture d’écran montrant le bouton « Ajouter des dépendances »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. Dans le volet **Ajouter des dépendances**, conservez l’option par défaut **Afficher toutes les dépendances**.

    - L’option **Afficher toutes les dépendances** itère au sein de toutes les dépendances directes et indirectes d’une ressource. Par exemple, pour une machine virtuelle, la carte réseau, le réseau virtuel, les groupes de sécurité réseau, etc. sont affichés.
    - L’option **Afficher uniquement les dépendances de premier niveau** n’affiche que les dépendances directes. Par exemple, pour une machine virtuelle, la carte réseau est affichée, mais pas le réseau virtuel.
 
1. Sélectionnez les ressources dépendantes que vous souhaitez ajouter, puis sélectionnez **Ajouter des dépendances**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Capture d’écran de la liste des dépendances et du bouton « Ajouter des dépendances »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Revalidez les dépendances. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Capture d’écran du volet utilisé pour revalider les dépendances." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Affecter les ressources de destination

Vous devez attribuer manuellement les ressources de destination qui sont associées au chiffrement.

- Si vous déplacez une machine virtuelle sur laquelle Azure Disk Encryption est activé, le coffre de clés dans votre région de destination est affiché en tant que dépendance.
- Si vous déplacez une machine virtuelle sur laquelle est activé le chiffrement côté serveur avec des clés gérées par le client, le jeu de chiffrement de disque dans la région de destination est affiché en tant que dépendance. 
- Étant donné que ce tutoriel montre comment déplacer une machine virtuelle sur laquelle Azure Disk Encryption est activé et qui utilise une clé gérée par le client, le coffre de clés de destination et le jeu de chiffrement de disque s’affichent tous les deux en tant que dépendances.

Pour attribuer les ressources de destination manuellement, effectuez les étapes suivantes :

1. Dans l’entrée du jeu de chiffrement de disque, sélectionnez **Ressource non affectée** dans la colonne **Configuration de destination**.
1. Dans **Paramètres de configuration**, sélectionnez le jeu de chiffrement de disque de destination, puis sélectionnez **Enregistrer les modifications**.
1. Vous pouvez enregistrer et valider les dépendances de la ressource que vous modifiez, ou enregistrer uniquement les modifications et valider ensuite tout ce que vous modifiez en même temps.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Capture d’écran du volet « Configuration de la destination » utilisé pour l’enregistrement des modifications dans la région de destination." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Une fois que vous avez ajouté la ressource de destination, l’état du jeu de chiffrement de disque change en *Validation du déplacement en attente*.

1. Dans l’entrée du coffre de clés, sélectionnez **Ressource non affectée** dans la colonne **Configuration de destination**. Sous **Paramètres de configuration**, sélectionnez le coffre de clés de destination, puis enregistrez vos modifications. 

À ce stade, l’état du jeu de chiffrement de disque et celui du coffre de clés changent en *Validation du déplacement en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Capture d’écran du volet utilisé pour préparer d’autres ressources." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Pour valider et terminer le processus de déplacement des ressources de chiffrement, effectuez ces étapes :

1. Dans **Entre régions**, sélectionnez la ressource (jeu de chiffrement de disque ou coffre de clés), puis sélectionnez **Valider le déplacement**.
1. Dans **Déplacer des ressources**, sélectionnez **Valider**.

> [!NOTE]
> Une fois que vous avez validé le déplacement, l’état de la ressource change en *Suppression de la source en attente*.


## <a name="move-the-source-resource-group"></a>Déplacer le groupe de ressources source 

Avant de pouvoir préparer et déplacer une machine virtuelle, le groupe de ressources de cette machine virtuelle doit être présent dans la région cible. 

### <a name="prepare-to-move-the-source-resource-group"></a>Préparer le déplacement du groupe de ressources source

Pendant le processus de préparation, Resource Mover génère des modèles Azure Resource Manager (ARM) à partir des paramètres du groupe de ressources. Les ressources à l’intérieur du groupe de ressources ne sont pas impactées.

Pour préparer le déplacement du groupe de ressources source, procédez comme suit :

1. Dans **Entre régions**, sélectionnez le groupe de ressources source, puis sélectionnez **Préparer**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Capture d’écran du bouton « Préparer » dans le volet « Préparer les ressources »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. Dans **Préparer les ressources**, sélectionnez **Préparer**.

> [!NOTE]
> Une fois que vous avez préparé le déplacement, l’état du groupe de ressources change en *Lancement du déplacement en attente*. 

 
### <a name="move-the-source-resource-group"></a>Déplacer le groupe de ressources source

Lancez le déplacement du groupe de ressources source en effectuant ces étapes :

1. Dans le volet **Entre les régions**, sélectionnez le groupe de ressources, puis sélectionnez **Lancer le déplacement**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Capture d’écran du bouton « Lancer le déplacement » dans le volet « Entre les régions »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. Dans le volet **Déplacer des ressources**, sélectionnez **Lancer le déplacement**. L’état du groupe de ressources change en *Lancement du déplacement en cours*.   
1. Une fois le déplacement lancé, le groupe de ressources cible est créé sur la base du modèle ARM généré. L’état du groupe de ressources source change en *Validation du déplacement en attente*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Capture d’écran du volet « Déplacer des ressources » qui montre l’état du groupe de ressources changé en « Validation du déplacement en attente »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Pour valider le déplacement et terminer le processus, procédez comme suit :

1. Dans le volet **Entre les régions**, sélectionnez le groupe de ressources, puis sélectionnez **Valider le déplacement**.
1. Dans le volet **Déplacer des ressources**, sélectionnez **Valider**.

> [!NOTE]
> Une fois que vous avez validé le déplacement, l’état du groupe de ressources change en *Suppression de la source en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Capture d’écran du groupe de ressources source qui montre l’état changé en « Suppression de la source en attente »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Préparer les ressources à déplacer

Maintenant que les ressources de chiffrement et le groupe de ressources source sont déplacés, vous pouvez préparer le déplacement d’autres ressources dont l’état actuel est *Préparation en attente*.


1. Dans le volet **Entre les régions**, revalidez le déplacement et résolvez les problèmes éventuels.
1. Si vous souhaitez modifier les paramètres cibles avant de commencer le déplacement, sélectionnez le lien dans la colonne **Configuration de la destination** pour la ressource, puis modifiez les paramètres. Si vous modifiez les paramètres de la machine virtuelle cible, la taille de cette machine ne doit pas être inférieure à celle de la machine virtuelle source.
1. Pour les ressources à déplacer qui présentent l’état *Préparation en attente*, sélectionnez **Préparer**.
1. Dans le volet **Préparer les ressources**, sélectionnez **Préparer**.

    - Durant la préparation, l’agent Mobilité d’Azure Site Recovery est installé sur les machines virtuelles pour les besoins de la réplication.
    - Les données des machines virtuelles sont répliquées régulièrement dans la région cible. Cette opération n’a pas d’incidence sur la machine virtuelle source.
    - Le déplacement de ressources génère des modèles ARM pour les autres ressources sources.

> [!NOTE]
> Après avoir préparé les ressources, leur état change en *Lancement du déplacement en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Capture d’écran du volet « Préparer les ressources », montrant les ressources à l’état « Lancement du déplacement en attente »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Lancer le déplacement

La préparation des ressources est terminée. Vous pouvez maintenant lancer le déplacement. 

1. Dans le volet **Entre les régions**, sélectionnez les ressources dont l’état est *Lancement du déplacement en attente*, puis sélectionnez **Lancer le déplacement**.
1. Dans le volet **Déplacer des ressources**, sélectionnez **Lancer le déplacement**.
1. Suivez la progression du déplacement dans la barre de notification.

    - Pour les machines virtuelles, les machines virtuelles de réplication sont créées dans la région cible. La machine virtuelle source est arrêtée, et un temps d’interruption se produit (généralement en minutes).
    - Resource Mover recrée d’autres ressources à l’aide des modèles ARM préparés. Il n’y a généralement pas de temps d’arrêt.
    - Une fois que les ressources ont été déplacées, leur état change en *Validation du déplacement en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Capture d’écran d’une liste de ressources à l’état « Validation du déplacement en attente »." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Abandonner ou valider ?

Après le déplacement initial, vous pouvez décider de valider le déplacement ou de l’abandonner. 

- **Abandonner** : vous pouvez abandonner un déplacement s’il s’agissait seulement d’un test et que vous ne voulez pas déplacer réellement la ressource source. Si vous abandonnez le déplacement, la ressource revient à l’état *Lancement du déplacement en attente*.
- **Valider** : La validation termine le déplacement vers la région cible. Une fois que vous avez validé une ressource source, son état change en *Suppression de la source en attente*. Vous pouvez alors décider de la supprimer.


## <a name="discard-the-move"></a>Abandonner le déplacement 

Pour abandonner le déplacement, effectuez les étapes suivantes :

1. Dans le volet **Entre les régions**, sélectionnez les ressources dont l’état est *Validation du déplacement en attente*, puis sélectionnez **Abandonner le déplacement**.
1. Dans le volet **Abandonner le déplacement**, sélectionnez **Abandonner**.
1. Suivez la progression du déplacement dans la barre de notification.


> [!NOTE]
> Après l’abandon des ressources, l’état des machines virtuelles change en *Lancement du déplacement en attente*.

## <a name="commit-the-move"></a>Valider le déplacement

Pour terminer le processus de déplacement, validez le déplacement en procédant comme suit : 

1. Dans le volet **Entre les régions**, sélectionnez les ressources dont l’état est *Validation du déplacement en attente*, puis sélectionnez **Valider le déplacement**.
1. Dans le volet **Valider les ressources**, sélectionnez **Valider**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Capture d’écran d’une liste des ressources à valider pour terminer le déplacement." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Suivez la progression de la validation dans la barre de notification.

> [!NOTE]
> - Une fois que vous avez validé le déplacement, les machines virtuelles arrêtent la réplication. La machine virtuelle source n’est pas impactée par la validation.
> - Le processus de validation ne s’applique pas aux ressources réseau sources.
> - Une fois que vous avez validé le déplacement, l’état de chaque ressource change en *Suppression de la source en attente*.



## <a name="configure-settings-after-the-move"></a>Configurer des paramètres après le déplacement

- Le service Mobilité n’est pas désinstallé automatiquement des machines virtuelles. Désinstallez-le manuellement ou laissez-le si vous envisagez de déplacer à nouveau le serveur.
- Modifiez les règles de contrôle d’accès en fonction du rôle (RBAC) Azure après le déplacement.

## <a name="delete-source-resources-after-commit"></a>Supprimer les ressources sources après la validation

Après le déplacement, vous pouvez, si vous le souhaitez, supprimer les ressources dans la région source. 

1. Dans le volet **Entre les régions**, sélectionnez chaque ressource source à supprimer, puis sélectionnez **Supprimer la source**.
1. Dans **Supprimer la source**, passez en revue les ressources que vous comptez supprimer puis, dans **Confirmer la suppression**, tapez **Oui**. L’action étant irréversible, vérifiez attentivement !
1. Après avoir tapé **Oui**, sélectionnez **Supprimer la source**.

> [!NOTE]
>  Dans le portail Resource Move, vous ne pouvez pas supprimer des groupes de ressources, des coffres de clés ni des instances SQL Server. Vous devez les supprimer individuellement à partir de la page de propriétés de chaque ressource.


## <a name="delete-resources-that-you-created-for-the-move"></a>Supprimer les ressources créées pour le déplacement

Après le déplacement, vous pouvez supprimer manuellement la collection de déplacement ainsi que les ressources Site Recovery que vous avez créées durant ce processus.

- La collection de déplacement est masquée par défaut. Pour la voir, vous devez activer les ressources masquées.
- Le stockage de cache est équipé d’un verrou qui doit être supprimé avant de pouvoir procéder à la suppression.

Pour supprimer vos ressources, procédez comme suit : 
1. Localisez les ressources dans le groupe de ressources ```RegionMoveRG-<sourceregion>-<target-region>```.
1. Vérifiez que toutes les machines virtuelles et les autres ressources sources dans la région source ont été déplacées ou supprimées. Cette étape permet de vous assurer qu’aucune ressource en attente ne les utilise.
1. Supprimer les ressources :

    - Nom de la collection de déplacement : ```movecollection-<sourceregion>-<target-region>```
    - Nom du compte de stockage de cache : ```resmovecache<guid>```
    - Nom du coffre : ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Déplacé des machines virtuelles Azure chiffrées et leurs ressources dépendantes vers une autre région Azure.


À présent, vous allez essayer de déplacer des pools élastiques et des bases de données Azure SQL vers une autre région.

> [!div class="nextstepaction"]
> [Déplacer des ressources Azure SQL](./tutorial-move-region-sql.md)
