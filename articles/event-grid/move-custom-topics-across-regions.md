---
title: Déplacer des rubriques personnalisées Azure Event Grid vers une autre région
description: Cet article explique comment déplacer des rubriques personnalisées Azure Event Grid d’une région vers une autre.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145335"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Déplacer des rubriques personnalisées Azure Event Grid vers une autre région
Vous pouvez être amené à déplacer vos ressources vers une autre région pour diverses raisons. Par exemple, pour tirer parti d’une nouvelle région Azure, pour répondre à des exigences de stratégie et de gouvernance internes ou pour respecter des exigences de planification de la capacité. 

Voici les étapes générales décrites dans cet article : 

- **Exporter la ressource de rubrique personnalisée** vers un modèle Azure Resource Manager. 

    > [!IMPORTANT]
    > Seule la rubrique personnalisée est exportée vers le modèle. Les abonnements de la rubrique ne sont pas exportés.
- **Utiliser le modèle pour déployer la rubrique personnalisée** dans la région cible. 
- **Créer manuellement des abonnements** dans la région cible. Lorsque vous exportez la rubrique personnalisée vers un modèle dans la région actuelle, seule la rubrique est exportée. Les abonnements ne sont pas inclus dans le modèle. Vous devez donc les créer manuellement après la création de la rubrique personnalisée dans la région cible. 
- **Vérifier le déploiement**. Vérifiez que la rubrique personnalisée est créée dans la région cible. 
- Pour **terminer le déplacement**, supprimez la rubrique personnalisée de la région source. 

## <a name="prerequisites"></a>Prérequis
- Suivez le [Démarrage rapide : Router des événements personnalisés vers un point de terminaison web](custom-event-quickstart-portal.md) dans la région source. Effectuez cette étape afin de pouvoir tester les étapes de cet article. 
- Vérifiez que le service Event Grid est disponible dans la région cible. Consultez la [disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Préparation
Pour commencer, exportez un modèle Resource Manager pour la rubrique personnalisée. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre de recherche tapez **Rubriques Event Grid**, puis sélectionnez **Rubriques Event Grid** dans la liste des résultats. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Recherchez et sélectionnez Rubriques Event Grid":::
3. Sélectionnez la **rubrique** que vous souhaitez exporter vers un modèle Resource Manager. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Sélectionnez la vignette personnalisée":::   
4. Dans la page **Rubrique Event Grid**, sélectionnez **Exporter le modèle** sous **Paramètres** dans le menu de gauche, puis sélectionnez **Télécharger** dans la barre d’outils. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Exporter le modèle -> Télécharger":::   

    > [!IMPORTANT]
    > Seule la rubrique est exportée vers le modèle. Les abonnements de la rubrique ne sont pas exportés. Par conséquent, vous devez créer des abonnements pour la rubrique après l’avoir déplacée vers la région cible. 
5. Localisez le fichier **.zip** que vous avez téléchargé à partir du portail, puis décompressez-le dans le dossier de votre choix. Ce fichier zip contient les fichiers JSON des modèles et paramètres. 
1. Ouvrez le fichier **template.json** dans l’éditeur de votre choix. 
8. Mettez à jour `location` pour la ressource **rubrique** en spécifiant la région ou l’emplacement cible. Pour obtenir les codes d’emplacement, consultez [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/). Le code d’une région correspond au nom de la région sans espace, par exemple `West US` correspond à `westus`.

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. **Enregistrez** le modèle. 

## <a name="recreate"></a>Recréer 
Déployez le modèle pour créer une rubrique personnalisée dans la région cible. 

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.
3. Sélectionnez **Déploiement de modèle**.
4. Sélectionnez **Create** (Créer).
5. Sélectionnez **Générer votre propre modèle dans l’éditeur**.
6. Sélectionnez **Charger le fichier**, puis suivez les instructions pour charger le fichier **template.json** que vous avez téléchargé dans la section précédente.
7. Sélectionnez **Enregistrer** pour enregistrer le modèle. 
8. Dans la page **Déploiement personnalisé**, procédez comme suit : 
    1. Sélectionnez un **abonnement** Azure. 
    1. Sélectionnez un **groupe de ressources** existant dans la région cible ou créez-en un. 
    1. Pour **Région**, sélectionnez la région cible. Si vous avez sélectionné un groupe de ressources existant, ce paramètre est en lecture seule. 
    1. Pour **Nom de la rubrique**, entrez un nouveau nom pour la rubrique. 
    1. Au bas de la page, sélectionnez **Examiner et créer**. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Déploiement personnalisé":::
    1. Dans la page **Vérifier + créer**, passez en revue les paramètres, puis sélectionnez **Créer**. 

## <a name="verify"></a>Vérification

1. Une fois le déploiement réussi, sélectionnez **Accéder à la ressource**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Accéder à la ressource":::
1. Vérifiez que la page **Rubrique Event Grid** de la rubrique personnalisée est visible.   
1. Suivez les étapes fournies dans [Router des événements personnalisés vers un point de terminaison web](custom-event-quickstart-portal.md#send-an-event-to-your-topic) pour envoyer des événements à la rubrique. Vérifiez que le gestionnaire d’événements du webhook est appelé. 

## <a name="discard-or-clean-up"></a>Ignorer ou nettoyer
Pour terminer le déplacement, supprimez la rubrique personnalisée dans la région source.  

Si vous souhaitez recommencer, supprimez la rubrique dans la région cible, puis répétez les étapes décrites dans les sections [Préparer](#prepare) et [Recréer](#recreate) de cet article.

Pour supprimer une rubrique personnalisée à l’aide du portail Azure :

1. Dans la fenêtre de recherche en haut du portail Azure, tapez **Rubriques Event Grid**, puis sélectionnez **Rubriques Event Grid** dans les résultats de la recherche. 
2. Sélectionnez la rubrique à supprimer, puis choisissez **Supprimer** dans la barre d’outils. 
3. Dans la page de confirmation, entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.  

Pour supprimer le groupe de ressources qui contient la rubrique personnalisée à l’aide du portail Azure :

1. Dans la fenêtre de recherche en haut du portail Azure, tapez **Groupes de ressources**, puis sélectionnez **Groupes de ressources** dans les résultats de la recherche. 
2. Sélectionnez le groupe de ressources à supprimer, puis choisissez **Supprimer** dans la barre d’outils. 
3. Dans la page de confirmation, entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.  

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris à déplacer une rubrique Event Grid personnalisée d’une région vers une autre. Consultez les articles suivants pour déplacer des rubriques système, des domaines et des espaces de noms de partenaires parmi différentes régions.

- [Déplacer des rubriques système d’une région à une autre](move-system-topics-across-regions.md). 
- [Déplacer des domaines d’une région à une autre](move-domains-across-regions.md). 
- [Déplacer des espaces de noms de partenaires d’une région à une autre](move-partner-namespaces-across-regions.md).

Pour plus d’informations sur le déplacement de ressources entre régions et la récupération d’urgence dans Azure, consultez l’article suivant : [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).