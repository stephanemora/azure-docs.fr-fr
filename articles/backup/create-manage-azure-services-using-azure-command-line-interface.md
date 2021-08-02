---
title: Créer et gérer des services Azure avec l’interface Azure CLI
description: Utilisez l’interface Azure CLI pour créer et gérer des services Azure pour Sauvegarde Azure.
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 3581be20082d1c579caad4cf82ce6b2408f01a92
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482653"
---
# <a name="create-and-manage-azure-backup-services-using-azure-command-line-interface-azure-cli"></a>Créer et gérer des services de Sauvegarde Azure à l’aide de l’interface de ligne de commande Azure (Azure CLI)

L’interface de ligne de commande Azure (Azure CLI) est un ensemble de commandes qui sert à créer et à gérer des ressources Azure. L’interface Azure CLI vous aide pour l’automatisation et l’utilisation de la Sauvegarde Azure.

Cet article fournit des informations sur les services qui prennent en charge l’interface Azure CLI pour la Sauvegarde Azure et les liens correspondants vers ces articles.

## <a name="document-references-for-cli-supported-azure-services"></a>Références de documents pour les services Azure pris en charge par l’interface CLI

Le tableau suivant répertorie les références de documents concernant Azure CLI disponibles pour les services Azure pris en charge.

Services Azure | Références de documents CLI
-------------------------- | ---------------------------------
Coffre Azure | [Supprimer un coffre Recovery Services](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-cli)
Machine virtuelle Azure | <li>[Sauvegarder une machine virtuelle Azure](quick-backup-vm-cli.md)</li><li>[Restaurer une machine virtuelle Azure](tutorial-restore-disk.md)</li><li>[Restaurer des fichiers à partir des sauvegardes de machines virtuelles Azure](tutorial-restore-files.md)</li><li>[Mettre à jour la stratégie existante de sauvegarde des machines virtuelles](modify-vm-policy-cli.md)</li><li>[Sauvegarde et restauration sélectives de disques pour les machines virtuelles Azure](selective-disk-backup-restore.md#using-azure-cli)</li>
Partage de fichiers Azure | <li>[Sauvegarder des partages de fichiers Azure](backup-afs-cli.md)</li><li>[Restaurer des partages de fichiers Azure](restore-afs-cli.md)</li><li>[Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup-cli.md)</li>
SAP HANA | <li>[Sauvegarder des bases de données SAP HANA dans une machine virtuelle Azure](tutorial-sap-hana-backup-cli.md)</li><li>[Restaurer des bases de données SAP HANA dans une machine virtuelle Azure](tutorial-sap-hana-restore-cli.md)</li><li>[Gérer des bases de données SAP HANA dans une machine virtuelle Azure](tutorial-sap-hana-manage-cli.md)</li>

