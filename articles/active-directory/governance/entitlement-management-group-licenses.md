---
title: Tutoriel - Gérer le cycle de vie de vos licences basées sur des groupes dans Azure AD
description: Tutoriel pas à pas permettant de créer un package d’accès pour gérer les licences basées sur des groupes dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: sama
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 08/18/2021
ms.author: sama
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8577da92657086edeb0d901c54693faa3cabab92
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095167"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>Tutoriel : Gérer le cycle de vie de vos licences basées sur des groupes dans Azure AD
 
Avec Azure Active Directory, vous pouvez utiliser des groupes pour gérer les [licences de vos applications](/active-directory/enterprise-users/licensing-groups-assign.md). Vous pouvez rendre la gestion de ce groupe encore plus facile grâce à la gestion des droits d’utilisation. 

*   Configurez des révisions d’accès régulières pour vous assurer que seuls les utilisateurs qui ont besoin des licences sont dans le groupe. 
*   Autoriser d’autres utilisateurs à demander d’appartenir au groupe

Dans ce tutoriel, vous travaillez pour WoodGrove Bank comme administrateur informatique. Vous avez été chargé de créer un package d’accès à votre organisation pour accéder facilement aux licences Office. Vous devez déjà avoir un groupe qui gère vos [licences Office](/active-directory/enterprise-users/licensing-groups-assign.md). Ces utilisateurs nécessitent une révision annuelle et permettent à de nouveaux utilisateurs de demander des licences Office que le responsable devra approuver. Pour utiliser la gestion des droits d’utilisation Azure AD, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5 Pour plus d’informations, consultez [Conditions des licences](entitlement-management-overview.md#license-requirements).
## <a name="step-1-configure-basics-for-your-access-package"></a>Étape 1 : Configurer les bases pour votre package d’accès

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès.

1. Dans le **portail Azure**, dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

2. Dans le menu gauche, cliquez sur **Identity Governance**.

3. Dans le menu gauche, cliquez sur **Packages d’accès**. 

4. Cliquez sur **Nouveau package d’accès**.

5. Sous l’onglet **Informations de base**, entrez le nom **Licences Office** et la description **Accès aux licences des logiciels Office**.

6. Vous pouvez laisser la liste déroulante **Catalogue** définie sur **Général**.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>Étape 2 : Configurer les ressources pour votre package d’accès

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Rôles des ressources**.

2. Sous cet onglet, sélectionnez les ressources et le rôle de ressource à inclure dans le package d’accès. Dans cet exemple de scénario, vous pouvez cliquer sur **Groupes et équipes** et rechercher votre groupe auquel a été affecté [Licences Office](/active-directory/enterprise-users/licensing-groups-assign.md).

3. Dans la liste déroulante **Rôle**, sélectionnez **Membre**.

## <a name="step-3-configure-requests-for-your-access-package"></a>Étape 3 : Configurer les demandes pour votre package d’accès

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Requêtes**.

2. Sous cet onglet, vous créez une stratégie de demande. Une *stratégie* définit les règles ou barrières mises en place pour accéder à un package d’accès. Vous créez une stratégie qui permet à un utilisateur spécifique de l’annuaire de ressources de demander ce package d’accès.

3. Dans la section **Utilisateurs qui peuvent demander l’accès**, cliquez sur **Pour les utilisateurs de votre annuaire** et sélectionnez **Tous les membres (à l’exclusion des invités)** . Ainsi, seuls les membres de votre annuaire seront en mesure de demander des licences Office.

4. Assurez-vous que l’option **Exiger l’approbation** est définie sur **Oui**.

5. Pour **Exiger la justification du demandeur**, laissez sur **Oui**.

6. Pour **Combien de phases**, laissez sur **1**.

7. Pour **Approbateur**, sélectionnez **Gestionnaire comme approbateur**. Cette option permet au gestionnaire du demandeur d’approuver la demande. Si le système ne parvient pas à trouver le responsable, vous pouvez sélectionner une autre personne comme approbateur de secours.

8. Pour **La décision doit être prise dans combien de jours ?** , laissez sur **14**.

9. Pour **Exiger la justification de l’approbateur**, laissez sur **Oui**.

10. Définissez **Activer les nouvelles demandes et les attributions** sur **Oui** afin que ce package d’accès puisse être demandé dès qu’il sera créé.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>Étape 4 : Configurer les informations du demandeur pour votre package d’accès

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Informations sur le demandeur**.

2. Sur cet écran, vous pouvez poser plus de questions pour collecter plus d’informations sur votre demandeur. Ces questions sont affichées sur son formulaire de demande et peuvent être définies comme obligatoires ou facultatives. Dans ce scénario, vous n’avez pas été invité à inclure d’informations sur le demandeur pour ce package d’accès, donc vous pouvez laisser ces champs vides.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>Étape 5 : Configurer le cycle de vie pour votre package d’accès

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Cycle de vie**.

2. Dans la section **Expiration**, sélectionnez **Nombre de jours** en regard de **Les attributions de package d’accès expirent**.
    
3. Définissez **Les attributions expirent après** sur **365** jours. Ce champ détermine à quel moment les membres qui ont accès à ce package d’accès devront renouveler leur accès. 

4. Vous pouvez également configurer des **révisions d’accès** pour pouvoir vérifier régulièrement si l’utilisateur a toujours besoin d’accéder au package d’accès. Une révision peut être une auto-révision effectuée par l’utilisateur lui-même ou vous pouvez définir son responsable ou un autre réviseur pour cette tâche. Pour plus d’informations, consultez [Révisions d’accès](entitlement-management-access-reviews-create.md). Pour ce scénario, vous voulez que chaque utilisateur vérifie s’il a toujours besoin d’une licence pour Office chaque année.

    1.  Définissez **Exiger des révisions d’accès** sur **Oui**.
    2.  Vous pouvez laisser **Date de début** à la date du jour. La date de début correspond au démarrage de la campagne de révision d’accès. Une fois qu’une révision d’accès a été créée, vous ne pouvez plus mettre à jour sa date de début.
    3.  Définissez **Fréquence de révision** sur **Annuelle**, car la révision aura lieu une fois par an. Le champ Fréquence de révision vous permet de déterminer la fréquence d’exécution de la campagne de révision d’accès.
    4.  Spécifiez une **Durée (en jours)** .  Le champ Durée est là où vous indiquez le nombre de jours pendant lesquels chaque occurrence de la série de révisions d’accès s’exécutera.
    5.  Pour les **Réviseurs**, sélectionnez **Responsable**.

## <a name="step-6-review-and-create-your-access-package"></a>Étape 6 : Vérifier et créer votre package d’accès

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Vérifier + créer**.

2. Sur cet écran, vous pouvez vérifier la configuration de votre package d’accès avant de le créer. En cas de problème, vous pouvez utiliser les onglets pour naviguer jusqu’à un point spécifique dans l’expérience de création pour apporter des modifications.

3. Une fois que vous êtes content de votre sélection, cliquez sur **Créer**. Après quelques instants, vous devez obtenir une notification indiquant que le package d’accès a été créé.

4. Une fois le package d’accès créé, vous êtes dirigé vers la page **Vue d’ensemble** de votre package d’accès. Vous trouverez le **lien du portail Mon Accès** ici. Copiez le lien et partagez-le avec votre équipe afin qu’elle puisse demander que le package d’accès reçoive des licences pour Office.

## <a name="step-7-clean-up-resources"></a>Étape 7 : Nettoyer les ressources

Dans cette étape, vous pouvez supprimer le package d’accès **Licences Office**. 

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités ou gestionnaire de package d’accès

1. Dans le **portail Azure**, dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

2. Dans le menu gauche, cliquez sur **Identity Governance**.

3. Dans le menu gauche, cliquez sur **Packages d’accès**. 

4. Ouvrez le package d’accès **Licences Office**. 

5. Cliquez sur **Rôles des ressources**.

6. Sélectionnez le groupe que vous avez ajouté à ce package d’accès, puis dans le volet des détails, cliquez sur **Supprimer le rôle de ressource**. Dans le message qui s’affiche, cliquez sur **Oui**.

7. Ouvrez la liste des packages d’accès.

8. Pour **Licences Office**, cliquez sur les points de suspension (...) et sur **Supprimer**. Dans le message qui s’affiche, cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des packages d’accès pour gérer l’accès à d’autres types de ressources comme les applications et les sites. [Tutoriel : Gérer l’accès aux ressources dans la gestion des droits d’utilisation Azure AD](/active-directory/governance/entitlement-management-access-package-first.md)
