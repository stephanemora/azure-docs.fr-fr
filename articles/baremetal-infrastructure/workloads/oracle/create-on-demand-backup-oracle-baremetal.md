---
title: Créer une sauvegarde à la demande d’Oracle Database dans SnapCenter
description: Découvrez comment créer une sauvegarde à la demande d’Oracle Database dans SnapCenter sur l’infrastructure Oracle BareMetal.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/07/2021
ms.openlocfilehash: 699c070a3eeca2904f9620ca5f12c95c509210ab
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579153"
---
# <a name="create-on-demand-backup-of-your-oracle-database-in-snapcenter"></a>Créer une sauvegarde à la demande d’Oracle Database dans SnapCenter

Dans cet article, nous allons vous guider dans la création d’une sauvegarde à la demande d’Oracle Database dans SnapCenter. 

Une fois que vous avez [configuré SnapCenter](configure-snapcenter-oracle-baremetal.md), les sauvegardes de vos fichiers de données, fichiers de contrôle et journaux d’archivage se poursuivent en fonction de la planification que vous avez entrée lors de la création du ou des groupes de ressources. Toutefois, dans le cadre de la protection normale des bases de données, vous pouvez également être amené à effectuer des sauvegardes à la demande.

## <a name="steps-to-create-an-on-demand-backup"></a>Étapes pour créer une sauvegarde à la demande

1. Sélectionnez **Ressources** dans le menu de gauche. Ensuite, dans le menu déroulant en regard d’**Affichage**, sélectionnez **Groupe de ressources**. Sélectionnez le nom du groupe de ressources correspondant à la sauvegarde à la demande que vous souhaitez créer.

2. Sélectionnez **Sauvegarder maintenant** dans le coin supérieur droit.

3. Vérifiez que le groupe de ressources et la stratégie de protection sont corrects pour la sauvegarde à la demande. Activez la case à cocher **Vérifier après la sauvegarde** si vous souhaitez vérifier cette sauvegarde. Sélectionnez **Sauvegarder**.

Une fois la sauvegarde terminée, elle est disponible dans la liste des sauvegardes sous **Ressources**. Sélectionnez la ou les bases de données associées au groupe de ressources que vous avez sauvegardé. Cette sauvegarde sera conservée en fonction de la stratégie de rétention à la demande que vous définissez lors de la création de la stratégie de protection.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment restaurer Oracle Database dans SnapCenter :

> [!div class="nextstepaction"]
> [Restaurer la base de données Oracle](restore-oracle-database-baremetal.md)
