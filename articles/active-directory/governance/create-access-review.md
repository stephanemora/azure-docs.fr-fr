---
title: Créer une révision d’accès des groupes et applications - Azure AD
description: Découvrez comment créer une révision d’accès pour les membres de groupes ou pour l’accès aux applications dans les révisions d’accès Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d97efdafdaca73a65dfe0013d36d83d3b681809c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052099"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Créer une révision d’accès des groupes et applications dans les révisions d’accès Azure AD
 
L’accès aux groupes et aux applications pour les employés et les invités change au fil du temps. Afin de réduire les risques associés aux affectations d’accès obsolètes, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer des révision des accès pour les membres de groupes ou pour l’accès aux applications. 

Les propriétaires de groupes de sécurité et de Microsoft 365 peuvent également utiliser Azure AD pour créer des révisions d’accès pour les membres du groupe tant que l’administrateur général ou utilisateur active le paramètre via le panneau Paramètres de révision d’accès (préversion). Pour plus d’informations sur ces scénarios, consultez [Gérer les révision d’accès](manage-access-review.md) 
 
Vous pouvez regarder une courte vidéo sur l’activation des révisions d’accès :
 
>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]
 
Cet article explique comment créer une ou plusieurs révisions d’accès pour les membres de groupes ou pour l’accès aux applications.
 
## <a name="prerequisites"></a>Prérequis
 
- Azure AD Premium P2
-  Administrateur général, administrateur d’utilisateurs ou administrateur de gouvernance des identités pour créer des révisions sur les groupes ou les applications. 
-  Les administrateurs généraux et les administrateurs de rôle privilégié peuvent créer des révisions sur les groupes auxquels des rôles peuvent être attribués. Consultez [Utiliser des groupes Azure AD pour gérer les attributions de rôles](../roles/groups-concept.md)
- (Préversion) Microsoft 365 et Propriétaire du groupe de sécurité
 
Pour plus d’informations, consultez [Exigences des licences](access-reviews-overview.md#license-requirements).
 
## <a name="create-one-or-more-access-reviews"></a>Créer une ou plusieurs révisions d’accès
 
1. Connectez-vous au portail Azure et ouvrez la page [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
2. Dans le menu de gauche, cliquez sur **Révisions d’accès**.
 
3. Cliquez sur **Nouvelle révision d’accès** pour créer une révision d’accès.
 
    ![Volet des révisions d’accès dans Gouvernance des identités](./media/create-access-review/access-reviews.png)
 
4. À l’**Étape 1 : Sélectionner les éléments à réviser**, sélectionnez la ressource que vous souhaitez réviser.
 
    ![Créer une révision d’accès - Nom et description de la révision](./media/create-access-review/select-what-review.png)
 
5. Si vous avez sélectionné **Équipes + groupes** à l’étape 1, vous avez deux options à l’étape 2.
   - **Tous les groupes Microsoft 365 avec des utilisateurs invités.** Sélectionnez cette option si vous souhaitez créer des révisions périodiques sur tous vos utilisateurs invités dans l’ensemble des équipes Microsoft et des groupes Microsoft 365 de votre organisation. Il est important de noter que les groupes dynamiques et les groupes assignables par rôle ne sont pas inclus. Vous pouvez également choisir d’exclure des groupes individuels en cliquant sur « Sélectionner le(s) groupe(s) à exclure ». 
   - **Sélectionner les équipes + groupes.** Sélectionnez cette option si vous souhaitez spécifier un ensemble limité d’équipes et/ou de groupes à réviser. Après avoir cliqué sur cette option, vous verrez une liste de groupes à droite parmi lesquels vous pourrez choisir.
 
     ![Équipes et groupes](./media/create-access-review/teams-groups.png)
 
 
6. Si vous avez sélectionné **Applications** à l’étape 1, vous pouvez sélectionner une ou plusieurs applications à l’étape 2.
 
    >[!NOTE]
    > La sélection de plusieurs groupes et/ou applications entraîne la création de plusieurs révisions d’accès. Par exemple, si vous sélectionnez cinq groupes à réviser, cela donnera lieu à cinq révisions d’accès distinctes.
 
   ![Interface affichée si vous avez choisi des applications plutôt que des groupes](./media/create-access-review/select-application-detailed.png)
 
7. Ensuite, à l’étape 3, vous pouvez sélectionner l’étendue de la révision. Vos options sont les suivantes :
   - **Utilisateurs invités uniquement.** En sélectionnant cette option, vous limitez la révision d’accès aux seuls utilisateurs invités B2B d’Azure AD dans votre répertoire.
   - **Tout le monde.** La sélection de cette option permet d’étendre la révision d’accès à tous les objets utilisateur associés à la ressource.
 
    >[!NOTE]
    > Si vous avez sélectionné Tous les groupes Microsoft 365 avec des utilisateurs invités à l’étape 2, la seule option consiste à réviser les utilisateurs invités à l’étape 3.
 
8. Cliquez sur **Suivant : Révisions**.
 
9. Dans la section **Sélectionner les réviseurs**, sélectionnez une ou plusieurs personnes pour effectuer les révisions d’accès. Vous pouvez choisir :
    - **Propriétaire(s) de groupe**. (Disponible uniquement lors de la révision d’une équipe ou d’un groupe).
    - **Utilisateur(s) ou groupe(s) sélectionné(s)** .
    - **Les utilisateurs révisent leur propre accès**.
    - **Managers des utilisateurs.**
    Si vous choisissez **Managers des utilisateurs** ou **Propriétaires de groupes**, vous avez également la possibilité de spécifier un réviseur de secours. Les réviseurs de secours sont invités à effectuer une révision lorsque l’utilisateur n’a pas de manager spécifié dans le répertoire ou que le groupe n’a pas de propriétaire.
 
    ![Nouvelle révision d’accès](./media/create-access-review/new-access-review.png)
 
10. Dans la section **Spécifier la récurrence de la révision**, vous devez spécifier quatre sélections différentes :
- **Durée** : définit la durée pendant laquelle une révision sera ouverte à la contribution des réviseurs. 
- **Examen de la périodicité** : fréquence d’une révision, par exemple **Une fois, hebdomadaire, mensuelle, trimestrielle, semestrielle et annuelle**.
- **Date de début** : date de début de la série de révisions.
-  **Date de fin** : date de fin de la série de révisions. Vous pouvez spécifier qu’elle ne se termine **jamais**, qu’elle **se termine à une date spécifique** ou qu’elle **se termine après le nombre d’occurrences**.

 
    ![Choisir la fréquence de la révision](./media/create-access-review/frequency.png)
 
11. Cliquez sur le bouton **Suivant : Paramètres** en bas de la page.
 
12. Dans **Paramètres une fois l’opération terminée**, vous pouvez spécifier ce qui se produit une fois la révision terminée.
 
    ![Créer une révision d’accès : paramètres une fois l’opération terminée](./media/create-access-review/upon-completion-settings-new.png)
 
    Si vous souhaitez que l’accès des utilisateurs refusés soit supprimé automatiquement une fois la durée de la vérification terminée, définissez **Appliquer automatiquement les résultats à la ressource** sur Activer. Si désactivé, vous allez devoir appliquer manuellement les résultats lorsque la révision se termine. Consultez [Gérer les révisions d’accès](manage-access-review.md) pour en savoir plus sur l’application des résultats de la révision.
    
     Utilisez **Si les réviseurs ne répondent pas** pour spécifier ce qui se passe pour les utilisateurs non vérifiés par un réviseur quelconque au cours de la révision. Ce paramètre n’affecte pas les utilisateurs qui ont été vérifiés par un réviseur.
 
    - **Aucune modification** : laisser l’accès de l’utilisateur inchangé
    - **Supprimer l’accès** : supprimer l’accès de l’utilisateur
    - **Approuver l’accès** : approuver l’accès de l’utilisateur
    - **Accepter les recommandations** : accepter la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur
 
     Utilisez l'**Action à appliquer sur les utilisateurs invités refusés**, une option disponible uniquement si la révision d’accès est limitée à inclure uniquement les utilisateurs invités, pour spécifier ce qui arrive aux utilisateurs invités s’ils sont refusés par un réviseur ou par le paramètre **Si les réviseurs ne répondent pas**.
    - **Supprimer l’appartenance de l’utilisateur à la ressource** supprime l’accès de l’utilisateur invité refusé au groupe ou à l’application en cours de révision. Il pourra toujours se connecter au locataire et ne perdra pas d’autre accès.
    - **Empêcher l’utilisateur de se connecter pendant 30 jours, puis supprimer l’utilisateur du locataire** empêche un utilisateur invité refusé de se connecter au locataire, même s’il a accès à d’autres ressources. Si cette action a été effectuée par erreur, les administrateurs peuvent réactiver l’accès de l’utilisateur invité dans les 30 jours suivant la désactivation de l’utilisateur invité. Si aucune action n’est effectuée sur l’utilisateur invité désactivé après 30 jours, il est supprimé du locataire.
 
    Pour en savoir plus sur les meilleures pratiques pour la suppression des utilisateurs invités qui n’ont plus accès aux ressources de votre organisation, consultez [Utiliser Azure AD Identity Governance pour examiner et supprimer les utilisateurs externes qui n’ont plus accès aux ressources](access-reviews-external-users.md).
 
    > [!NOTE]
    > L’action à appliquer sur les utilisateurs invités refusés n’est pas configurable sur les révisions dont la portée est plus large que celle des utilisateurs invités. Elle n’est pas non plus configurable pour les révisions de **Tous les groupes Microsoft 365 avec des utilisateurs invités.** Lorsqu’elle n’est pas configurable, l’option par défaut de suppression de l’appartenance de l’utilisateur à la ressource est utilisée sur les utilisateurs refusés.
 
13. Utilisez **(Préversion) à la fin de la révision, envoyer une notification à** pour envoyer des notifications à d’autres utilisateurs ou groupes avec des mises à jour de fin. Cette fonctionnalité permet d’informer les parties prenantes autres que le créateur de la révision de la progression de la révision. Pour utiliser cette fonctionnalité, sélectionnez **Sélectionner un ou plusieurs utilisateurs ou groupes** et ajoutez un utilisateur ou groupe supplémentaire pour lequel vous souhaitez recevoir l’état d’achèvement.
 
14. Dans **Activer l’assistance aux décisions de révision**, indiquez si vous souhaitez que votre réviseur reçoive des recommandations au cours du processus de révision. Lorsque cette option est activée, il est recommandé d'approuver les utilisateurs qui se sont connectés au cours des 30 derniers jours, tandis que les utilisateurs qui ne se sont pas connectés au cours des 30 derniers jours sont recommandés d'être refusés.

> [!NOTE]
> Si vous créez une révision d’accès basée sur les applications, vos recommandations seront basées sur la période d'intervalle de 30 jours en fonction de la date de la dernière connexion de l'utilisateur à l'application plutôt qu'au locataire.

![Activer les options d’assistance à la décision](./media/create-access-review/helpers.png)

15. Dans la section **Paramètres avancés**, vous pouvez choisir les options suivantes :
    - Définissez **Justification obligatoire** sur **Activer** afin d’exiger que le réviseur indique un motif d’approbation ou de refus.
    - Définissez **Notifications par e-mail** sur **Activer** pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision est terminée.
    - Définissez **Rappels** sur **Activer** pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours à tous les réviseurs. Les réviseurs recevront les rappels à mi-parcours, qu’ils aient ou non terminé la révision.
    - Le contenu de l’e-mail envoyé aux réviseurs est généré automatiquement en fonction des détails de la révision, comme le nom de la révision, le nom de la ressource, la date d’échéance, etc. Si vous souhaitez communiquer des informations supplémentaires telles que des instructions ou des coordonnées, vous pouvez les ajouter dans la section **Contenu supplémentaire pour l’e-mail du réviseur**. Les informations que vous entrez sont incluses dans les e-mails d’invitation et de rappel envoyés aux réviseurs désignés. La section mise en surbrillance dans l’image ci-dessous indique où ces informations sont affichées.
 
      ![Contenu supplémentaire pour le réviseur](./media/create-access-review/additional-content-reviewer.png)
 
16. Cliquez sur **Suivant : Réviser + créer** pour passer à la page suivante.
 
  ![Écran de création d’une révision](./media/create-access-review/create-review.png)
17. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.
 
18. Vérifiez les informations, puis sélectionnez **Créer**.
 
    
 
## <a name="allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview"></a>Autoriser les propriétaires de groupes à créer et à gérer des révisions d’accès de leurs groupes (préversion)
 
Rôle prérequis : Administrateur général ou d’utilisateurs
 
1. Connectez-vous au portail Azure et ouvrez la page [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
1. Dans le menu de gauche, sous **Révisions d’accès**, **Paramètres**.
 
1. Dans la page **Déléguer qui peut créer et gérer des révisions d’accès**, définissez **(Préversion) Les propriétaires du groupe peuvent créer et gérer les révisions d’accès des groupes** dont ils disposent, sur **Oui**.
 
    ![Créer des révisions - Autoriser les propriétaires de groupes à effectuer des révisions](./media/create-access-review/group-owners-review-access.png)
 
    > [!NOTE]
    > Par défaut, le paramètre est défini sur **Non**. Il doit donc être mis à jour pour permettre aux propriétaires de groupes de créer et gérer les révisions d’accès.
 
## <a name="start-the-access-review"></a>Démarrer la révision d’accès
 
Une fois que vous avez spécifié les paramètres pour une révision d’accès, cliquez sur **Démarrer**. La révision d’accès s’affiche dans votre liste, avec un indicateur de son état.
 
![Liste des révisions d’accès et de leur état](./media/create-access-review/access-reviews-list.png)
 
Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur montrer les instructions relatives à la [révision d’accès aux groupes ou aux applications](perform-access-review.md). Si votre révision s’adresse à des invités qui doivent réviser leur propre accès, donnez-leur des instructions sur la méthode à suivre pour [réviser leur accès à des groupes ou à des applications](review-your-access.md).
 
Si vous avez attribué le rôle de réviseur à des clients invités, mais que ceux-ci ont refusé l’invitation au locataire, ils ne recevront pas d’e-mails des révisions d’accès, car il est nécessaire d’avoir accepté l’invitation pour pouvoir commencer la révision.

## <a name="update-the-access-review"></a>Mettre à jour la révision d’accès

Après le lancement d’une ou de plusieurs révisions d’accès, vous souhaiterez peut-être modifier ou mettre à jour les paramètres de vos révisions d’accès existantes. Voici quelques scénarios courants que vous pouvez envisager :

- **Mise à jour des paramètres ou des réviseurs** : si une révision d’accès est récurrente, il existe des paramètres distincts sous « Actuel » et sous « Série ». La mise à jour des paramètres ou des réviseurs sous « Actuel » appliquera uniquement les modifications apportées à la révision d’accès actuelle, tandis que la mise à jour des paramètres sous « Série » mettra à jour le paramètre pour toutes les futures révisions.

![Mettre à jour les paramètres de la révision d'accès](./media/create-access-review/current-v-series-setting.png)

- **Ajout et suppression de réviseurs** : Lors de la mise à jour des révisions d’accès, vous pouvez choisir d’ajouter un réviseur de secours en plus du réviseur principal. Les réviseurs principaux peuvent être supprimés lors de la mise à jour d’une révision d’accès. Toutefois, les réviseurs de secours ne peuvent pas être supprimés du fait de la conception.

    > [!Note]
    > Les réviseurs de secours ne peuvent être ajoutés que lorsque le type de réviseur est manager ou propriétaire de groupe. Les réviseurs principaux peuvent être ajoutés lorsque le type de réviseur est un utilisateur sélectionné.

- **Rappel aux réviseurs** : Lors de la mise à jour des révisions d’accès, vous pouvez choisir d’activer l’option de rappel sous Paramètres avancés. Une fois cette option activée, les utilisateurs recevront une notification par e-mail à mi-parcours de la période de révision, qu’ils aient terminé ou non la révision. 

![Rappel des réviseurs](./media/create-access-review/reminder-setting.png)



 
## <a name="next-steps"></a>Étapes suivantes
 
- [Réviser l’accès à des groupes ou à des applications](perform-access-review.md)
- [Réviser son accès à des groupes ou à des applications](review-your-access.md)
- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)


