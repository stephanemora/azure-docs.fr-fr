---
title: Effectuer une migration vers une ressource Azure pour la création 2
titleSuffix: Azure Cognitive Services
description: Effectuez une migration vers une clé de ressource de création Azure 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983688"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Effectuer une migration vers une clé de ressource de création Azure

Pour la création LUIS (Language Understanding), l’authentification ne se fait plus avec un compte e-mail, mais avec une ressource Azure. Bien que cela ne soit pas le cas pour le moment, le passage à une ressource Azure sera obligatoire à l’avenir.


## <a name="what-is-migration"></a>Qu’est-ce que la migration ?

La migration est le processus consistant à passer d’un compte e-mail à une ressource Azure pour l’authentification de la création. Une fois la migration effectuée, votre compte est lié à un abonnement Azure et à une ressource de création Azure. **Tous les utilisateurs de LUIS (propriétaires ou collaborateurs) devront finalement migrer.** La migration doit être effectuée à partir du portail LUIS. Si vous créez les clés de création, par exemple à l’aide de l’interface de ligne de commande LUIS, vous devez toujours effectuer le processus de migration dans le portail LUIS. Après la migration, vous pouvez toujours avoir des coauteurs associés à vos applications, mais ceux-ci sont ajoutés au niveau de la ressource Azure plutôt qu’au niveau de l’application.

> [!Note]
> Avant la migration, les coauteurs sont appelés _collaborateurs_ au niveau de l’application LUIS. Après la migration, le rôle Azure de _contributeur_ est utilisé pour la même fonctionnalité, mais au niveau de la ressource Azure.

## <a name="note-before-you-migrate"></a>Remarque avant de migrer

* La migration est un processus **à sens unique**. Une fois l’opération effectuée, il n’est pas possible de revenir en arrière.
* Les applications **migrent automatiquement** avec leur **propriétaire**.
* Le propriétaire ne peut pas effectuer la migration d’un sous-ensemble d’applications et ce processus n’est pas réversible.
* Après que le propriétaire **a opéré la migration**, les applications **disparaissent côté collaborateur**.
* Les propriétaires sont invités à envoyer des e-mails aux collaborateurs pour les informer de la migration.
* Si vous êtes un **collaborateur** associé à une application, celle-ci ne **migre pas** avec vous.
* Il n’existe aucun moyen pour le propriétaire d’une application de savoir si des collaborateurs ont migré.
* **La migration ne collecte pas** automatiquement les collaborateurs pour les déplacer vers la ressource de création Azure ou les ajouter à celle-ci. Il incombe au propriétaire de l’application d’effectuer cette étape après la migration. Cette étape nécessite de disposer d’autorisations [sur la ressource de création Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Les collaborateurs affectés à la ressource Azure **doivent effectuer la migration pour accéder aux applications**. Autrement, ils n’ont pas accès à la création des applications.
* Il n’est pas possible d’ajouter un utilisateur migré en tant que collaborateur de l’application.
* Si **vous possédez des clés de prédiction attribuées à des applications appartenant à un autre utilisateur**, cela a pour effet de **bloquer la migration** pour le propriétaire et les collaborateurs. Vous trouverez des recommandations ci-dessous.

> [!Note]
> Si vous avez besoin de créer un runtime de prédiction, [un processus distinct](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) permet de le faire.

## <a name="migration-prerequisites"></a>Prérequis pour la migration

* Vous devez être associé à un abonnement Azure valide. Demandez à votre administrateur de locataire de vous ajouter à l’abonnement, ou inscrivez-vous pour en obtenir un gratuitement [ici](https://azure.microsoft.com/free/).
* Vous devez créer une ressource de création LUIS Azure à partir du portail LUIS ou du portail Azure. La création d’une ressource de création à partir du portail LUIS fait partie du flux de migration décrit dans la section suivante.
* Si vous êtes un **collaborateur associé à des applications**, celles-ci ne migrent pas automatiquement. Il est **recommandé de sauvegarder les applications** en les exportant ou en utilisant l’[API d’exportation](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Vous pouvez les réimporter dans LUIS après la migration. Le processus d’importation crée une application (avec un nouvel ID d’application) dont vous êtes le propriétaire.
* Si vous êtes le **propriétaire de l’application**, vous n’avez pas besoin de l’exporter, car elle migre automatiquement. Il est **recommandé d’enregistrer la liste des collaborateurs de chaque application**. Un modèle d’e-mail contenant cette liste est éventuellement fourni dans le cadre du processus de migration.


|Portail|Objectif|
|--|--|
|[Microsoft Azure](https://azure.microsoft.com/free/)|* Créer des ressources de prédiction et de création<br>* Affecter des contributeurs aux ressources.|
|[LUIS](https://www.luis.ai)|* Effectuer une migration vers de nouvelles ressources de création<br>* Créer une ressource de création dans le flux de migration.<br>* Affecter des ressources de prédiction et de création à des applications (ou annuler leur affectation) à partir de la page **Gérer -> Ressources Azure** <br>* Déplacer des applications d’une ressource de création vers une autre.  |

> [!Note]
> **La création de votre application LUIS est gratuite**, indiquée par le niveau `F0`. Découvrez [plus d’informations sur les niveaux tarifaires](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Étapes de la migration

1. Dans le portail LUIS au sein duquel vous travaillez, vous pouvez commencer le processus de migration à partir de l’icône **Azure** dans la barre d’outils supérieure.

   > [!div class="mx-imgBorder"]
   > ![Icône Migration](./media/migrate-authoring-key/migration-button.png)

2. La fenêtre contextuelle de migration vous permet de poursuivre la migration ou de l’effectuer ultérieurement. Sélectionnez **Migrate now** (Effectuer la migration maintenant).

   > [!div class="mx-imgBorder"]
   > ![Première fenêtre contextuelle du processus de migration. Sélectionnez Migrate now (Migrer maintenant).](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Si l’une de vos applications est associée à des contributeurs, vous êtes invité à **leur envoyer un e-mail** pour les informer de la migration. Il s’agit d’une étape facultative.

   Pour chaque collaborateur et application, l’application de messagerie par défaut s’ouvre avec un e-mail légèrement formaté. Vous pouvez modifier l’e-mail avant de l’envoyer. Le modèle d'e-mail inclut l'ID exact de l'application et le nom de l'application.
   
   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources
      
   Thank you
   ```
   
   > [!Note]
   > Une fois votre compte migré vers Azure, vos applications ne sont plus disponibles pour les collaborateurs.

4. Éventuellement, si vous êtes un collaborateur associé à une application, vous êtes invité à **exporter une copie de celle-ci** en sélectionnant cette option pendant le flux de migration. La sélection de l’option a pour effet de vous diriger vers la page ci-dessous dans laquelle vous pouvez cliquer sur le bouton Télécharger à gauche pour exporter les applications souhaitées. Vous pouvez réimporter celles-ci après la migration, car elles ne migrent pas automatiquement avec vous. Il s’agit d’une étape facultative.

   > [!div class="mx-imgBorder"]
   > ![Invite à exporter votre application.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Vous pouvez choisir de créer une ressource de création LUIS ou de migrer vers une ressource de création que vous avez déjà créée à partir d’Azure. Choisissez l’option souhaitée en sélectionnant le bouton approprié ci-dessous.

   > [!div class="mx-imgBorder"]
   > ![Créez une ressource de création](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Créer une ressource de création à partir de LUIS à migrer

Si vous souhaitez créer une ressource de création, sélectionnez **créer une ressource de création**, puis fournissez les informations ci-après dans la fenêtre suivante.

> [!div class="mx-imgBorder"]
> ![Créez une ressource de création](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Resource name** (Nom de la ressource) : nom personnalisé que vous choisissez, utilisé comme élément de l’URL pour vos requêtes de point de terminaison de création et de prédiction.
* **Subscription name** (Nom de l’abonnement) : abonnement qui sera associé à la ressource. Si votre locataire possède plusieurs abonnements, sélectionnez celui que vous souhaitez dans la liste déroulante.
* **Resource group** (Groupe de ressources) : nom de groupe de ressources personnalisé que vous choisissez dans la liste déroulante. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion.
* **Tenant** (Locataire) : locataire auquel votre abonnement Azure est associé. Il s’agit par défaut du locataire que vous choisissez. Vous pouvez modifier des locataires en sélectionnant l’avatar à droite qui contient vos initiales.

Après avoir entré les informations ci-dessus, sélectionnez **Terminé**.

Notez que vous pouvez avoir 10 ressources de création gratuites par région et par abonnement. Si votre abonnement compte plus de 10 ressources de création dans la même région, vous ne pourrez plus en créer.

* Lorsque la ressource de création est créée, un message de confirmation s’affiche. Sélectionnez **Close** pour fermer la fenêtre contextuelle.

  > [!div class="mx-imgBorder"]
  > ![Votre ressource de création a bien été créée.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Utiliser une ressource de création existante à migrer

Si votre abonnement est déjà associé à une ressource de création LUIS Azure, ou si vous en avez créé une dans le portail Azure et que vous souhaitez migrer vers celle-ci au lieu d’en créer une, sélectionnez **Use Existing Authoring resource** (Utiliser une ressource de création existante) et fournissez les informations ci-après dans la fenêtre suivante.

> [!div class="mx-imgBorder"]
>![Créez une ressource de création](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Tenant** (Locataire) : locataire auquel votre abonnement Azure est associé. Il s’agit par défaut du locataire que vous choisissez.
* **Subscription name** (Nom de l’abonnement) : abonnement qui sera associé à la ressource. Si votre locataire possède plusieurs abonnements, sélectionnez celui que vous souhaitez dans la liste déroulante.
* **Resource name** (Nom de la ressource) : sélectionnez la ressource de création vers laquelle vous souhaitez effectuer la migration.

> [!Note]
> Si votre ressource de création ne figure pas dans la liste déroulante, assurez-vous que vous l’avez créée dans l’**emplacement approprié** correspondant au portail LUIS auquel vous êtes connecté. Assurez-vous également que ce que vous avez effectivement créé une **ressource de création** et non une **ressource de prédiction**.


* Validez le nom de votre ressource de création, puis cliquez sur le bouton **Migrate now** (Migrer maintenant).

 > [!div class="mx-imgBorder"]
 > ![Créez une ressource de création](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Le message de réussite s’affiche. Sélectionnez **Close** pour fermer la fenêtre contextuelle.

 > [!div class="mx-imgBorder"]
 > ![Votre ressource de création a bien été créée.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Utilisation des applications après la migration

* Après le processus de migration, toutes les applications LUIS dont vous êtes propriétaire sont affectées à une seule ressource de création LUIS.
* La liste **Mes applications** affiche les applications qui ont fait l’objet d’une migration vers la nouvelle ressource de création.
* Avant d’accéder à vos applications, sélectionnez l’abonnement et la ressource de création LUIS pour voir les applications que vous pouvez créer.

 > [!div class="mx-imgBorder"]
 > ![Sélectionnez un abonnement et une ressource de création LUIS pour voir les applications que vous pouvez créer.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Vous n’avez pas besoin de connaître la clé de la ressource de création pour continuer à modifier vos applications dans le portail LUIS.
* Si vous prévoyez de modifier vos applications par programmation, vous avez besoin des valeurs de clé de création. Ces valeurs sont affichées dans la page **Gérer -> Ressources Azure** du portail LUIS et sont également disponibles dans la page **Clés** de la ressource du portail Azure. Vous pouvez également créer des ressources de création supplémentaires et les affecter à partir de la même page.

 > [!div class="mx-imgBorder"]
 > ![Gérer une ressource de création.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Ajouter des contributeurs à des ressources de création

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Découvrez [comment ajouter des contributeurs](luis-how-to-collaborate.md) à votre ressource de création. Les contributeurs ont accès à toutes les applications associées à cette ressource.

Vous pouvez ajouter des contributeurs à la ressource de création à partir du _portail Azure_, dans la page **Contrôle d’accès (IAM)** de cette ressource. Pour plus d’informations, voir [Ajouter un accès contributeur](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Si le propriétaire de l’application LUIS a migré et ajouté le collaborateur en tant que contributeur sur la ressource Azure, le collaborateur n’aura toujours pas accès à l’application, sauf s’il migre également.

## <a name="luis-portal-migration-reminders"></a>Rappels de migration du portail LUIS

Le [portail LUIS](https://www.luis.ai) fournit le processus de migration.

Vous serez invité à effectuer une migration dans les cas suivants :
* Vous avez des applications à créer sur le système d’authentification par e-mail.
* Et vous êtes le propriétaire de l’application.

Chaque semaine, vous êtes invité à effectuer la migration de vos applications. Vous pouvez fermer cette fenêtre pour ne pas effectuer la migration. Si vous souhaitez effectuer la migration avant la prochaine période planifiée, vous pouvez démarrer le processus de migration à l’aide de l’icône **Azure** située dans la barre d’outils supérieure du portail LUIS.

Vous pouvez remettre à plus tard le processus de migration en fermant la fenêtre. Vous serez régulièrement invité à effectuer une migration tant que celle-ci n’aura pas été faite et que la date d’échéance n’aura pas été atteinte. Vous pouvez démarrer le processus de migration à partir de l’icône de verrou de la barre de navigation supérieure.

## <a name="prediction-resources-blocking-migration"></a>Ressources de prédiction bloquant la migration
Votre migration a un impact négatif sur le runtime d’autres applications. Lors de la migration, tous les collaborateurs sont supprimés de vos applications, et vous êtes supprimé en tant que collaborateur d’autres applications. Ce processus signifie que les clés qu’un collaborateur attribue sont également supprimées, ce qui peut bloquer votre application si celle-ci est en production. C’est la raison pour laquelle nous bloquons la migration jusqu’à ce que vous supprimiez manuellement des collaborateurs ou des clés qui leur sont attribuées.

### <a name="when-does-prediction-resources-block-migration"></a>Quand des ressources de prédiction bloquent-elles la migration ?
* La migration est bloquée si vous avez attribué des ressources de prédiction/runtime dans des applications dont vous n’êtes pas propriétaire.
* La migration est bloquée si d’autres utilisateurs attribuent des ressources de prédiction/runtime à des applications dont vous êtes propriétaire.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Procédure recommandée si vous êtes le propriétaire de l’application
Si vous êtes propriétaire d’applications auxquelles des collaborateurs ont attribué une clé de prédiction/runtime, un message d’erreur s’affiche lorsque vous effectuez la migration, qui répertorie les ID des applications auxquelles sont attribuées des clés de prédiction appartenant à d’autres utilisateurs.

Il est recommandé d’effectuer les opérations suivantes :
* Notifiez les collaborateurs de la migration.
* Supprimez tous les collaborateurs des applications répertoriées dans le message d’erreur.
* Suivez le processus de migration qui devrait aboutir si vous supprimez manuellement des collaborateurs.
* Affectez des collaborateurs en tant que contributeurs à votre nouvelle ressource de création.
* Les collaborateurs doivent effectuer la migration et réaffecter les ressources de prédiction aux applications.
Notez que l’application est bloquée temporairement jusqu’à ce que les ressources de prédiction soient réaffectées.

Une autre solution est qu’avant la migration du propriétaire, des collaborateurs ajoutent des propriétaires d’applications en tant que contributeurs à leurs abonnements Azure à partir du portail Azure. Cela permet au propriétaire d’accéder à la ressource de prédiction du Runtime. Si le propriétaire effectue une migration à l’aide du nouvel abonnement auquel il a été ajouté (qui se trouve sous un nouveau locataire), cela a pour effet non seulement de débloque le processus de migration pour le collaborateur et le propriétaire de l’application, mais aussi de permettre une migration sans heurts des applications avec la clé de prédiction toujours affectée à celles-ci.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Procédure recommandée si vous êtes un collaborateur associé à une application
Si vous collaborez sur une application à laquelle vous avez affecté une clé de prédiction/runtime, un message d’erreur s’affiche lorsque vous effectuez la migration, qui répertorie les ID des applications et les chemins d’accès de clé qui bloquent la migration.

Il est recommandé d’effectuer les opérations suivantes :
* Exportez les applications en tant que sauvegarde. Il s’agit d’une étape facultative du processus de migration.
* Désaffectez les ressources de prédiction à partir de la page **Gérer -> Ressources Azure**.
* Suivez le processus de migration.
* Réimportez des applications après la migration.
* Réaffectez les clés de prédiction à vos applications à partir de la page **Gérer -> Ressources Azure**.

> [!Note]
> Lorsque vous réimportez vos applications après la migration, elles ont des ID différents, et sont différentes des applications en production. Vous êtes maintenant le propriétaire de ces applications.

## <a name="troubleshooting-migration-process"></a>Résolution des problèmes liés au processus de migration

Lorsque vous tentez d’effectuer une migration mais ne trouvez pas votre abonnement Azure dans la liste déroulante :
* Vérifiez que vous disposez d’un abonnement Azure valide autorisé à créer des ressources Cognitive Services. Accédez au [portail Azure](https://ms.portal.azure.com) et vérifiez l’état de l’abonnement. Si vous n’en avez pas, [créez un abonnement d’essai gratuit](https://azure.microsoft.com/free/).
* Assurez-vous que vous êtes dans le locataire approprié associé à votre abonnement valide. Vous pouvez modifier des locataires à partir de l’avatar de gauche dans la barre d’outils ci-dessous : ![Modifiez des locataires.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Si vous disposez d’une ressource de création existante, mais ne la trouvez pas lors de la sélection de l’option « Use Existing Authoring resource » (Utiliser une ressource de création existante) :
* Vous avez probablement créé votre ressource dans un emplacement autre que le portail auquel êtes connecté. Consultez [Régions de création LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions).
* Créez plutôt une ressource à partir du portail LUIS.

Si vous sélectionnez l’option « Create New Authoring Resource » (Créer une ressource de création) et que la migration échoue avec le message d’erreur « Failed retrieving user’s Azure information, retry again later » (Échec de la récupération des informations Azure de l’utilisateur, réessayez plus tard ») :
* Il se peut que votre abonnement compte au moins 10 ressources de création par région et par abonnement. Si tel est le cas, vous ne pouvez plus créer de ressource de création.
* Effectuez la migration en sélectionnant l’option « Use Existing Authoring resource » (Utiliser une ressource de création existante), puis sélectionnez l’une des ressources disponibles dans votre abonnement.

Si vous voyez le message d’erreur ci-dessous, consultez [section Procédure recommandée si vous êtes le propriétaire de l’application] ![Échec de la migration pour les propriétaires](./media/migrate-authoring-key/migration-failed-for-owner-2.png).

Si vous voyez le message d’erreur ci-dessous, consultez [section Procédure recommandée si vous êtes un collaborateur associé à une application] ![Échec de la migration pour les collaborateurs](./media/migrate-authoring-key/migration-failed-for-collab-2.png).


## <a name="next-steps"></a>Étapes suivantes

* Découvrez les [concepts](luis-concept-keys.md) relatifs aux clés de création et de runtime.
* Découvrez [comment affecter des clés](luis-how-to-azure-subscription.md) et ajouter des [contributeurs](luis-how-to-collaborate.md).
