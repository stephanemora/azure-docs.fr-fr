---
title: Empêcher la suppression accidentelle - Partages de fichiers Azure
description: En savoir plus sur la suppression réversible pour les partages de fichiers Azure et sur la façon de l’utiliser pour récupérer des données et empêcher la suppression accidentelle.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882932"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Empêcher la suppression accidentelle de partages de fichiers Azure

Le stockage Azure offre désormais une suppression réversible pour les partages de fichiers. La suppression réversible vous permet de récupérer vos données lorsqu’elles sont supprimées par erreur par une application ou un autre utilisateur du compte de stockage.

## <a name="how-soft-delete-works"></a>Fonctionnement de la suppression réversible

Quand elle est activée, la suppression réversible vous permet d’enregistrer et de récupérer vos partages de fichiers en cas de suppression. Lorsque les données sont supprimées, elle passent par un état transitoire de suppression réversible au lieu d’être supprimées définitivement. Vous pouvez configurer la durée pendant laquelle les données supprimées de manière réversible sont récupérables avant leur suppression définitive.

La suppression réversible peut être activée sur les partages de fichiers nouveaux ou existants. En outre, la suppression réversible étant rétrocompatible, il est inutile d’apporter des modifications à vos applications pour tirer parti des protections qu’offre cette fonctionnalité. 

Pour les partages de fichiers Premium supprimés de manière réversible, le quota de partage de fichiers (la taille provisionnée d’un partage de fichiers) est utilisé dans le calcul du quota total du compte de stockage jusqu’à la date d’expiration du partage supprimé de manière réversible, lorsque le partage est entièrement supprimé.

### <a name="availability"></a>Disponibilité

La suppression réversible pour les partages de fichiers Azure est disponible sur tous les niveaux de stockage, tous les types de comptes de stockage et dans chaque région où Azure Files est disponible.

## <a name="configuration-settings"></a>Paramètres de configuration

La suppression réversible pour les partages de fichiers est activée au niveau du compte de stockage, les paramètres de suppression réversible s’appliquent à tous les partages de fichiers dans un compte de stockage. Lorsque vous créez un compte de stockage, la suppression réversible est désactivée par défaut. Elle est également désactivée par défaut pour les comptes de stockage existants. Vous pouvez activer ou désactiver la suppression réversible à tout moment.

Si vous activez la suppression réversible pour les partages de fichiers, supprimez certains partages de fichiers, puis désactivez la suppression réversible, si les partages ont été enregistrés pendant cette période, vous pouvez toujours y accéder et les récupérer. Lorsque vous activez la suppression réversible, vous devez également configurer la période de rétention.

La période de rétention indique la durée pendant laquelle les partages de fichiers supprimés de manière réversible sont stockés et disponibles pour récupération. Pour les partages de fichiers supprimés explicitement, le délai la période de rétention démarre dès la suppression des données. Actuellement, vous pouvez conserver des partages de fichiers supprimés de manière réversible pendant une période de 1 à 365 jours.

Vous pouvez modifier la période de rétention de suppression réversible à tout moment. Une période de rétention mise à jour s’applique uniquement aux partages supprimés après la mise à jour de la période de rétention. Les partages supprimés avant la mise à jour de la période de rétention expireront à l’issue de la période de rétention qui était configurée lors de leur suppression.

Pour supprimer définitivement un partage de fichiers dans un état de suppression réversible avant son délai d’expiration, vous devez annuler la suppression du partage, désactiver la suppression réversible, puis supprimer à nouveau le partage. Vous devez ensuite réactiver la suppression réversible, car tous les autres partages de fichiers dans ce compte de stockage seront vulnérables à une suppression accidentelle alors que la suppression réversible est désactivée.

## <a name="pricing-and-billing"></a>Tarification et facturation

Les partages de fichiers Standard et Premium sont facturés en fonction de la capacité utilisée lorsqu’ils sont supprimés de manière réversible, plutôt qu’en fonction d’une capacité provisionnée. En outre, les partages de fichiers Premium sont facturés au taux d’instantané alors qu’ils sont dans l’état de suppression réversible. Les partages de fichiers Standard sont facturés au taux régulier alors qu’ils sont dans l’état de suppression réversible. Vous ne serez pas facturé pour des données supprimées définitivement à l’issue de la période de rétention configurée.

Pour plus d’informations sur les prix du Stockage Fichier Azure en général, consultez la [page de tarification Stockage Fichier Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Lorsque vous activez initialement la suppression réversible, nous vous recommandons d’utiliser une courte période de rétention pour mieux comprendre l’impact de cette fonctionnalité sur votre facture.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer et utiliser la suppression réversible, consultez [Activer la suppression réversible](storage-files-enable-soft-delete.md)