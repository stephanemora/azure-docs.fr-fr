---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 0af9d225a1bb1c8d5b4eff3b54698d2b033c4c58
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412625"
---
1. Connectez-vous au [portail Azure][Azure portal].
1. Sélectionnez **Créer une ressource**. Ensuite, sélectionnez **Intégration** > **Relais**. Si **Relais** ne s’affiche pas dans la liste, sélectionnez **Afficher tout** dans l’angle supérieur droit.
1. Sélectionnez **Créer**, puis entrez un nom d’espace de noms dans le champ **Nom**. Le portail Azure vérifie si le nom est disponible.
1. Choisissez un abonnement Azure dans lequel créer l’espace de noms.
1. Pour l’option [Groupe de ressources](../../azure-resource-manager/management/manage-resource-groups-portal.md), choisissez un groupe de ressources existant dans lequel placer l’espace de noms, ou créez-en un nouveau.  
1. Sélectionnez le pays ou la région où votre espace de noms doit être hébergé.

    ![Créer un espace de noms][create-namespace]

1. Sélectionnez **Create** (Créer). Le portail Azure crée l’espace de noms, puis l’active. Après quelques minutes, le système approvisionne des ressources pour votre compte.

### <a name="get-management-credentials"></a>Obtenir des informations d'identification d'administration

1. Sélectionnez **Toutes les ressources**, puis choisissez le nom de l’espace de noms nouvellement créé.
1. Sélectionnez **Stratégies d’accès partagé**.  
1. Dans **Stratégies d’accès partagées**, sélectionnez **RootManageSharedAccessKey**.
1. Sous **Stratégie SAS : RootManageSharedAccessKey**, sélectionnez le bouton **Copier** à côté de **Chaîne de connexion principale**. L’action copie la chaîne de connexion dans le Presse-papiers pour l’utiliser plus tard. Copiez cette valeur dans le Bloc-notes ou un autre emplacement temporaire.
1. Répétez l’étape précédente pour copier et coller la valeur de **Clé primaire** dans un emplacement temporaire pour une utilisation ultérieure.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
