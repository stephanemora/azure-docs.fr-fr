---
title: Prise en charge de la migration Hyper-V dans Azure Migrate
description: Découvrez la prise en charge de la migration Hyper-V avec Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225421"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matrice de prise en charge pour la migration Hyper-V

Cet article résume les paramètres et les limites de la migration de machines virtuelles Hyper-V avec [Azure Migrate : Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool). Si vous recherchez des informations sur l’évaluation des machines virtuelles Hyper-V pour la migration vers Azure, consultez la [matrice de prise en charge de l’évaluation](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limites de la migration

Vous pouvez sélectionner jusqu’à 10 machines virtuelles à la fois pour la réplication. Si vous souhaitez migrer davantage de machines, répliquez-les en groupes de 10.


## <a name="hyper-v-hosts"></a>Hôtes Hyper-V

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement**       | L'hôte Hyper-V peut être autonome ou déployé dans un cluster. <br/>Le logiciel de réplication Azure Migrate (fournisseur de réplication Hyper-V) doit être installé sur les hôtes Hyper-V.|
| **autorisations**           | Vous avez besoin des droits d'administrateur sur l'hôte Hyper-V. |
| **Système d'exploitation hôte** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |
| **Accès URL** | Le logiciel du fournisseur de réplication sur les hôtes Hyper-V doit accéder à ces URL :<br/><br/> – login.microsoftonline.com: Contrôle d’accès et gestion des identités avec Active Directory.<br/><br/> – *.backup.windowsazure.com: Transfert des données de réplication et coordination. URL du service de migration.<br/><br/> – *.blob.core.windows.net: Chargez des données dans des comptes de stockage.<br/><br/> – dc.services.visualstudio.com: Chargez les journaux d’applications utilisés pour la supervision interne.<br/><br/> - time.windows.com : Vérifie la synchronisation horaire entre l’horloge système et l’heure globale.
| **Accès au port** |  Connexions sortantes sur le port HTTPS 443 pour envoyer les données de réplication des machines virtuelles.

## <a name="hyper-v-vms"></a>Machines virtuelles Hyper-V

| **Support**                  | **Détails**               
| :----------------------------- | :------------------- |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure. |
| **Modifications nécessaires pour Azure** | Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Vous devez effectuer les ajustements manuellement avant la migration. Les articles pertinents contiennent des instructions sur la façon de procéder. |
| **Démarrage Linux**                 | Si /boot se trouve sur une partition dédiée, il doit être le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas s’étendre sur d’autres disques. |
| **Démarrage UEFI**                  | La machine virtuelle migrée dans Azure est automatiquement convertie en machine virtuelle de démarrage du BIOS. La machine virtuelle doit exécuter Windows Server 2012 ou une version ultérieure uniquement. Le disque du système d’exploitation ne doit pas comporter plus de cinq partitions et la taille du disque du système d’exploitation doit être inférieure à 300 Go.
  |
| **Taille du disque**                  | 2 To pour le disque du système d’exploitation ; 4 To pour les disques de données.
| **Nombre de disques** | Un maximum de 16 disques par machine virtuelle.
| **Disques/volumes chiffrés**    | Non pris en charge pour la migration. |
| **RDM/disques directs**      | Non pris en charge pour la migration. |
| **Disque partagé** | Les machines virtuelles qui utilisent des disques partagés ne sont pas prises en charge pour la migration.
| **NFS**                        | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués. |
| **ISCSI**                      | Les machines virtuelles avec des cibles iSCSI ne sont pas prises en charge pour la migration.
| **Disque cible**                | Vous pouvez uniquement migrer des machines virtuelles Azure avec des disques managés. |
| **IPv6** | Non pris en charge.
| **Association de cartes réseau** | Non pris en charge.
| **Azure Site Recovery** | Vous ne pouvez pas répliquer en utilisant Azure Migrate Server Migration si la machine virtuelle est configurée pour la réplication avec Azure Site Recovery.
| **Ports** | Connexions sortantes sur le port HTTPS 443 pour envoyer les données de réplication des machines virtuelles.

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Toutes les machines virtuelles locales répliquées sur Azure doivent respecter les exigences des machines virtuelles Azure récapitulées dans ce tableau.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go. | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 16 ou moins. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille de disque de données | Jusqu’à 4 095 Go | La vérification est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
BitLocker | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine.
nom de la machine virtuelle | De 1 et 63 caractères.<br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. |  Mettez à jour la valeur dans les propriétés de machine de Site Recovery.
Se connecter après la migration - Windows | Pour vous connecter à des machines virtuelles Azure exécutant Windows après la migration :<br/> - Avant la migration, active le protocole RDP sur la machine virtuelle locale. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.<br/> Pour l’accès VPN site à site, activez RDP et autorisez RDP dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**. Vérifiez aussi que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](prepare-for-migration.md) |
Se connecter après la migration - Linux | Pour vous connecter à des machines virtuelles Azure après la migration avec SSH :<br/> Avant la migration, sur la machine locale, vérifiez que le service Secure Shell est défini sur Démarrer et que les règles de pare-feu autorisent une connexion SSH.<br/> Après le basculement, sur la machine virtuelle Azure, autorisez les connexions entrantes au port SSH pour les règles du groupe de sécurité réseau sur la machine virtuelle basculée, et pour le sous-réseau Azure auquel elle est connectée. Ajoutez aussi une adresse IP publique pour la machine virtuelle. |  

## <a name="next-steps"></a>Étapes suivantes

[Migrer des machines virtuelles Hyper-V](tutorial-migrate-hyper-v.md) pour la migration.
