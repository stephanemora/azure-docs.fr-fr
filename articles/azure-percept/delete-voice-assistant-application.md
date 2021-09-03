---
title: Supprimer votre application d’assistant vocal Azure Percept Audio
description: Cet article explique comment supprimer une application d’assistant vocal créée précédemment.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b62ba69695606e04e7044109818c18d30ca21c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562651"
---
# <a name="delete-your-voice-assistant-application"></a>Supprimer votre application d’assistant vocal

Ces instructions vous montrent comment supprimer une application d’assistant vocal de votre appareil Azure Percept Audio.

## <a name="prerequisites"></a>Configuration requise

- [Une application vocale Assistant créée précédemment](./tutorial-no-code-speech.md)
- Votre Azure Percept DK est sous tension et l’accessoire Azure Percept Audio est connecté à l’aide d’un câble USB.

## <a name="remove-all-voice-assistant-resources-from-the-azure-portal"></a>Supprimer toutes les ressources de l’Assistant vocal du Portail Azure

Une fois que vous avez fini d’utiliser l’application Assistant vocal, effectuez les étapes suivantes pour nettoyer les ressources Speech que vous avez déployées en créant l’application.

1. Sur le [portail Azure](https://portal.azure.com), sélectionnez **Groupes de ressources** dans le menu de gauche ou tapez « groupes de ressources » dans la barre de recherche.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Capture d’écran de la page d’accueil du portail Azure montrant le menu de gauche et les groupes de ressources.":::

1. Sélectionnez votre groupe de ressources.

1. Sélectionnez les six ressources dont le nom comprend le préfixe de votre application, puis sélectionnez l’icône **Supprimer** dans le menu du haut.

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Capture d’écran des ressources Speech sélectionnées pour la suppression.":::

1. Pour confirmer la suppression, tapez **oui** dans la zone de confirmation, vérifiez que vous avez sélectionné les bonnes ressources, puis sélectionnez **Supprimer**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Capture d’écran de la fenêtre de confirmation de la suppression.":::

> [!WARNING]
> Cette opération supprimera tous les mots clés personnalisés qui ont été créés avec les ressources Speech que vous supprimez, et la démonstration de l’Assistant vocal ne fonctionnera plus.


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez supprimé votre application d’assistant vocal, essayez de créer d’autres applications sur votre Azure Percept DK en suivant ces didacticiels.
- [Créer une solution de vision sans code](./tutorial-nocode-vision.md)
- [Créer une application d’assistant vocal sans code](./tutorial-no-code-speech.md)


