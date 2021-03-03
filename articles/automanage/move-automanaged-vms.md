---
title: Déplacement d’une machine virtuelle Azure Automanage d’une région à l’autre
description: Découvrez comment déplacer une machine virtuelle autogérée d’une région à l’autre.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650463"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Déplacement d’une machine virtuelle Azure Automanage dans une autre région
Cet article explique comment maintenir Automanage activé sur une machine virtuelle lorsque vous la déplacez dans une autre région. On peut être amené à déplacer ses machines virtuelles dans une autre région pour différentes raisons. Par exemple, pour tirer parti d’une nouvelle région Azure, pour répondre à des exigences de stratégie et de gouvernance internes ou pour respecter des exigences de planification de la capacité. Il peut arriver que les machines virtuelles déplacées soient actuellement autogérées et qu’elles doivent le rester à l’issue du déplacement.

## <a name="prerequisites"></a>Prérequis
* Vérifiez que votre région cible est [prise en charge par Automanage](./automanage-virtual-machines.md#prerequisites).
* Vérifiez que la région de votre espace de travail Log Analytics, celle de votre compte Automation et votre région cible sont toutes prises en charge par les mappages de régions disponibles [ici](../automation/how-to/region-mappings.md).

## <a name="prepare-your-automanaged-vms-for-moving"></a>Préparation au déplacement de machines virtuelles autogérées
Désactivez Automanage sur vos machines virtuelles autogérées. Pour cela, sélectionnez-les dans le panneau Automanage, puis cliquez sur **Désactiver l’autogestion Automanage** dans le panneau Automanage.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Déplacement de machines virtuelles autogérées et réactivation d’Automanage
Pour plus d’informations sur le déplacement de machines virtuelles, consultez cet [article](../resource-mover/tutorial-move-region-virtual-machines.md).

Une fois que vous avez déplacé vos machines virtuelles d’une région à l’autre, vous pouvez y réactiver Automanage. Pour plus d’informations, cliquez [ici](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Azure Automanage](./automanage-virtual-machines.md)
* [Consulter la FAQ sur Azure Automanage](./faq.md)