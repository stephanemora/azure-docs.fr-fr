---
title: Configurer la réplication pour les machines virtuelles compatibles avec Azure Disk Encryption dans Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment configurer la réplication pour les machines virtuelles compatibles avec Azure Disk Encryption d’une région Azure vers un autre à l’aide de Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678953"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Répliquer des ordinateurs virtuels activés pour Azure Disk Encryption sur une autre région Azure

Cet article explique comment répliquer des machines virtuelles Azure Disk Encryption d’une région Azure vers un autre.

>[!NOTE]
>Azure Site Recovery prend actuellement en charge uniquement les machines virtuelles Azure qui exécutent un système d’exploitation Windows et qui sont [activé pour le chiffrement avec Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Autorisations utilisateur requises
Site Recovery a besoin l’utilisateur dispose des autorisations nécessaires pour créer le coffre de clés dans les clés de copie et de la région cible vers la région.

Pour activer la réplication de machines virtuelles chiffrement de disque à partir du portail Azure, l’utilisateur nécessite les autorisations suivantes :

- Autorisations d’accès au coffre de clés
    - Liste
    - Créer
    - Obtenir

-   Autorisations d’accès au secret du coffre de clés
    - Liste
    - Créer
    - Obtenir

- Autorisations de clé de coffre de clés (obligatoire uniquement si les machines virtuelles utilisent la clé de chiffrement à clé pour chiffrer les clés de chiffrement de disque)
    - Liste
    - Obtenir
    - Créer
    - Encrypt (Chiffrer)
    - Decrypt (Déchiffrer)

Pour gérer les autorisations, accédez à la ressource de coffre de clés dans le portail. Ajouter les autorisations nécessaires pour l’utilisateur. L’exemple suivant montre comment activer les autorisations au coffre de clés *ContosoWeb2Keyvault*, qui se trouve dans la région source.

1. Accédez à **accueil** > **Keyvaults** > **ContosoWeb2KeyVault > stratégies d’accès**.

   ![Fenêtre des autorisations de coffre de clés](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Vous pouvez voir qu’il n’y a aucune autorisation d’utilisateur. Sélectionnez **Ajouter**. Entrez les informations utilisateur et les autorisations.

   ![autorisations de coffre de clés](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Si l’utilisateur qui est l’activation de la récupération d’urgence (DR) ne dispose des autorisations nécessaires pour copier les clés, un administrateur de sécurité qui dispose des autorisations appropriées peut utiliser le script suivant pour copier les clés secrètes de chiffrement et les clés à la région cible.

Pour résoudre les problèmes d’autorisations, reportez-vous à [problèmes d’autorisation de coffre de clé](#trusted-root-certificates-error-code-151066) plus loin dans cet article.

>[!NOTE]
>Pour activer la réplication de machines virtuelles chiffrement de disque à partir du portail, vous devez au moins « List » des autorisations sur les coffres de clés, secrets et des clés.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copier les clés de chiffrement de disque vers la région de récupération d’urgence en utilisant le script PowerShell

1. [Ouvrez le code de script brutes « CopyKeys »](https://aka.ms/ade-asr-copy-keys-code).
2. Copiez le script dans un fichier et nommez-le **keys.ps1 de copie**.
3. Ouvrez l’application de Windows PowerShell et accédez au dossier où vous avez enregistré le fichier.
4. Exécutez keys.ps1 de copie.
5. Fournir des informations d’identification Azure pour vous connecter.
6. Sélectionnez l’**abonnement Azure** de vos machines virtuelles.
7. Attendez que les groupes de ressources à charger, puis sélectionnez le **groupe de ressources** de vos machines virtuelles.
8. Sélectionnez les machines virtuelles à partir de la liste qui s’affiche. Seules les machines virtuelles qui sont activés pour le chiffrement de disque figurent dans la liste.
9. Sélectionnez le **emplacement cible**.

    - **Coffres de clés de chiffrement de disque**
    - **Coffres de clés de chiffrement à clé**

   Par défaut, Site Recovery crée un coffre de clés dans la région cible. Le nom du coffre a un suffixe « asr » qui est basé sur les clés de chiffrement de disque de machine virtuelle source. Si un coffre de clés existe déjà un qui a été créé par Site Recovery, il est réutilisé. Sélectionnez un autre coffre de clés à partir de la liste si nécessaire.

## <a name="enable-replication"></a>Activer la réplication

Pour cet exemple, la région principale Azure est Asie et la région secondaire est Asie du Sud-est.

1. Dans le coffre, sélectionnez **+ répliquer**.
2. Notez les champs suivants.
    - **Source** : point d’origine des machines virtuelles, qui dans ce cas est **Azure**.
    - **Emplacement source** : La région Azure où vous souhaitez protéger vos machines virtuelles. Pour cet exemple, l’emplacement source est « East Asia ».
    - **Modèle de déploiement** : Le modèle de déploiement Azure des machines sources.
    - **Abonnement source** : abonnement auquel vos machines virtuelles sources appartiennent. Il peut être n’importe quel abonnement qui se trouve dans le même locataire Azure Active Directory en tant que votre coffre recovery services.
    - **Groupe de ressources** : groupe de ressources auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles dans le groupe de ressources sélectionné sont répertoriées pour la protection à l’étape suivante.

3. Dans **Machines virtuelles** > **sélectionner les machines virtuelles**, sélectionnez chaque machine virtuelle que vous souhaitez répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Ensuite, sélectionnez **OK**.

4. Dans **paramètres**, vous pouvez configurer les paramètres de site de la cible suivante.

    - **Emplacement cible** : Emplacement où vos données de machines virtuelles sources sont répliquées. Site Recovery fournit une liste des régions cibles appropriées selon l’emplacement de l’ordinateur sélectionné. Nous vous recommandons d’utiliser le même emplacement comme emplacement de l’archivage Recovery Services.
    - **Abonnement cible** : L’abonnement cible qui est utilisé pour la récupération d’urgence. Par défaut, l’abonnement cible est identique à l’abonnement source.
    - **Groupe de ressources cible** : groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées. Par défaut, Site Recovery crée un nouveau groupe de ressources dans la région cible. Le nom Obtient le suffixe « asr ». Si un groupe de ressources existant qui a été créé par Azure Site Recovery, il est réutilisé. Vous pouvez également choisir de le personnaliser, comme indiqué dans la section suivante. L’emplacement du groupe de ressources cible peut être n’importe quelle région Azure à l’exception de la région où se trouvent les machines virtuelles sources.
    - **Réseau virtuel cible** : Par défaut, Site Recovery crée un nouveau réseau virtuel dans la région cible. Le nom Obtient le suffixe « asr ». Il a mappé à votre réseau source et utilisé pour toute protection ultérieure. [En savoir plus](site-recovery-network-mapping-azure-to-azure.md) sur le mappage réseau.
    - **Comptes de stockage cibles (si votre machine virtuelle source n’utilise des disques managés)**: Par défaut, Site Recovery crée un nouveau compte de stockage cible en simulant votre configuration de stockage de machine virtuelle source. Si un compte de stockage existe déjà, il est réutilisé.
    - **Disques managés de réplica (si votre machine virtuelle source utilise des disques gérés)**: Site Recovery crée des nouveaux disques managés de réplica dans la région cible pour mettre en miroir des disques gérés utilisant la source de la machine virtuelle le même type de stockage (standard ou premium) en tant que disques managés de la source de la machine virtuelle.
    - **Comptes de stockage de cache** : Site Recovery a besoin d’un compte de stockage supplémentaire appelé *stockage de cache* dans la région source. Toutes les modifications sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache. Elles sont ensuite répliquées vers l’emplacement cible.
    - **Groupe à haute disponibilité** : Par défaut, Site Recovery crée un groupe à haute disponibilité dans la région cible. Le nom comporte le suffixe « asr ». Si un groupe à haute disponibilité qui a été créé par Site Recovery déjà existe, il est réutilisé.
    - **Coffres de clés de chiffrement de disque** : Par défaut, Site Recovery crée un coffre de clés dans la région cible. Il a un suffixe « asr » qui est basé sur les clés de chiffrement de disque de machine virtuelle source. Si un coffre de clés a été créé par Azure Site Recovery déjà existe, il est réutilisé.
    - **Coffres de clés de chiffrement de clé** : Par défaut, Site Recovery crée un coffre de clés dans la région cible. Le nom comporte un suffixe « asr » qui est basé sur les clés de chiffrement à clé de machine virtuelle source. Si un coffre de clés créé par Azure Site Recovery déjà existe, il est réutilisé.
    - **Stratégie de réplication** : Définit les paramètres de l’historique de rétention de point de récupération et la fréquence des captures instantanées cohérentes de l’application. Par défaut, Site Recovery crée une stratégie de réplication avec les paramètres par défaut de *24 heures* pour la récupération de points de rétention et *60 minutes* pour la fréquence des captures instantanées cohérentes de l’application.

## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

Suivez ces étapes pour modifier les paramètres de cible de récupération de Site par défaut.

1. Sélectionnez **personnaliser** en regard de « Abonnement cible » pour modifier l’abonnement cible par défaut. Sélectionnez l’abonnement dans la liste des abonnements qui sont disponibles dans le locataire Azure AD.

2. Sélectionnez **personnaliser** regard « groupe de ressources, réseau, stockage et la disponibilité de jeux » pour modifier les paramètres par défaut suivants :
    - Pour **groupe de ressources cible**, sélectionnez le groupe de ressources dans la liste des groupes de ressources à l’emplacement cible de l’abonnement.
    - Pour **réseau virtuel cible**, sélectionnez le réseau à partir d’une liste de réseaux virtuels dans l’emplacement cible.
    - Pour **à haute disponibilité**, vous pouvez ajouter des paramètres de groupe à haute disponibilité à la machine virtuelle, s’ils faisaient partie d’une haute disponibilité dans la région source.
    - Pour **comptes de stockage cible**, sélectionnez le compte à utiliser.

2. Sélectionnez **personnaliser** en regard de « Paramètres de chiffrement » pour modifier les paramètres par défaut suivants :
   - Pour **coffre de clés de chiffrement cible disque**, sélectionnez le coffre de clés chiffrement de disque cible dans la liste des coffres de clés dans l’emplacement cible de l’abonnement.
   - Pour **coffre de clés de chiffrement à clé cible**, sélectionnez le coffre de clés de chiffrement à clé cible dans la liste des coffres de clés dans l’emplacement cible de l’abonnement.

3. Sélectionnez **créer une ressource cible** > **activer la réplication**.
4. Une fois que les machines virtuelles sont activés pour la réplication, vous pouvez vérifier l’état d’intégrité des machines virtuelles sous **éléments répliqués**.

>[!NOTE]
>Pendant la réplication initiale, l’état peut prendre un certain temps à actualiser, sans apparente progression. Cliquez sur **Actualiser** pour obtenir le dernier état.

## <a name="update-target-vm-encryption-settings"></a>Mettre à jour les paramètres de chiffrement de machine virtuelle cible
Dans les scénarios suivants, vous devrez mettre à jour les paramètres de chiffrement de machine virtuelle cible :
  - Vous avez activé la réplication Site Recovery sur la machine virtuelle. Plus tard, vous avez activé le chiffrement de disque sur la machine virtuelle source.
  - Vous avez activé la réplication Site Recovery sur la machine virtuelle. Plus tard, vous avez modifié la clé de chiffrement de disque ou une clé de chiffrement de clé sur la machine virtuelle source.

Vous pouvez utiliser [un script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) pour copier les clés de chiffrement dans la région cible et puis mettez à jour les paramètres de chiffrement cible dans **coffre Recovery services** > *élémentrépliqué*  >  **Propriétés** > **calcul et réseau**.

![Boîte de dialogue Paramètres de mise à jour ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Résoudre les problèmes d’autorisation de coffre de clés lors de la réplication de machine virtuelle Azure vers Azure

**Cause 1 :** Vous avez peut-être sélectionné à partir de la région cible un coffre de clés déjà créé qui n’a pas les autorisations requises au lieu de laisser Site Recovery en créer un. Assurez-vous que le coffre de clés a le nécessitent des autorisations, comme décrit précédemment.

*Par exemple* : Vous essayez de répliquer une machine virtuelle avec le coffre de clés *ContososourceKeyvault* sur une région source.
Vous avez toutes les autorisations sur le coffre de clés de la région source. Mais lors de la protection, vous sélectionnez le coffre clé déjà créé ContosotargetKeyvault, ce qui n’a pas les autorisations. Une erreur se produit.

**Procédure de résolution :** Accédez à **accueil** > **Keyvaults** > **ContososourceKeyvault** > **stratégies d’accès** et ajoutez les autorisations appropriées.

**Cause 2 :** Vous avez sélectionné à partir de la région cible un coffre de clés déjà créé n’ayant pas déchiffrer-chiffrer les autorisations au lieu de laisser Site Recovery en créer un. Assurez-vous que vous avez decrypt-chiffrer les autorisations si vous chiffrez également la clé sur la région source.</br>

*Par exemple* : Vous essayez de répliquer une machine virtuelle avec un coffre de clés *ContososourceKeyvault* sur la région source. Vous avez toutes les autorisations nécessaires sur le coffre de clés de la région source. Mais lors de la protection, vous sélectionnez le coffre clé déjà créé ContosotargetKeyvault, ce qui n’a pas les autorisations pour déchiffrer et chiffrer. Une erreur se produit.</br>

**Procédure de résolution :** Accédez à **accueil** > **Keyvaults** > **ContososourceKeyvault** > **stratégies d’accès**. Ajouter des autorisations sous **autorisations de clé** > **opérations de chiffrement**.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
