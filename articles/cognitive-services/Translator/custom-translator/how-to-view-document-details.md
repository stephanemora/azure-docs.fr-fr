---
title: Informations détaillées sur les documents – Custom Translator
titleSuffix: Azure Cognitive Services
description: La page de liste des documents affiche les 10 premiers documents dans votre espace de travail. Le nom de chaque document, ainsi que les informations relatives à son appariement, son type, sa langue, l’heure de son chargement, ainsi que l’adresse de messagerie de l’utilisateur ayant chargé ce document.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 267d60c2e9941c66ab128f68bd7aaf1cc54fedd9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442503"
---
# <a name="view-document-details"></a>Afficher les détails du document

La page de liste des documents affiche les 10 premiers documents dans votre espace de travail. Le nom de chaque document, ainsi que les informations relatives à son appariement, son type, sa langue, l’heure de son chargement, ainsi que l’adresse de messagerie de l’utilisateur ayant chargé ce document.

Cliquez sur un document pour afficher la page d’informations détaillées à son sujet. La page d’informations détaillées d’un document contient une liste des phrases extraites de ce document.

- La « langue source » est sélectionnée par défaut dans le champ de liste déroulante, mais vous pouvez changer cela pour afficher les phrases dans la « langue cible ».
- Par défaut, chaque page affiche 20 phrases. Vous pouvez utiliser le contrôle de pagination pour naviguer entre les pages.

![Informations détaillées sur le document](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Supprimer un document

Pour pouvoir supprimer un document, l’utilisateur doit être propriétaire de l’espace de travail dans lequel il se trouve. De plus, si un document est utilisé par un modèle dans n’importe quelle partie du processus de formation ou de déploiement, le document ne peut pas être supprimé.

1. Accédez à page du document.
2.  Placez le pointeur sur n’importe quel enregistrement de document, puis cliquez sur l’icône Corbeille.

    ![Supprimer un document](media/how-to/how-to-delete-document-1.png)

3.  Confirmez la suppression.

    ![Confirmer la suppression](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment effectuer l’apprentissage d’un modèle](how-to-train-model.md).
