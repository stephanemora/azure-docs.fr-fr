---
author: baanders
description: Fichier Include pour le processus de publication d’une fonction Azure à partir de Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 59506b1d1d3fbbc5a532c597d46dc92ee3c2e98e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750885"
---
Pour publier le projet sur une application de fonction dans Azure, cliquez avec le bouton droit sur le projet dans l’*Explorateur de solutions*, puis sélectionnez **Publier**.

> [!IMPORTANT] 
> La publication d’une application de fonction dans Azure entraîne des frais supplémentaires dans votre abonnement, indépendamment d’Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Capture d’écran de Visual Studio montrant le menu contextuel de la solution. L’option Publier est mise en surbrillance dans le menu.":::

Dans la page *Publier* qui suit, conservez **Azure** comme sélection de la cible par défaut et sélectionnez *Suivant*. 

Pour une cible spécifique, choisissez **Application de fonction Azure (Windows)** et sélectionnez *Suivant*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Capture d’écran de Visual Studio dans la boîte de dialogue Publier une fonction Azure. Application de fonction Azure (Windows) est sélectionné dans la page Cible spécifique.":::

Dans la page *Instance de Functions*, choisissez votre abonnement. Sélectionnez ensuite l’icône *+* pour créer une nouvelle fonction Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Capture d’écran de Visual Studio dans la boîte de dialogue Publier une fonction Azure. Le bouton + pour créer une fonction est mis en surbrillance dans la page de l’instance Functions.":::

Dans la fenêtre *Application de fonction (Windows) - Créer* , renseignez les champs comme suit :
* Le **Nom** est le nom du plan de consommation qu’Azure utilisera pour héberger votre application Azure Functions. Il s’agit également du nom de l’application de fonction qui contient votre fonction réelle. Vous pouvez choisir votre propre valeur unique ou conserver la suggestion par défaut.
* Assurez-vous que l’**Abonnement** correspond à l’abonnement que vous souhaitez utiliser. 
* Assurez-vous que le **Groupe de ressources** correspond au groupe de ressources que vous souhaitez utiliser.
* Conservez *Consommation* comme **Type de plan**.
* Sélectionnez la **Localisation** qui correspond à la localisation de votre groupe de ressources.
* Créez une ressource de **Stockage Azure** à l’aide du lien *Nouveau...* . Définissez l’emplacement pour qu’il corresponde à votre groupe de ressources, utilisez les autres valeurs par défaut et sélectionnez « OK ».

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Capture d’écran de Visual Studio dans la boîte de dialogue Publier une fonction Azure. Les détails d’une nouvelle application de fonction sont renseignés, notamment Nom, Abonnement, Groupe de ressources, Type de plan, Emplacement et Stockage Azure.":::

Sélectionnez ensuite **Create** (Créer).

Après une courte attente pendant la création du service d’application, la boîte de dialogue doit revenir à la page *Instance de Functions*, avec votre nouvelle application de fonction apparaissant dans la zone **Applications de fonction** imbriquée sous votre groupe de ressources. Sélectionnez *Terminer*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publier la fonction Azure dans Visual Studio : Instance Functions (après la création de l’application de fonction)":::

Dans le volet *Publier* qui s’ouvre de nouveau dans la fenêtre principale de Visual Studio, vérifiez que toutes les informations semblent correctes et sélectionnez **Publier**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Capture d’écran de Visual Studio dans la boîte de dialogue Publier une fonction Azure. La nouvelle application de fonction apparaît dans la liste des applications de fonction, et un bouton Terminer est présent.":::

> [!NOTE]
> Si une fenêtre contextuelle similaire à celle-ci s’affiche : :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Capture d’écran de la fenêtre contextuelle de Visual Studio appelée Informations d’identification de publication. Il contient des champs pour un nom d’utilisateur et un mot de passe, et un bouton pour tenter de récupérer les informations d’identification d’Azure." border="false":::
> Sélectionnez **Essayer de récupérer les informations d’identification d’Azure**, puis **Enregistrer**.
>
> Si vous voyez s’afficher l’avertissement *Mettre à niveau la version de Functions sur Azure* ou *Votre version du runtime Functions ne correspond pas à la version en cours d’exécution dans Azure* :
>
> Suivez les invites pour effectuer une mise à niveau vers la dernière version du runtime Azure Functions. Ce problème peut se produire si vous utilisez une version plus ancienne de Visual Studio.

Votre application de fonction est maintenant publiée sur Azure. 

Pour que votre application de fonction puisse accéder à Azure Digital Twins, elle devra disposer d’une identité managée par le système avec les autorisations nécessaires pour accéder à votre instance d’Azure Digital Twins. Vous allez maintenant configurer cela.
