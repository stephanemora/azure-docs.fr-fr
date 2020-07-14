---
title: Matrice de prise en charge de la sauvegarde de partage de fichiers Azure
description: Fournit un récapitulatif des limitations et des paramètres de prise en charge lors de la sauvegarde de partages de fichiers Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 5670f4702a8e8d199b9762d87793a053cf6e53b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806869"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matrice de prise en charge de la sauvegarde de partage de fichiers Azure

Vous pouvez utiliser le [service Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview) pour sauvegarder des partages de fichiers Azure. Cet article récapitule les paramètres de prise en charge lorsque vous sauvegardez des partages de fichiers Azure avec Sauvegarde Azure.

## <a name="supported-regions"></a>Régions prises en charge

### <a name="ga-regions-for-azure-file-shares-backup"></a>Régions GA pour la sauvegarde des partages de fichiers Azure

La sauvegarde des partages de fichiers Azure est disponible dans toutes les régions **sauf** : Allemagne Centre (souverain), Allemagne Nord-Est (souverain), Chine Est, Chine Est 2, Chine Nord, Chine Nord 2, US Gov Iowa

### <a name="supported-regions-for-accidental-delete-protection"></a>Régions prises en charge pour la protection contre la suppression accidentelle

USA Centre-Ouest, Australie Sud-Est, Canada Centre

## <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

| Détails du compte de stockage | Support                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Type de compte            | Sauvegarde Azure prend en charge les partages de fichiers Azure présents dans les comptes de stockage de type à usage général v1, à usage général v2 et stockage de fichiers |
| Performances              | Sauvegarde Azure prend en charge les partages de fichiers dans des comptes de stockage Standard et Premium |
| Réplication              | Les partages de fichiers Azure dans des comptes de stockage avec tout type de réplication sont pris en charge |
| Pare-feu activé         | Les partages de fichiers Azure dans des comptes de stockage avec des règles de pare-feu qui permettent aux services Microsoft Azure d’accéder au compte de stockage sont pris en charge|

## <a name="supported-file-shares"></a>Partages de fichiers pris en charge

| Type de partage de fichiers                                   | Support   |
| -------------------------------------------------- | --------- |
| Standard                                           | Prise en charge |
| grand                                              | Prise en charge |
| Premium                                            | Prise en charge |
| Partages de fichiers connectés au service Azure File Sync | Prise en charge |

## <a name="protection-limits"></a>Limites relatives à la protection

| Paramètre                                                      | Limite |
| ------------------------------------------------------------ | ----- |
| Nombre maximal de partages de fichiers pouvant être protégés par coffre par jour| 200   |
| Nombre maximal de comptes de stockage pouvant être inscrits par coffre par jour | 50    |
| Nombre maximal de partages de fichiers pouvant être protégés par coffre | 2000   |
| Nombre maximal de comptes de stockage pouvant être inscrits par coffre | 200   |

## <a name="backup-limits"></a>Limites Azure Backup

| Paramètre                                      | Limite |
| -------------------------------------------- | ----- |
| Nombre maximal de sauvegardes à la demande par jour | 10   |
| Nombre maximal de sauvegardes planifiées par jour | 1     |

## <a name="restore-limits"></a>Limites de restauration

| Paramètre                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Nombre maximal de restaurations par jour                           | 10      |
| Nombre maximal de fichiers par restauration                         | 10      |
| Taille de restauration maximale recommandée par restauration pour des partages de fichiers volumineux | 15 Tio |

## <a name="retention-limits"></a>Limites de rétention

| Paramètre                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Nombre total maximal de points de récupération par partage de fichiers à tout moment | 200      |
| Rétention maximale de point de récupération créé par une sauvegarde à la demande | 10 ans |
| Rétention maximale de points de récupération quotidiens (instantanés) par partage de fichiers| 200 jours |
| Rétention maximale de points de récupération hebdomadaires (instantanés) par partage de fichiers | 200 semaines |
| Rétention maximale de points de récupération mensuels (instantanés) par partage de fichiers | 120 mois |
| Rétention maximale de points de récupération annuels (instantanés) par partage de fichiers | 10 ans |

## <a name="supported-restore-methods"></a>Méthodes de restauration prises en charge

| Méthode de restauration     | Détails                                                      |
| ------------------ | ------------------------------------------------------------ |
| Restauration de partage complet | Vous pouvez restaurer le partage de fichiers complet à l’emplacement d’origine ou à un autre emplacement |
| Restauration au niveau élément | Vous pouvez restaurer des fichiers et dossiers individuels à l’emplacement d’origine ou à un autre emplacement |

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [sauvegarder des partages de fichiers Azure](backup-afs.md)
* Découvrir comment [Restaurer des partages de fichiers Azure](restore-afs.md)
* Découvrir comment [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md)
