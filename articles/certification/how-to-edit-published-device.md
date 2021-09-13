---
title: Comment modifier votre certification Azure Certified Device publiée
description: Guide vous permettant de modifier les informations de votre appareil une fois que vous avez certifié et publié votre appareil par l’intermédiaire du programme Azure Certified Device.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 07/13/2021
ms.custom: template-how-to
ms.openlocfilehash: 82993fb11ad6f2d1fde3d997494e5fc9151673e5
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731609"
---
# <a name="edit-your-published-device"></a>Modifier votre appareil publié

Une fois votre appareil certifié et publié sur le catalogue Azure Certified Device, vous devrez peut-être mettre à jour les détails de votre appareil. Cela peut être dû à une mise à jour de votre liste de distributeurs, à des modifications apportées à des URL de page d’achat ou à des mises à jour des spécifications matérielles (telles que la version du système d’exploitation ou l’ajout d’un nouveau composant). Vous devrez peut-être également mettre à jour votre modèle d’appareil IoT Plug-and-Play par rapport à celui chargé à l’origine vers le référentiel de modèles.


## <a name="prerequisites"></a>Prérequis

- Vous devez être connecté et disposer d’un projet **approuvé** pour votre appareil sur le [portail Azure Certified Device](https://certify.azure.com). Si vous n’avez pas d’appareil certifié, vous pouvez consulter ce [tutoriel](tutorial-01-creating-your-project.md) pour commencer.


## <a name="editing-your-published-project-information"></a>Modification des informations de votre projet publié

Dans le résumé du projet, vous remarquerez que votre projet est en mode lecture seule, car il a déjà été examiné et accepté. Pour apporter des modifications, vous devrez demander une modification de votre projet et faire approuver à nouveau la mise à jour par l’équipe de certification Azure.

1. Cliquez sur le bouton `Request Metadata Edit` en haut de la page.  

    ![Demander la mise à jour de métadonnées](./media/images/request-metadata-edit.png)

1. Accusez réception de la notification sur la page indiquant que vous devrez soumettre votre produit pour examen après la modification.
    > [!NOTE]
    > En confirmant cette modification, vous ne supprimez **pas** votre appareil du catalogue Azure Certified Device s’il a déjà été publié. La version précédente de votre produit restera dans le catalogue jusqu’à ce que vous ayez republié votre appareil.
    > Par ailleurs, vous n’aurez pas à répéter la section Connecter et tester du portail.

1. Après avoir accusé réception de cet avertissement, vous pouvez modifier les détails de votre appareil. Veillez à laisser une note dans la section `Comments for Reviewer` de `Device Details` pour indiquer ce qui a été modifié.

    ![Note de modification des métadonnées](./media/images/edit-notes.png)

1. Sur la page de résumé du projet, cliquez sur `Submit for review` pour que vos modifications soient réapprouvées par l’équipe de certification Azure.
1. Une fois vos modifications révisées et approuvées, vous pouvez republier vos modifications dans le catalogue par le biais du portail (voir notre [tutoriel](./tutorial-04-publishing-your-device.md)).

## <a name="editing-your-iot-plug-and-play-device-model"></a>Modification de votre modèle d’appareil IoT Plug-and-Play

Après avoir envoyé votre modèle d’appareil au référentiel de modèles public, vous ne pouvez plus l’en supprimer. Si vous mettez à jour votre modèle d’appareil et souhaitez lier à nouveau votre appareil certifié au nouveau modèle, vous **devez recertifier** votre appareil comme nouveau projet. Dans ce cas, laissez une note dans la section « Commentaires pour le réviseur » pour que l’équipe de certification puisse supprimer l’entrée de votre ancien appareil.

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant réussi à modifier votre appareil dans le catalogue Azure Certified Device. Vous pouvez vérifier vos modifications sur le catalogue ou certifier un autre appareil !
- [Catalogue Azure Certified Device](https://devicecatalog.azure.com/)
- [Prise en main de la certification d’un appareil](./tutorial-01-creating-your-project.md)
