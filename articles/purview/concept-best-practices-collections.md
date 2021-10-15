---
title: Architecture et meilleures pratiques des collections Purview
description: Cet article fournit des exemples d’architectures des collections Azure Purview et décrit les meilleures pratiques.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 838730453f5d49efd756abce40faec74513d52b2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390327"
---
# <a name="azure-purview-collections-architectures-and-best-practices"></a>Architecture et meilleures pratiques des collections Purview  

Au cœur d’Azure Purview, le mappage de données est un composant PaaS (Platform as a service) qui conserve une carte à jour des ressources et leurs métadonnées dans l’ensemble de vos données. Pour mettre en attente le mappage de données, vous devez inscrire et analyser vos sources de données. Dans une organisation, il peut y avoir des milliers de sources de données qui sont gérées et régies par des équipes centralisées ou décentralisées.  

Les [collections](./how-to-create-and-manage-collections.md) dans Azure Purview prennent en charge le mappage organisationnel des métadonnées. À l’aide de regroupements, vous pouvez gérer et gérer des sources de données, des analyses et des ressources dans une hiérarchie au lieu d’une structure plate. Les collections vous permettent de créer un modèle hiérarchique personnalisé de votre paysage de données en fonction de la façon dont votre organisation envisage d’utiliser Azure Purview pour gérer votre paysage.

Une collection fournit également une limite de sécurité pour vos métadonnées dans le mappage de données. L’accès aux collections, aux sources de données et aux métadonnées est défini et géré en fonction de la hiérarchie des collections dans Azure Purview, suivant un modèle de privilège minimum : 
- Les utilisateurs disposent de la quantité minimale d’accès dont ils ont besoin pour effectuer leur travail. 
- Les utilisateurs n’ont pas accès aux données sensibles dont ils n’ont pas besoin.

## <a name="intended-audience"></a>Public concerné

- Équipe en charge de l’architecture des données 
- Équipes en charge de la gestion et de la gouvernance des données
- Équipe de sécurité des données

## <a name="why-do-you-need-to-define-collections-and-an-authorization-model-for-your-azure-purview-account"></a>Pourquoi devez-vous définir des regroupements et un modèle d’autorisation pour votre compte Azure Purview ? 

Envisagez de déployer des regroupements dans Azure Purview pour répondre aux exigences suivantes : 

- Organisez les sources de données, distribuez les ressources et exécutez des analyses en fonction des besoins de votre entreprise, de la distribution géographique des données et des équipes de gestion des données, des services ou des fonctions commerciales. 

- Déléguez la propriété des sources de données et des ressources aux équipes correspondantes en affectant des rôles aux regroupements correspondants. 

- Rechercher et filtrer les éléments multimédias par Collections. 
 

## <a name="define-a-collection-hierarchy"></a>Définir une hiérarchie de collection  

### <a name="design-considerations"></a>Remarques relatives à la conception  

- Chaque compte Purview est créé avec un _regroupement racine_ par défaut. Le nom de la collection racine est le même que le nom de votre compte Azure Purview. Impossible de supprimer le regroupement racine. Pour modifier le nom convivial de la collection racine, vous pouvez modifier le nom convivial de votre compte Purview à partir du centre d’administration Purview.   

- Les regroupements peuvent contenir des sources de données, des analyses, des ressources et des attributions de rôles.

- Une collection peut avoir autant de collections enfants que nécessaire. Toutefois, chaque collection ne peut avoir qu’une seule collection parent. Vous ne pouvez pas déployer de collections au-dessus de la collection racine.

- Les sources de données, les analyses et les ressources ne peuvent appartenir qu’à une seule collection. 

- Une hiérarchie de collections dans Azure Purview peut prendre en charge jusqu’à 256 collections, avec un maximum de huit niveaux de profondeur. Cela n'inclut pas la collection racine. 

- Par défaut, vous ne pouvez pas inscrire des sources de données plusieurs fois dans un seul compte Purview. Cette architecture permet d’éviter le risque d’affectation de différents niveaux de contrôle d’accès à une source de données unique. Si plusieurs équipes consomment les métadonnées d’une source de données unique, vous pouvez inscrire et gérer la source de données dans une collection parente. Vous pouvez ensuite créer des analyses correspondantes sous chaque sous-groupe afin que les ressources pertinentes apparaissent sous chaque regroupement enfant.

- Les connexions et les artefacts de lignage sont attachés à la collection racine même si les sources de données sont inscrites dans des collections de niveau inférieur.

- Lorsque vous exécutez une nouvelle analyse, par défaut, l’analyse est déployée dans la même collection que la source de données. Vous pouvez éventuellement sélectionner un autre sous-groupe pour exécuter l’analyse. Par conséquent, les ressources appartiendront à la sous-collection. 

- Actuellement, le déplacement des sources de données entre les collections n’est pas autorisé. Si vous avez besoin de déplacer une source de données sous une autre collection, vous devez supprimer toutes les ressources, supprimer la source de données de la collection d’origine, puis réinscrire la source de données sous la collection de destinations.

- Le déplacement de ressources entre les regroupements est autorisé si l’utilisateur se voit accorder le rôle de conservateur de données pour les regroupements source et de destination. 

- Actuellement, certaines opérations, telles que le déplacement et le changement de nom d’une collection, ne sont pas autorisées. 

- Vous pouvez supprimer une collection si elle n’a pas de ressources, d’analyses associées, de sources de données ou de collections enfants.

- Les sources de données, les analyses et les éléments multimédias doivent appartenir à un regroupement s’ils existent dans le mappage de données Azure Purview.    

<!-- 
- Moving data sources across collections is allowed if the user is granted the Data Source Admin role for the source and destination collections. 

- Moving assets across collections is allowed if the user is granted the Data Curator role for the source and destination collections. 

-->

### <a name="design-recommendations"></a>Recommandations de conception 

- Passez en revue les [meilleures pratiques pour le compte Azure Purview](./deployment-best-practices.md#determine-the-number-of-purview-instances) et définissez le nombre adéquat de comptes portée requis dans votre organisation avant de planifier la structure de la collection.  

- Nous vous recommandons de concevoir l’architecture de votre collection en fonction des exigences de sécurité et de la structure de gestion et de gouvernance des données de votre organisation. Passez en revue les [regroupements archétypes](#collections-archetypes) recommandés dans cet article.

- Pour une évolutivité future, nous vous recommandons de créer une collection de niveau supérieur pour votre organisation sous le regroupement racine. Assignez des rôles appropriés au niveau de la collection de niveau supérieur au lieu de la collection racine.  

- Envisagez la gestion de la sécurité et des accès dans le cadre de votre processus de prise de décision lors de la création de regroupements dans Azure Purview. 

- Chaque collection a un attribut nom et un attribut nom convivial. Si vous utilisez Azure [Purview Studio](https://web.purview.azure.com/resource/) pour déployer un regroupement, le système affecte automatiquement un nom aléatoire de six lettres au regroupement afin d’éviter la duplication. Pour réduire la complexité, évitez d’utiliser des noms conviviaux dupliqués dans vos collections, en particulier dans le même niveau.  

- Si vous le pouvez, évitez de dupliquer la structure de votre organisation dans une hiérarchie de groupe d’administration profondément imbriquée. Si vous ne pouvez pas éviter cela, veillez à utiliser des noms différents pour chaque collection de la hiérarchie afin de faciliter la distinction entre les collections.

- Automatisez le déploiement des regroupements à l’aide de l’API si vous envisagez de déployer des regroupements et des attributions de rôles en bloc.

- Utilisez un nom de principal du service (SPN) dédié pour exécuter des opérations sur les regroupements et l’attribution de rôle à l’aide de l’API. L’utilisation d’un SPN réduit le nombre d’utilisateurs disposant de droits élevés, conformément aux recommandations en matière de privilège minimum.

## <a name="define-an-authorization-model"></a>Définir un modèle d’autorisation

Les rôles de plan de données Azure Purview sont gérés dans Azure Purview. Une fois que vous avez déployé un compte Purview, le créateur du compte Purview reçoit automatiquement les rôles suivants au niveau du regroupement racine. Vous pouvez utiliser [Purview Studio](https://web.purview.azure.com/resource/) ou une méthode de programmation pour attribuer et gérer directement des rôles dans Azure Purview.

  - **Administrateurs de collections** : ils peuvent modifier les collections Purview, leurs détails et ajouter des sous-collections. Ils peuvent également ajouter des utilisateurs dans d’autres rôles Purview sur les collections dont ils sont administrateurs.
  - **Administrateurs de source de données** : peuvent gérer les sources de données et les analyses de données.
  - **Conservateurs de données** : ils peuvent créer, lire, modifier et supprimer les ressources de données du catalogue et établir des relations entre les ressources.
  - **Lecteurs de données** : ils peuvent accéder aux ressources de données du catalogue, mais pas les modifier.

### <a name="design-considerations"></a>Remarques relatives à la conception  

- La gestion de l’accès Azure Purview a été déplacée dans le plan de données. Les rôles de Azure Resource Manager ne sont plus utilisés. Vous devez donc utiliser Azure Purview pour attribuer des rôles. 

- Dans Azure Purview, vous pouvez attribuer des rôles à des utilisateurs, des groupes de sécurité et des principaux de service (y compris des identités gérées) à partir de Azure Active Directory (Azure AD) sur le même Azure AD client où le compte Purview est déployé.
  
- Vous devez d’abord ajouter des comptes invités à votre client Azure AD en tant qu’utilisateurs B2B avant de pouvoir affecter des rôles Purview à des utilisateurs externes. 

- Par défaut, les administrateurs de regroupements n’ont pas accès à la lecture ou à la modification des ressources. Mais ils peuvent élever leur accès et s’ajouter à d’autres rôles.

- Par défaut, toutes les attributions de rôles sont héritées automatiquement par toutes les collections enfants. Toutefois, vous pouvez activer **restreindre les autorisations héritées** sur n’importe quelle collection, à l’exception de la collection racine. **Restreindre les autorisations héritées** supprime les rôles hérités de toutes les collections parentes, à l’exception du rôle d’administrateur de collection. 

- Pour la connexion Azure Data Factory : pour vous connecter à Azure Data Factory, vous devez être administrateur de collection pour la collection racine.

- Si vous devez vous connecter à Azure Data Factory pour le lignage, accordez le rôle de conservateur de données à l’identité gérée de la fabrique de données au niveau de la collection racine portée. Lorsque vous connectez Data Factory à Purview dans l’interface utilisateur de création, Data Factory tente d’ajouter ces attributions de rôles automatiquement. Si vous avez un rôle Administrateurs de collection sur la collection racine Purview, cette opération s’effectue avec succès. 

### <a name="design-recommendations"></a>Recommandations de conception 

- Envisagez d’implémenter l'[accès d’urgence](/azure/active-directory/users-groups-roles/directory-emergency-access) ou une stratégie de coupe pour le rôle d’administrateur de collection au niveau de la collection racine Azure Purview pour éviter les verrouillages au niveau du compte Purview. Documentez le processus d’utilisation des comptes d’urgence. 

    > [!NOTE]
    > Dans certains scénarios, vous devrez peut-être utiliser un compte d’urgence pour vous connecter à Azure Purview. Vous aurez peut-être besoin de ce type de compte pour résoudre les problèmes d’accès au niveau de l’organisation lorsque personne d’autre ne peut se connecter à Purview ou lorsque d’autres administrateurs ne peuvent pas effectuer certaines opérations en raison de problèmes d’authentification d’entreprise. Nous vous recommandons vivement de suivre les meilleures pratiques Microsoft en matière d’implémentation des [comptes d’accès d’urgence](/azure/active-directory/users-groups-roles/directory-emergency-access) à l’aide d’utilisateurs Cloud uniquement.
    >
    > Suivez les instructions de [cet article](./concept-account-upgrade.md#what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin) pour récupérer l’accès à votre collection racine Purview si votre administrateur de collection précédent n’est pas disponible.

- Réduisez le nombre d’administrateurs de collection racine. Assignez au maximum trois utilisateurs administrateurs de collection au niveau de la collection racine, y compris le SPN et vos comptes de panne. Affectez à la place les rôles d’administrateur de votre collection à la collection de niveau supérieur ou aux sous-groupes.

- Affectez des rôles à des groupes plutôt qu’à des utilisateurs individuels afin de réduire la surcharge administrative et les erreurs de gestion des rôles individuels. 

- Affectez le principal du service à la collection racine à des fins d’automatisation.

- Pour renforcer la sécurité, activez Azure AD accès conditionnel avec l’authentification multifacteur pour au moins les administrateurs de collection, administrateurs de source de données et conservateurs de données. Assurez-vous que les comptes d’urgence sont exclus de la stratégie d’accès conditionnel.
 
## <a name="collections-archetypes"></a>Collections archétypes

Vous pouvez déployer votre collection Azure Purview basée sur des modèles de gestion et de gouvernance des données centralisés, décentralisés ou hybrides. Basez cette décision sur vos exigences professionnelles et de sécurité.

### <a name="example-1-single-region-organization"></a>Exemple 1 : organisation à une seule région 

Cette structure convient aux organisations qui : 
- Sont principalement basées sur un emplacement géographique unique. 
- Disposent d’une équipe de gestion et de gouvernance des données centralisée dans laquelle le niveau de gestion des données suivant se situe dans les départements, les équipes ou les projets.  

La hiérarchie de collection se compose des éléments suivants : 

- Collection racine (par défaut)
- Contoso (collection de niveau supérieur)
- Services (collection déléguée pour chaque service) 
- Équipes ou projets (répartition supplémentaire en fonction des projets)

Chaque source de données est inscrite et analysée dans sa collection correspondante. Ainsi, les éléments multimédias apparaissent également dans le même regroupement. 

Les sources de données partagées au niveau de l’organisation sont inscrites et analysées dans la collection Hub-Shared. 

Les sources de données partagées au niveau du service sont inscrites et analysées dans les regroupements de services. 

:::image type="content" source="media/concept-best-practices/collections-example-1.png" alt-text="Capture d’écran montrant le premier exemple de collections Azure Purview."lightbox="media/concept-best-practices/collections-example-1.png":::

### <a name="example-2-multiregion-organization"></a>Exemple 2 : organisation multirégion

Ce scénario est utile pour les organisations : 
- Qui ont une présence dans plusieurs régions. 
- Où l’équipe de gouvernance des données est centralisée ou décentralisée dans chaque région.
- Où les équipes de gestion des données sont distribuées à chaque emplacement géographique. 

La hiérarchie de collection se compose des éléments suivants : 

- Collection racine (par défaut)
- FourthCoffee (collection de niveau supérieur)
- Emplacements géographiques (regroupements de niveau intermédiaire basés sur les emplacements géographiques où se trouvent les sources de données et les propriétaires de données)
- Services (collection déléguée pour chaque service) 
- Équipes ou projets (répartition supplémentaire en fonction des équipes ou des projets)

Dans ce scénario, chaque région a un sous-groupe propre sous le regroupement de niveau supérieur dans le compte Purview. Les sources de données sont inscrites et analysées dans les sous-groupes correspondants dans leurs propres emplacements géographiques. Les ressources s’affichent également dans la hiérarchie de sous-groupes pour la région. 

Si vous avez des équipes de gouvernance et de gestion des données centralisées, vous pouvez leur accorder l’accès à partir du regroupement de niveau supérieur. Dans ce cas, ils ont un aperçu de l’ensemble de l’espace de données dans le mappage de données. Si vous le souhaitez, l’équipe centralisée peut inscrire et analyser toutes les sources de données partagées.

Les équipes de gestion et de gouvernance des données basées sur les régions peuvent obtenir un accès à partir de leurs regroupements correspondants à un niveau inférieur.

Les sources de données partagées au niveau du service sont inscrites et analysées dans les regroupements de services. 

:::image type="content" source="media/concept-best-practices/collections-example-2.png" alt-text="Capture d’écran montrant le premier exemple de collections Azure Purview."lightbox="media/concept-best-practices/collections-example-2.png":::

### <a name="example-3-multiregion-data-transformation"></a>Exemple 3 : multirégion, transformation de données

Ce scénario peut être utile si vous souhaitez distribuer la gestion de l’accès aux métadonnées en fonction des emplacements géographiques et des états de transformation des données. Les chercheurs de données et les ingénieurs de données qui peuvent transformer des données pour le rendre plus significatif peuvent gérer des zones brutes et affinées. Ils peuvent ensuite déplacer les données dans des zones de production ou des zones organisées.  

La hiérarchie de collection se compose des éléments suivants : 

- Collection racine (par défaut)
- Fabrikam (regroupement de niveau supérieur)
- Emplacements géographiques (regroupements de niveau intermédiaire basés sur les emplacements géographiques où se trouvent les sources de données et les propriétaires de données)
- Étapes de transformation des données (brut, affiner, production/organisée) 

Les scientifiques des données et les ingénieurs de données peuvent avoir le rôle de conservateurs de données sur les zones correspondantes pour pouvoir organiser les métadonnées. L’accès du lecteur de données à la zone organisée peut être accordé à des personnes de données entières et à des utilisateurs professionnels. 

:::image type="content" source="media/concept-best-practices/collections-example-3.png" alt-text="Capture d’écran montrant le troisième exemple de collections Azure Purview."lightbox="media/concept-best-practices/collections-example-3.png":::

### <a name="example-4-multiregion-business-functions"></a>Exemple 4 : multirégion, fonctions métier 

Cette option peut être utilisée par les organisations qui ont besoin d’organiser la gestion des métadonnées et des accès en fonction des fonctions métier.

La hiérarchie de collection se compose des éléments suivants : 

- Collection racine (par défaut)
- AdventureWorks (regroupement de niveau supérieur)
- Emplacements géographiques (regroupements de niveau intermédiaire basés sur les emplacements géographiques où se trouvent les sources de données et les propriétaires de données)
- Principaux clients ou fonctions commerciales (répartition supplémentaire basée sur les fonctions ou les clients)

Chaque région a un sous-groupe propre sous le regroupement de niveau supérieur dans le compte Purview. Les sources de données sont inscrites et analysées dans les sous-groupes correspondants dans leurs propres emplacements géographiques. Les ressources s’affichent également dans la hiérarchie de sous-groupes pour la région. 

Si vous avez des équipes de gouvernance et de gestion des données centralisées, vous pouvez leur accorder l’accès à partir du regroupement de niveau supérieur. Dans ce cas, ils ont un aperçu de l’ensemble de l’espace de données dans le mappage de données. Si vous le souhaitez, l’équipe centralisée peut inscrire et analyser toutes les sources de données partagées.

Les équipes de gestion et de gouvernance des données basées sur les régions peuvent obtenir un accès à partir de leurs regroupements correspondants à un niveau inférieur.
Chaque unité commerciale possède son propre sous-groupe.

:::image type="content" source="media/concept-best-practices/collections-example-4.png" alt-text="Capture d’écran montrant le quatrième exemple de collections Azure Purview."lightbox="media/concept-best-practices/collections-example-4.png":::

## <a name="access-management-options"></a>Options de gestion des accès

Si vous souhaitez implémenter la démocratisation des données au sein d’une organisation entière, attribuez le rôle de lecteur de données à la collection de niveau supérieur à la gestion des données, à la gouvernance et aux utilisateurs professionnels. Attribuez des rôles d’administrateur de source de données et de conservateur de données aux niveaux des sous-groupes aux équipes de gestion et de gouvernance des données correspondantes.

Si vous devez restreindre l’accès à la recherche et à la découverte des métadonnées dans votre organisation, affectez des rôles de lecteur de données et de conservateur de données au niveau de la collection spécifique. Par exemple, vous pouvez limiter les employés aux États-Unis pour qu’ils puissent lire les données uniquement au niveau de la collection US et non dans la collection LATAM. 

Vous pouvez appliquer une combinaison de ces deux scénarios dans votre carte de données Purview si la démocratisation totale des données est requise avec quelques exceptions pour certaines collections. Vous pouvez assigner des rôles Purview à la collection de niveau supérieur et limiter l’héritage aux collections enfants spécifiques.

Affectez le rôle d’administrateur de collection à l’équipe de sécurité et de gestion des données centralisée dans le regroupement de niveau supérieur. Déléguer la gestion de la collecte des collections de niveau inférieur aux équipes correspondantes.

## <a name="next-steps"></a>Étapes suivantes
-  [Création d’une collection et attribution d’autorisations dans Purview](./quickstart-create-collection.md)
-  [Créer et gérer des regroupements dans Azure Purview](./how-to-create-and-manage-collections.md)
-  [Contrôle d’accès dans Azure Purview](./catalog-permissions.md)
