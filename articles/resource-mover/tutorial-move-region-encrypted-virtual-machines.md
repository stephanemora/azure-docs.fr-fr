---
title: Déplacer des machines virtuelles Azure chiffrées d’une région à une autre avec Azure Resource Mover
description: Découvrez comment déplacer des machines virtuelles Azure chiffrées dans une autre région avec Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361007"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Tutoriel : Déplacer des machines virtuelles Azure chiffrées d’une région à une autre

Dans cet article, vous allez découvrir comment déplacer des machines virtuelles Azure chiffrées vers une autre région Azure à l’aide d’[Azure Resource Mover](overview.md). Voici ce que nous voulons dire par chiffrement :

- Machines virtuelles sur lesquelles Azure Disk Encryption est activé. [En savoir plus](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Ou, machines virtuelles qui utilisent des clés gérées par le client (clés CMK) pour le chiffrement au repos (chiffrement côté serveur). [En savoir plus](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Vérifier les prérequis. 
> * Pour les machines virtuelles sur lesquelles Azure Disk Encryption est activé, copier les clés et les secrets du coffre de clés de la région source vers le coffre de clés de la région de destination.
> * Préparer les machines virtuelles pour leur déplacement, puis sélectionner les ressources dans la région source que vous souhaitez déplacer.
> * Résoudre des dépendances de ressources.
> * Pour les machines virtuelles sur lesquelles Azure Disk Encryption est activé, attribuer manuellement le coffre de clés de destination. Pour les machines virtuelles utilisant le chiffrement côté serveur avec des clés gérées par le client, attribuer manuellement un jeu de chiffrement de disque dans la région de destination.
> * Déplacer le coffre de clés et/ou le jeu de chiffrement de disque.
> * Préparer et déplacer le groupe de ressources source. 
> * Préparer et déplacer les autres ressources.
> * Décider si vous souhaitez abandonner ou valider le déplacement. 
> * Le cas échéant, supprimer les ressources dans la région source après le déplacement.

> [!NOTE]
> Les tutoriels enseignent le moyen le plus rapide de tester un scénario et d’utiliser les options par défaut. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer. Puis, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prérequis

**Prérequis** |**Détails**
--- | ---
**Autorisations d’abonnement** | Veillez à disposer d’un accès *Propriétaire* sur l’abonnement contenant les ressources que vous souhaitez déplacer.<br/><br/> **Pourquoi j’ai besoin d’un accès Propriétaire ?** La première fois que vous ajoutez une ressource pour une paire source et destination spécifique dans un abonnement Azure, Resource Mover crée une [identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anciennement Managed Service Identify, MSI) qui est approuvée par l’abonnement. Afin de créer l’identité et lui affecter le rôle demandé (Contributeur et Administrateur de l’accès utilisateur dans l’abonnement source), le compte que vous utilisez pour ajouter des ressources a besoin des autorisations *Propriétaire* sur l’abonnement. [Explorez en détail](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) les rôles Azure.
**Prise en charge des machines virtuelles** | Assurez-vous que les machines virtuelles que vous souhaitez déplacer sont prises en charge.<br/><br/> - [Vérifiez](support-matrix-move-region-azure-vm.md#windows-vm-support) les machines virtuelles Windows prises en charge.<br/><br/> - [Vérifiez](support-matrix-move-region-azure-vm.md#linux-vm-support) les machines virtuelles Linux et les versions du noyau prises en charge.<br/><br/> - Contrôlez les paramètres de [calcul](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), de [stockage](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) et de [réseau](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) pris en charge.
**Conditions requises pour le coffre de clés (Azure Disk Encryption)** | Si Azure Disk Encryption est activé pour les machines virtuelles, en plus du coffre de clés dans la région source, vous avez besoin d’un coffre de clés dans la région de destination. [Créez un coffre de clés](../key-vault/general/quick-create-portal.md).<br/><br/> Pour les coffres de clés dans la région source et la région cible, vous devez disposer des autorisations suivantes :<br/><br/> - Autorisations de clés : Opérations de gestion des clés (Obtenir, Lister) ; Opérations de chiffrement (Déchiffrer et Chiffrer).<br/><br/> - Autorisations de secret : Opérations de gestion des secrets (Obtenir, Lister et Définir)<br/><br/> - Certificat (Lister et Obtenir).
**Jeu de chiffrement de disque (chiffrement côté serveur avec CMK)** | Si vous utilisez des machines virtuelles avec chiffrement côté serveur à l’aide d’une CMK, en plus du jeu de chiffrement de disque dans la région source, vous avez besoin d’un jeu de chiffrement de disque dans la région de destination. [Créez un jeu de chiffrement de disque](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Le déplacement entre régions n’est pas pris en charge si vous utilisez des clés HSM pour les clés gérées par le client.
**Quota de la région cible** | L’abonnement a besoin d’un quota suffisant pour créer les ressources que vous déplacez dans la région cible. S’il n’a pas le quota, [demandez des limites supplémentaires](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Frais de la région cible** | Vérifiez le tarif et les frais associés à la région cible vers laquelle vous déplacez des machines virtuelles. Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour vous aider.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Vérifier les autorisations utilisateur sur le coffre de clés pour les machines virtuelles à l’aide d’Azure Disk Encryption (ADE)

Si vous déplacez des machines virtuelles sur lesquelles Azure Disk Encryption est activé, vous devez exécuter un script comme indiqué [ci-dessous](#copy-the-keys-to-the-destination-key-vault) pour lequel l’utilisateur qui exécute le script doit disposer des autorisations appropriées. Reportez-vous au tableau ci-dessous pour en savoir plus sur les autorisations nécessaires. Les options permettant de modifier les autorisations sont accessibles en accédant au coffre de clés dans le portail Azure. Sous **Paramètres**, sélectionnez **Stratégies d’accès**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Bouton permettant d’ouvrir les stratégies d’accès au coffre de clés." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

S’il n’y a pas d’autorisations utilisateur, sélectionnez **Ajouter une stratégie d’accès** et spécifiez les autorisations. Si le compte d’utilisateur a déjà une stratégie, sous **Utilisateur**, définissez les autorisations selon le tableau ci-dessous.

Les machines virtuelles Azure utilisant ADE peuvent présenter les variations suivantes et les autorisations doivent être définies en conséquence pour les composants appropriés.
- Option par défaut avec laquelle le disque est chiffré à l’aide de secrets uniquement
- Sécurité accrue avec la [clé de chiffrement principale](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Coffre de clés de la région source

Les autorisations ci-dessous doivent être définies pour l’utilisateur qui exécute le script 

**Composant** | **Autorisation requise**
--- | ---
Secrets|  Autorisation Get <br> </br> Dans **Autorisations de secret**>  **Opérations de gestion des secrets**, sélectionnez **Get** 
Keys <br> </br> Si vous utilisez la clé de chiffrement principale, vous avez besoin de cette autorisation en plus des secrets| Autorisations Get et Decrypt <br> </br> Dans **Autorisations de clé** > **Opérations de gestion des clés**, sélectionnez **Get**. Dans **Opérations de chiffrement**, sélectionnez **Decrypt**.

### <a name="destination-region-keyvault"></a>Coffre de clés de la région de destination

Dans **Stratégies d’accès**, vérifiez que **Azure Disk Encryption pour chiffrer des volumes** est activé. 

Les autorisations ci-dessous doivent être définies pour l’utilisateur qui exécute le script 

**Composant** | **Autorisation requise**
--- | ---
Secrets|  Autorisation Set <br> </br> Dans **Autorisations de secret**>  **Opérations de gestion des secrets**, sélectionnez **Set** 
Keys <br> </br> Si vous utilisez la clé de chiffrement principale, vous avez besoin de cette autorisation en plus des secrets| Autorisations Get, Create et Encrypt <br> </br> Dans **Autorisations de clé** > **Opérations de gestion des clés**, sélectionnez **Get** et **Create**. Dans **Opérations de chiffrement**, sélectionnez **Encrypt**.

Outre les autorisations ci-dessus, dans le coffre de clés de destination, vous devez ajouter des autorisations pour l’identité [Managed Service Identity](./common-questions.md#how-is-managed-identity-used-in-resource-mover) utilisée par Resource Mover pour accéder aux ressources Azure en votre nom. 

1. Sous **Paramètres**, sélectionnez **Ajouter des stratégies d’accès**. 
2. Dans **Sélectionner le principal**, recherchez l’identité MSI. Le nom MSI est ```movecollection-<sourceregion>-<target-region>-<metadata-region>```. 
3. Ajoutez les autorisations ci-dessous pour l’identité MSI

**Composant** | **Autorisation requise**
--- | ---
Secrets|  Autorisations Get et List <br> </br> Dans **Autorisations de secret**>  **Opérations de gestion des secrets**, sélectionnez **Get** et **List** 
Keys <br> </br> Si vous utilisez la clé de chiffrement principale, vous avez besoin de cette autorisation en plus des secrets| Autorisations Get, List <br> </br> Dans **Autorisations de clé** > **Opérations de gestion des clés**, sélectionnez **Get** et **List**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copier les clés dans le coffre de clés de destination

Vous devez copier les secrets de chiffrement et les clés du coffre de clés source vers le coffre de clés de destination, à l’aide d’un script que nous fournissons.

- Vous exécutez le script dans PowerShell. Nous vous recommandons d’exécuter la dernière version de PowerShell.
- Plus précisément, le script nécessite les modules suivants :
    - Az.Compute
    - Az.KeyVault (version 3.0.0)
    - Az.Accounts (version 2.2.3)

Exécutez comme suit :

1. Accédez au [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) dans GitHub.
2. Copiez le contenu du script dans un fichier local, puis nommez-le *Copy-keys.ps1*.
3. Exécutez le script.
4. Connectez-vous à Azure.
5. Dans la fenêtre contextuelle **Entrée utilisateur**, sélectionnez l’abonnement source, le groupe de ressources et la machine virtuelle source. Sélectionnez ensuite l’emplacement cible et les coffres cibles pour le chiffrement de disque et de clé.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Fenêtre contextuelle pour entrer les valeurs de script." :::


6. Une fois le script terminé, la sortie indique que CopyKeys a réussi.

## <a name="prepare-vms"></a>Préparer les machines virtuelles

1. Après avoir [vérifié que les machines virtuelles répondaient aux conditions requises](#prerequisites), vérifiez que les machines virtuelles que vous souhaitez déplacer sont allumées. Tous les disques de machines virtuelles que vous souhaitez mettre à disposition dans la région de destination doivent être attachés et initialisés dans la machine virtuelle.
3. Vérifiez que les machines virtuelles disposent des certificats racines approuvés les plus récents et d’une liste de révocation de certificats (CRL) mise à jour. Pour ce faire :
    - Sur les machines virtuelles Windows, installez les dernières mises à jour Windows.
    - Sur les machines virtuelles Linux, suivez les instructions du distributeur afin que les machines disposent des derniers certificats et des listes de révocation de certificats les plus récentes. 
4. Autorisez une connexion sortante à partir des machines virtuelles de la manière suivante :
    - Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connexion sortante, autorisez l’accès à ces [URL](support-matrix-move-region-azure-vm.md#url-access) :
    - Si vous utilisez des règles de groupe de sécurité réseau (NSG) pour contrôler la connexion sortante, créez ces [règles d’étiquette de service](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Sélectionner les ressources à déplacer


- Vous pouvez sélectionner n’importe quel type de ressource pris en charge dans n’importe quel groupe de ressources de la région source sélectionnée.  
- Vous déplacez des ressources dans une région cible du même abonnement que la région source. Si vous voulez changer d’abonnement, vous pouvez le faire après le déplacement des ressources.

Sélectionnez les ressources comme suit :

1. Dans le portail Azure, recherchez *resource mover*. Ensuite, sous **Services**, sélectionnez **Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Résultats de la recherche de Resource Mover dans le portail Azure." :::

2. Dans **Vue d’ensemble**, cliquez sur **Passer d’une région à l’autre**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Bouton pour ajouter des ressources à déplacer dans une autre région" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. Dans **Déplacer des ressources** > **Source + destination**, sélectionnez l’abonnement et la région sources.
4. Dans **Destination**, sélectionnez la région vers laquelle vous souhaitez déplacer les machines virtuelles. Cliquez ensuite sur **Suivant**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Page de sélection de la région source et de la région de destination" :::

5. Dans **Ressources à déplacer**, cliquez sur **Sélectionner des ressources**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Bouton permettant de sélectionner la ressource à déplacer.]." :::

6. Dans **Sélectionner des ressources**, sélectionnez les machines virtuelles. Vous pouvez uniquement ajouter des ressources [prises en charge pour le déplacement](#prepare-vms). Cliquez ensuite sur **Terminé**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Page de sélection des machines virtuelles à déplacer." :::

    > [!NOTE]
    >  Dans ce tutoriel, nous allons sélectionner une machine virtuelle qui utilise le chiffrement côté serveur (rayne-vm) avec une clé gérée par le client, et une machine virtuelle sur laquelle le chiffrement de disque est activé (rayne-vm-ade).

7.  Dans **Ressources à déplacer**, cliquez sur **Suivant**.
8. Dans **Vérifier**, contrôlez les paramètres de source et de destination. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Page de vérification des paramètres et de la poursuite du déplacement." :::

9. Cliquez sur **Continuer** pour commencer à ajouter les ressources.
10. Sélectionnez l’icône de notifications pour suivre la progression. Une fois le processus d’ajout terminé, sélectionnez **Ressources ajoutées pour le déplacement** dans les notifications.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Notification pour confirmer que les ressources ont bien été ajoutées." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Après avoir cliqué sur la notification, passez en revue les ressources sur la page **Entre régions**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Pages montrant des ressources ajoutées avec préparation en attente." :::

> [!NOTE]
> - Les ressources que vous ajoutez sont placées à l’état *Préparation en attente*.
> - Le groupe de ressources pour les machines virtuelles est ajouté automatiquement.
> - Si vous modifiez les entrées de **Configuration de destination** pour utiliser une ressource qui existe déjà dans la région de destination, l’état de la ressource devient *Validation en attente*, car vous n’avez pas besoin de lancer un déplacement pour elle.
> - Si vous souhaitez supprimer une ressource qui a été ajoutée, la méthode permettant d’effectuer cette opération dépend de l’étape où vous vous trouvez dans la procédure de déplacement. [Plus d’informations](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Résoudre les erreurs de dépendance

1. Si des ressources affichent un message *Valider les dépendances* dans la colonne **Problèmes**, cliquez sur le bouton **Valider les dépendances**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Bouton permettant de vérifier les dépendances." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Le processus de validation démarre.
2. Si des dépendances sont trouvées, cliquez sur **Ajouter des dépendances**.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Bouton pour ajouter des dépendances." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. Dans **Ajouter des dépendances**, conservez l’option par défaut **Afficher toutes les dépendances**.

    - **Afficher toutes les dépendances** itère au sein de toutes les dépendances directes et indirectes d’une ressource. Par exemple, pour une machine virtuelle, la carte réseau, le réseau virtuel, les groupes de sécurité réseau, et ainsi de suite, sont affichés.
    - **Afficher uniquement les dépendances de premier niveau** affiche uniquement les dépendances directes. Par exemple, pour une machine virtuelle, la carte réseau est affichée, mais pas le réseau virtuel.
 
4. Sélectionnez les ressources dépendantes que vous souhaitez ajouter > **Ajouter des dépendances**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Sélectionnez les dépendances dans la liste." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Revalidez les dépendances. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Page de revalidation." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Affecter les ressources de destination

Les ressources de destination associées au chiffrement nécessitent une affectation manuelle.

- Si vous déplacez une machine virtuelle sur laquelle Azure Disk Encryption est activé, le coffre de clés de votre région de destination est affiché en tant que dépendance.
- Si vous déplacez une machine virtuelle avec chiffrement côté serveur qui utilise des clés gérées par le client (CMK), alors le jeu de chiffrement de disque dans la région de destination est affiché en tant que dépendance. 
- Étant donné que ce tutoriel déplace une machine virtuelle avec Azure Disk Encryption activé et une machine virtuelle utilisant une CMK, le coffre de clés de destination et le jeu de chiffrement de disque sont tous deux affichés en tant que dépendances.

Procédez à une affectation manuelle comme suit :

1. Dans l’entrée du jeu de chiffrement de disque, sélectionnez **Ressource non affectée** dans la colonne **Configuration de destination**.
2. Dans **Paramètres de configuration**, sélectionnez le jeu de chiffrement de disque de destination. Sélectionnez ensuite **Enregistrer les modifications**.
3. Vous pouvez choisir d’enregistrer et de valider les dépendances de la ressource que vous modifiez, ou simplement enregistrer les modifications et valider tout ce que vous modifiez en une seule fois.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Page de sélection du jeu de chiffrement de disque dans la région de destination." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Après l’ajout de la ressource de destination, l’état du jeu de chiffrement de disque devient *Validation du déplacement en attente*.
3. Dans l’entrée du coffre de clés, sélectionnez **Ressource non affectée** dans la colonne **Configuration de destination**. Dans **Paramètres de configuration**, sélectionnez le coffre de clés de destination. Enregistrez les modifications. 

À ce stade, l’état du jeu de chiffrement de disque et du coffre de clés devient *Validation du déplacement en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Page de sélection pour la préparation d’autres ressources." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Pour valider et terminer le processus de déplacement pour les ressources de chiffrement

1. Dans **Entre régions**, sélectionnez la ressource (jeu de chiffrement de disque ou coffre de clés) > **Valider le déplacement**.
2. Dans **Déplacer des ressources**, cliquez sur **Valider**.

> [!NOTE]
> Après la validation du déplacement, la ressource est à l’état *Suppression de la source en attente*.


## <a name="move-the-source-resource-group"></a>Déplacer le groupe de ressources source 

Avant de pouvoir préparer et déplacer une machine virtuelle, le groupe de ressources de cette machine virtuelle doit être présent dans la région cible. 

### <a name="prepare-to-move-the-source-resource-group"></a>Préparer le déplacement du groupe de ressources source

Pendant le processus de préparation, Resource Mover génère des modèles Azure Resource Manager (ARM) à l’aide des paramètres du groupe de ressources. Les ressources à l’intérieur du groupe de ressources ne sont pas affectées.

Préparez de la manière suivante :

1. Dans **Entre régions**, sélectionnez le groupe de ressources source > **Préparer**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Préparer un groupe de ressources." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. Dans **Préparer les ressources**, cliquez sur **Préparer**.

> [!NOTE]
> Après sa préparation, le groupe de ressources présente l’état *Lancement du déplacement en attente*. 

 
### <a name="move-the-source-resource-group"></a>Déplacer le groupe de ressources source

Lancez le déplacement de la manière suivante :

1. Dans **Entre régions**, sélectionnez le groupe de ressources > **Lancer le déplacement**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Bouton de lancement du déplacement." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. Dans **Déplacer des ressources**, cliquez sur **Lancer le déplacement**. Le groupe de ressources passe à l’état *Lancement du déplacement en cours*.   
3. Après l’initialisation du déplacement, le groupe de ressources cible est créé, en fonction du modèle ARM généré. Le groupe de ressources source passe à l’état *Validation du déplacement en attente*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Passez en revue l’état de validation du déplacement en attente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Pour valider et terminer la procédure de déplacement :

1. Dans **Entre régions**, sélectionnez le groupe de ressources > **Valider le déplacement**.
2. Dans **Déplacer des ressources**, cliquez sur **Valider**.

> [!NOTE]
> Après la validation du déplacement, le groupe de ressources source présente l’état *Suppression de la source en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Passez en revue l’état de suppression du déplacement en attente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Préparer les ressources à déplacer

Maintenant que les ressources de chiffrement et le groupe de ressources source sont déplacés, vous pouvez préparer le déplacement d’autres ressources qui sont à l’état *Préparation en attente*.


1. Dans **Entre régions**, validez à nouveau et résolvez tous les problèmes.
2. Si vous souhaitez modifier les paramètres cibles avant de commencer le déplacement, sélectionnez le lien dans la colonne **Configuration de la destination** pour la ressource, puis modifiez les paramètres. Si vous modifiez les paramètres de la machine virtuelle cible, la taille de cette machine ne doit pas être inférieure à celle de la machine virtuelle source.
3. Sélectionnez **Préparer** pour les ressources à l’état *Préparation en attente* que vous souhaitez déplacer.
3. Dans **Préparer les ressources**, sélectionnez **Préparer**.

    - Durant le processus de préparation, l’agent de mobilité Azure Site Recovery est installé sur les machines virtuelles, en vue de leur réplication.
    - Les données des machines virtuelles sont répliquées régulièrement dans la région cible. Cette opération n’a pas d’incidence sur la machine virtuelle source.
    - Le déplacement de ressources génère des modèles ARM pour les autres ressources sources.

Après leur préparation, les ressources présentent l’état *Lancement du déplacement en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Page montrant les ressources à l’état de lancement du déplacement en attente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Lancer le déplacement

Les ressources préparées, vous pouvez maintenant lancer le déplacement. 

1. Dans **Entre régions**, sélectionnez les ressources avec l’état *Lancement du déplacement en attente*. Cliquez ensuite sur **Lancer le déplacement**.
2. Dans **Déplacer des ressources**, cliquez sur **Lancer le déplacement**.
3. Suivez la progression du déplacement dans la barre de notification.

    - Pour les machines virtuelles, les machines virtuelles de réplication sont créées dans la région cible. La machine virtuelle source est arrêtée, et un temps d’interruption se produit (généralement en minutes).
    - Resource Mover recrée d’autres ressources à l’aide des modèles ARM préparés. Il n’y a généralement pas de temps d’arrêt.
    - Après leur déplacement, les ressources présentent l’état *Validation du lancement en attente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Page montrant les ressources à l’état d’attente de validation du déplacement." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Abandonner ou valider ?

Après le déplacement initial, vous pouvez décider de valider le déplacement ou de l’abandonner. 

- **Abandonner** : vous pouvez abandonner un déplacement si vous effectuez un test et que vous ne souhaitez pas déplacer réellement la ressource source. Si vous abandonnez le déplacement, la ressource reviendra à l’état *Lancement du déplacement en attente*.
- **Valider** : La validation termine le déplacement vers la région cible. Après sa validation, une ressource source se présente avec l’état de *Suppression de la source en attente*, et vous pouvez décider si vous voulez la supprimer.


## <a name="discard-the-move"></a>Abandonner le déplacement 

Vous pouvez abandonner le déplacement de la manière suivante :

1. Dans **Entre régions**, sélectionnez les ressources avec l’état *Validation du déplacement en attente*, puis cliquez sur **Abandonner le déplacement**.
2. Dans **Abandonner le déplacement**, cliquez sur **Abandonner**.
3. Suivez la progression du déplacement dans la barre de notification.


> [!NOTE]
> Après l’abandon des ressources, les machines virtuelles présentent l’état *Lancement du déplacement en attente*.

## <a name="commit-the-move"></a>Valider le déplacement

Si vous voulez terminer la procédure de déplacement, validez le déplacement. 

1. Dans **Entre régions**, sélectionnez les ressources avec l’état *Validation du déplacement en attente*, puis cliquez sur **Valider le déplacement**.
2. Dans **Valider les ressources**, cliquez sur **Valider**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Page de validation des ressources permettant de finaliser le déplacement." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Suivez la progression de la validation dans la barre de notification.

> [!NOTE]
> - Après la validation du déplacement, les machines virtuelles arrêtent la réplication. La machine virtuelle source n’est pas affectée par la validation.
> - La validation n’a pas d’incidence sur les ressources réseau sources.
> - Après la validation du déplacement, les ressources présentent l’état de *Suppression de la source en attente*.



## <a name="configure-settings-after-the-move"></a>Configurer des paramètres après le déplacement

- Le service Mobilité n’est pas désinstallé automatiquement sur les machines virtuelles. Désinstallez-le manuellement ou laissez-le si vous envisagez de déplacer à nouveau le serveur.
- Modifiez les règles de contrôle d’accès en fonction du rôle (Azure RBAC) après le déplacement.

## <a name="delete-source-resources-after-commit"></a>Supprimer les ressources sources après la validation

Après le déplacement, vous pouvez, si vous le souhaitez, supprimer les ressources dans la région source. 

1. Dans **Entre régions**, sélectionnez chaque ressource source que vous souhaitez supprimer, puis sélectionnez **Supprimer la source**.
2. Dans **Supprimer la source**, passez en revue ce que vous avez l’intention de supprimer puis, dans **Confirmer la suppression**, tapez **Oui**. L’action étant irréversible, vérifiez attentivement !
3. Après avoir tapé **Oui**, sélectionnez **Supprimer la source**.

> [!NOTE]
>  Dans le portail de déplacement de ressources, vous ne pouvez pas supprimer des groupes de ressources, des coffres de clés ou des serveurs SQL Server. Vous devez les supprimer individuellement à partir de la page de propriétés de chaque ressource.


## <a name="delete-additional-resources-created-for-move"></a>Supprimer les ressources supplémentaires créées pour le déplacement

Après le déplacement, vous pouvez supprimer manuellement la collection de déplacement ainsi que les ressources Site Recovery qui ont été créées.

- La collection de déplacement est masquée par défaut. Pour la voir, vous devez activer les ressources masquées.
- Le stockage de cache est équipé d’un verrou qui doit être supprimé avant de pouvoir procéder à la suppression.

Supprimez comme suit : 
1. Localisez les ressources dans le groupe de ressources ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Vérifiez que toutes les machines virtuelles et les autres ressources sources de la région source ont été déplacées ou supprimées. Cette vérification permet de s’assurer qu’aucune ressource en attente ne les utilise.
2. Supprimer les ressources :

    - Le nom de la collection de déplacement est ```movecollection-<sourceregion>-<target-region>```.
    - Le nom du compte de stockage de cache est ```resmovecache<guid>```.
    - Le nom du coffre est ```ResourceMove-<sourceregion>-<target-region>-GUID```.
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Déplacé des machines virtuelles Azure chiffrées et leurs ressources dépendantes vers une autre région Azure.


À présent, vous allez vous intéresser au déplacement des pools élastiques et des bases de données Azure SQL vers une autre région.

> [!div class="nextstepaction"]
> [Déplacer des ressources Azure SQL](./tutorial-move-region-sql.md)
