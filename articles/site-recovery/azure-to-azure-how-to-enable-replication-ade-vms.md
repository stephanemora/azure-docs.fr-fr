---
title: Configurer la réplication de machines virtuelles prenant en charge Azure Disk Encryption dans Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment configurer la réplication de machines virtuelles Azure Disk Encryption d’une région Azure vers une autre à l’aide d’Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: sutalasi
ms.openlocfilehash: 5d1beb124bbb857d13aecad7bf0cef493d42dac5
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043275"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Répliquer des machines virtuelles prenant en charge Azure Disk Encryption vers une autre région Azure

Cet article décrit comment activer la réplication de machines virtuelles prenant en charge Azure Disk Encryption d’une région Azure vers une autre.

>[!NOTE]
>Seules les machines virtuelles Azure exécutant Windows OS et [permettant le chiffrement avec l’application Azure AD](https://aka.ms/ade-aad-app) sont actuellement prises en charge par Azure Site Recovery.
>

## <a name="required-user-permissions"></a>Autorisations utilisateur requises

Pour activer la réplication de machines virtuelles Azure Disk Encryption à partir du portail, l’utilisateur doit disposer des autorisations ci-dessous.
- Autorisations d’accès au coffre de clés
    - Liste
    - Créer
    - Obtenir

-   Autorisations d’accès au secret du coffre de clés
    - Liste
    - Créer
    - Obtenir

- Autorisations de clé du coffre de clés (obligatoires uniquement si les machines virtuelles utilisent la clé de chiffrement à clé pour chiffrer les clés d’Azure Disk Encryption)
    - Liste
    - Obtenir
    - Créer
    - Encrypt (Chiffrer)
    - Decrypt (Déchiffrer)

Vous pouvez gérer les autorisations en accédant à la ressource de coffre de clés dans le portail, puis en ajoutant les autorisations requises à l’utilisateur.

![keyvaultpermissions](./media/azure-to-azure-how-to-enable-replication-ade-vms/keyvaultpermissions.png)

Si l’utilisateur activant la récupération d’urgence ne dispose pas des autorisations requises pour copier les clés, le script ci-dessous peut être donné à l’administrateur de la sécurité disposant des autorisations appropriées pour copier les secrets et clés de chiffrement vers la région cible.

>[!NOTE]
>Pour activer la réplication de machine virtuelle Azure Disk Encryption à partir du portail, vous devez disposer au moins des autorisations « Liste » sur les coffres de clés, les secrets et les clés
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>Copier les clés Azure Disk Encryption vers la région de récupération d’urgence à l’aide d’un script PowerShell

1. Ouvrez le code de script brut « CopyKeys » dans une fenêtre de navigateur en cliquant sur [ce lien](https://aka.ms/ade-asr-copy-keys-code).
2. Copiez le script dans un fichier que vous nommez « copie-keys.ps1 ».
2. Ouvrez l’application Windows PowerShell, puis accédez à l’emplacement du dossier dans lequel le fichier réside.
3. Lancez la commende « Copy-keys.ps1 ».
4. Fournissez les informations d’identification Azure.
5. Sélectionnez l’**abonnement Azure** de vos machines virtuelles.
6. Attendez le chargement des groupes de ressources, puis sélectionnez le **groupe de ressources** de vos machines virtuelles.
7. Sélectionnez les machines virtuelles dans la liste de machines virtuelles affichée. Seuls des machines virtuelles prenant en charge Azure Disk Encryption s’affichent dans la liste.
8. Sélectionnez l’**emplacement cible**.
9. **Coffres de clés de chiffrement de disque** : par défaut, Azure Site Recovery crée un nouveau coffre de clés dans la région cible avec un nom ayant le suffixe « asr » en fonction des clés de chiffrement du disque de la machine virtuelle source. Si le coffre de clés créé par Azure Site Recovery existe déjà, il est réutilisé. Vous pouvez sélectionner un autre coffre de clés dans la liste si nécessaire.
10. **Coffres de clés de chiffrement de clé** : par défaut, Azure Site Recovery crée un nouveau coffre de clés dans la région cible avec un nom ayant le suffixe « asr » en fonction des clés de chiffrement de la clé de la machine virtuelle source. Si le coffre de clés créé par Azure Site Recovery existe déjà, il est réutilisé. Vous pouvez sélectionner un autre coffre de clés dans la liste si nécessaire.

## <a name="enable-replication"></a>Activer la réplication

Cette procédure suppose que la région principale Azure est Asie Est et que la région secondaire est Asie Sud-Est.

1. Dans le coffre, cliquez sur **+ Répliquer**.
2. Notez les champs suivants :
    - **Source** : le point d’origine des machines virtuelles, qui dans ce cas est **Azure**.
    - **Emplacement source :** La région Azure où vous souhaitez protéger vos machines virtuelles. Dans notre exemple, l’emplacement source est « Asie Est »
    - **Modèle de déploiement :** Le modèle de déploiement Azure des machines sources.
    - **Abonnement source** : abonnement auquel vos machines virtuelles sources appartiennent. Il peut s’agir de n’importe quel abonnement au sein du même locataire Azure Active Directory où se trouve votre coffre Recovery services.
    - **Groupe de ressources :** Le groupe de ressources auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles du groupe de ressources sélectionné sont répertoriées pour la protection à l’étape suivante.

3. Dans **Machines virtuelles > Sélectionner les machines virtuelles**, cliquez pour sélectionner chaque machine virtuelle à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

4. Dans **Paramètres**, vous pouvez éventuellement configurer les paramètres du site cible :

    - **Emplacement cible :** L’emplacement où vos données de machines virtuelles sources sont répliquées. En fonction de l’emplacement des machines virtuelles sélectionné, Site Recovery vous fournit la liste des régions cibles appropriées. Nous vous recommandons de conserver le même emplacement cible que l’emplacement du coffre Recovery Services.
    - **Abonnement cible** : l’abonnement cible utilisé pour la récupération d’urgence. Par défaut, l’abonnement cible sera identique à l’abonnement source.
    - **Groupe de ressources cible :** Le groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées. Par défaut, Azure Site Recovery crée un groupe de ressources dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe de ressources créé par Azure Site Recovery existe déjà, il est réutilisé. Vous pouvez également choisir de le personnaliser, comme indiqué dans la section ci-dessous. L’emplacement du groupe de ressources cible peut être n’importe quelle région Azure à l’exception de la région dans laquelle les machines virtuelles source sont hébergées.
    - **Réseau virtuel cible :** par défaut, Site Recovery crée un réseau virtuel dans la région cible avec un nom ayant le suffixe « asr ». Il est mappé à votre réseau source et utilisé pour toute protection ultérieure. [En savoir plus](site-recovery-network-mapping-azure-to-azure.md) sur le mappage réseau.
    - **Comptes de stockage cibles (si votre machine virtuelle source n’utilise pas de disques managés)**  : par défaut, Site Recovery crée un nouveau compte de stockage cible en imitant la configuration de stockage de votre machine virtuelle source. Si le compte de stockage existe déjà, il est réutilisé.
    - **Disques managés de réplica (si votre machine virtuelle source utilise des disques managés)** : Site Recovery crée de nouveaux disques managés de réplica dans la région cible pour mettre en miroir les disques managés de la machine virtuelle source avec le même type de stockage (Standard ou Premium) que celui du disque managé de la machine virtuelle source.
    - **Comptes de stockage de cache :** Site Recovery a besoin d’un compte de stockage supplémentaire appelé « stockage de cache » dans la région source. Toutes les modifications effectuées sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache avant leur réplication vers l’emplacement cible.
    - **Groupe à haute disponibilité :** par défaut, Azure Site Recovery crée un groupe à haute disponibilité dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe à haute disponibilité créé par Azure Site Recovery existe déjà, il est réutilisé.
    - **Coffres de clés de chiffrement de disque** : par défaut, Azure Site Recovery crée un nouveau coffre de clés dans la région cible avec un nom ayant le suffixe « asr » en fonction des clés de chiffrement du disque de la machine virtuelle source. Si le coffre de clés créé par Azure Site Recovery existe déjà, il est réutilisé.
    - **Coffres de clés de chiffrement de clé** : par défaut, Azure Site Recovery crée un nouveau coffre de clés dans la région cible avec un nom ayant le suffixe « asr » en fonction des clés de chiffrement de la clé de la machine virtuelle source. Si le coffre de clés créé par Azure Site Recovery existe déjà, il est réutilisé.
    - **Stratégie de réplication :** elle définit les paramètres de l’historique de rétention des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Azure Site Recovery crée une stratégie de réplication avec les paramètres par défaut de « 24 heures » pour la rétention des points de récupération, et de « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.



## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

Vous pouvez modifier les paramètres de cible par défaut utilisés par Site Recovery.


1. Cliquez sur **Personnaliser :** à côté de « Abonnement cible » pour modifier l’abonnement cible par défaut. Sélectionnez l’abonnement dans la liste de tous ceux disponibles, dans le même locataire Azure Active Directory (AAD).

2. Cliquez sur **Personnaliser** en regard de Groupe de ressources, Stockage, Réseau et Groupes à haute disponibilité pour modifier les paramètres par défaut ci-dessous :
    - Dans **Groupe de ressources cible**, sélectionnez le groupe de ressources dans la liste de tous les groupes de ressources à l’emplacement cible de l’abonnement.
    - Dans **Réseau virtuel cible**, sélectionnez le réseau à partir d’une liste de tous les réseaux virtuels dans l’emplacement cible.
    - Dans **Groupe à haute disponibilité**, vous pouvez ajouter les paramètres de groupe à haute disponibilité de la machine virtuelle, s’ils font partie d’un groupe à haute disponibilité dans la zone source.
    - Dans **Comptes de stockage cible**, sélectionnez le compte que vous souhaitez utiliser.


2. Cliquez sur **Personnaliser** en regard de « Paramètres de chiffrement » pour modifier les paramètres par défaut ci-dessous :
    - Dans la zone **Coffre de clés pour le chiffrement de disque cible**, sélectionnez le coffre de clés pour le chiffrement de disque cible dans la liste de tous les coffres de clés figurant dans l’emplacement cible de l’abonnement.
  - Dans la zone **Coffre de clés pour le chiffrement de clé cible**, sélectionnez le coffre de clés pour le chiffrement de clé cible dans la liste de tous les coffres de clés figurant dans l’emplacement cible de l’abonnement.

3. Cliquez sur **Créer une ressource cible** > **Activer la réplication**.
4. Une fois que les machines virtuelles sont activées pour la réplication, vous pouvez vérifier leur état d’intégrité sous **Éléments répliqués**

>[!NOTE]
>Pendant la réplication initiale, l’état des peut prendre un certain temps à s’actualiser à défaut de progression. Cliquez sur le bouton **Actualiser** pour obtenir le dernier état.
>

## <a name="update-target-vm-encryption-settings"></a>Mettre à jour les paramètres de chiffrement de machine virtuelle cible
Dans les scénarios ci-dessous, vous devez mettre à jour les paramètres de chiffrement de machine virtuelle cible.
  - Vous avez activé la réplication Azure Site Recovery sur la machine virtuelle, et Azure Disk Encryption (ADE) sur la machine virtuelle source à une date ultérieure.
  - Vous avez activé la réplication Azure Site Recovery sur la machine virtuelle, et modifié la clé de chiffrement pour le disque et/ou la clé de chiffrement à clé sur la machine virtuelle source à une date ultérieure.

Vous pouvez utiliser [le script](#copy-ade-keys-to-dr-region-using-powershell-script) pour copier les clés de chiffrement vers la région cible, puis mettre à jour les paramètres de chiffrement cibles dans **Coffre Recovery Services -> Propriétés -> Calcul et Réseau.**

![update-ade-settings](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
