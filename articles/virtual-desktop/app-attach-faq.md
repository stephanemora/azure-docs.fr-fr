---
title: FAS sur l’attachement d’application MSIX Windows Virtual Desktop – Azure
description: Forum aux questions sur l’attachement d’application MSIX pour Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a1a2464735ff8039bb31c43d6956cbf03e694c63
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448404"
---
# <a name="msix-app-attach-faq"></a>FAS sur l’attachement d’application MSIX

Cet article répond aux questions fréquemment posées sur l’attachement d’application MSIX pour Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Quelle est la différence entre MSIX et l’attachement d’application MSIX ?

MSIX est un format d’empaquetage pour les applications, tandis que l’attachement d’application MSIX est la fonctionnalité qui fournit des packages MSIX à votre déploiement.

## <a name="does-msix-app-attach-use-fslogix"></a>L’attachement d’application MSIX utilise-t-il FSLogix ?

L’attachement d’application MSIX n’utilise pas FSLogix. En revanche, l’attachement d’application MSIX et FSLogix sont conçus pour fonctionner ensemble afin de fournir une expérience utilisateur transparente.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Puis-je utiliser l’attachement d’application MSIX en dehors de Windows Virtual Desktop ?

Les API sur lesquelles repose l’attachement d’application MSIX sont disponibles pour Windows 10 Entreprise. Ces API peuvent être utilisées en dehors de Windows Virtual Desktop. Toutefois, il n’existe aucun plan de gestion pour l’attachement d’application MSIX en dehors de Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Comment obtenir un package MSIX ?

Votre fournisseur de logiciels vous donnera un package MSIX. Vous pouvez également convertir des packages non MSIX en MSIX. Pour plus d’informations, consultez [Comment migrer vos programmes d’installation existants vers MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Quels systèmes d’exploitation prennent en charge l’attachement d’application MSIX ?

Windows 10 Entreprise et Windows 10 Entreprise multisession, version 2004 ou ultérieure.

## <a name="is-msix-app-attach-currently-generally-available"></a>L’attachement d’application MSIX est-il mis à la disposition générale ?

L’attachement d’application MSIX fait partie de Windows 10 Entreprise et Windows 10 Entreprise multisession, version 2004 ou ultérieure. Les deux systèmes d’exploitation sont actuellement mis à la disposition générale. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Puis-je utiliser l’attachement d’application MSIX en dehors de Windows Virtual Desktop ?

MSIX et l’attachement d’application MSIX font partie de Windows 10 Entreprise et Windows 10 Entreprise multisession, version 2004 et ultérieures. Actuellement, nous ne proposons aucun logiciel de gestion pour l’attachement d’application MSIX en dehors de Windows Virtual Desktop.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Puis-je exécuter simultanément deux versions de la même application ?

Pour que deux versions des mêmes applications MSIX s’exécutent simultanément, la famille de packages MSIX définie dans le fichier appxmanifest.xml doit être différente pour chaque application.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Dois-je désactiver la mise à jour automatique lorsque j’utilise l’attachement d’application MSIX ?

Oui. L’attachement d’application MSIX ne prend pas en charge la mise à jour automatique des applications MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Comment fonctionnent les autorisations avec l’attachement d’application MSIX ?

Toutes les machines virtuelles d’un pool hôte qui utilise l’attachement d’application MSIX doivent avoir des autorisations de lecture sur le partage de fichiers où sont stockées les images MSIX. Si le pool utilise également Azure Files, il doit disposer à la fois du contrôle d’accès en fonction du rôle (RBAC) et des autorisations New Technology File System (NTFS).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Puis-je utiliser l’attachement d’application MSIX pour HTTP ou HTTPs ?

L’utilisation de l’attachement d’application MSIX sur HTTP ou HTTPs n’est actuellement pas prise en charge.

## <a name="can-i-restage-the-same-msix-application"></a>Puis-je effectuer une copie intermédiaire de la même application MSIX ?

Oui. Vous pouvez effectuer une nouvelle copie intermédiaire d’applications, ce qui ne devrait pas provoquer d’erreur.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>L’attachement d’application MSIX prend-il en charge les certificats auto-signés ?

Oui. Vous devez installer le certificat auto-signé sur toutes les machines virtuelles hôtes de session où l’attachement d’application MSIX est utilisé pour héberger l’application auto-signée.


## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur l’attachement d’application MSIX, consultez notre [page de présentation](what-is-app-attach.md) et notre [glossaire](app-attach-glossary.md). Sinon, commencez avec [Configurer l’attachement d’application](app-attach.md).
