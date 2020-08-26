---
title: FAS sur l’attachement d’application MSIX Windows Virtual Desktop – Azure
description: Forum aux questions sur l’attachement d’application MSIX pour Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556134"
---
# <a name="msix-app-attach-faq"></a>FAS sur l’attachement d’application MSIX

Cet article répond aux questions fréquemment posées sur l’attachement d’application MSIX pour Windows Virtual Desktop.

## <a name="does-msix-app-attach-use-fslogix"></a>L’attachement d’application MSIX utilise-t-il FSLogix ?

L’attachement d’application MSIX n’utilise pas FSLogix. En revanche, l’attachement d’application et FSLogix sont conçus pour fonctionner ensemble afin de fournir une expérience utilisateur transparente.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Puis-je utiliser l’attachement d’application MSIX en dehors de Windows Virtual Desktop ?

Oui, l’attachement d’application MSIX est une fonctionnalité incluse dans Windows 10 Entreprise, qui peut être utilisée en dehors de Windows Virtual Desktop. Toutefois, il n’existe aucun plan de gestion pour l’attachement d’application MSIX en dehors de Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Comment obtenir un package MSIX ?

Votre fournisseur de logiciels vous donnera un package MSIX. Vous pouvez également convertir des packages non MSIX en MSIX. Pour plus d’informations, consultez [Comment migrer vos programmes d’installation existants vers MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Quels systèmes d’exploitation prennent en charge l’attachement d’application MSIX ?

Windows 10 Entreprise et Windows 10 Entreprise multisession

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur l’attachement d’application MSIX, consultez notre [glossaire](app-attach-glossary.md)et notre [page de présentation](what-is-app-attach.md). Sinon, commencez à [Configurer un attachement d’application](app-attach.md).