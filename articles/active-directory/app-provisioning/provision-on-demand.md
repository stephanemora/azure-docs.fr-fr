---
title: Approvisionner un utilisateur à la demande à l’aide de Azure Active Directory
description: Forcer la synchronisation
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85297526"
---
# <a name="on-demand-provisioning"></a>Approvisionnement à la demande
L’approvisionnement à la demande vous permet de configurer un utilisateur dans une application en quelques secondes. Vous pouvez utiliser la fonctionnalité pour résoudre rapidement les problèmes de configuration, valider les expressions que vous avez définies, tester les filtres d’étendue, et bien plus encore. 

## <a name="how-to-use-on-demand-provisioning"></a>Utilisation de l’approvisionnement à la demande 

1. Connectez-vous au **portail Azure**.
2. Accédez à **Applications d’entreprise**.
3. Sélectionnez votre application et accédez à la page de configuration de l’approvisionnement.
4. Configurez l’approvisionnement en fournissant vos informations d’identification d’administrateur.
5. Cliquez sur **approvisionnement à la demande**.
6. Recherchez un utilisateur par prénom, nom, nom d’affichage, nom d’utilisateur principal ou adresse de messagerie.
7. Sélectionnez approvisionner au bas de la page.

## <a name="understanding-the-provisioning-steps"></a>Compréhension des étapes d’approvisionnement
La fonctionnalité d’approvisionnement à la demande tente d’afficher les étapes que le service d’approvisionnement effectue lors de l’approvisionnement d’un utilisateur. Il existe généralement cinq étapes pour approvisionner un utilisateur, et une ou plusieurs des étapes ci-dessous s’affichent dans l’expérience d’approvisionnement à la demande.

### <a name="step-1-test-connection"></a>Étape 1 : Tester la connexion
Le service d’approvisionnement tente d’autoriser l’accès à l’application cible en faisant une requête pour un « utilisateur test ». Le service d’approvisionnement attend une réponse indiquant qu’il est autorisé à poursuivre les étapes de configuration. Cette étape s’affiche uniquement en cas de défaillance de l’étape. Elle n’apparaît pas dans l’expérience d’approvisionnement à la demande lorsque l’étape est réussie. 

**Conseils de dépannage**
* Vérifiez que vous avez fourni des informations d’identification valides à l’application cible, telles que le jeton secret et l’URL du locataire. Les informations d’identification requises varient selon l’application. Vous trouverez des didacticiels de configuration détaillés [ici](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Assurez-vous que l’application cible prend en charge le filtrage sur le ou les attributs correspondants définis dans le panneau des mappages d’attributs. Vous devrez peut-être vérifier la documentation de l’API fournie par le développeur de l’application pour comprendre les filtres qu’ils prennent en charge.  
* Pour les applications SCIM, vous pouvez utiliser un outil tel que poster pour vous assurer que l’application répond aux demandes d’autorisation attendues par le service d’approvisionnement Azure AD s’attend. Vous trouverez un exemple de requête [ici](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3).

### <a name="step-2-import-user"></a>Étape 2 : Importer l'utilisateur
Ensuite, le service d’approvisionnement récupère l’utilisateur à partir du système source. Les attributs utilisateur que le service récupère sont utilisés ultérieurement pour évaluer si l’utilisateur se trouve dans la portée de l’approvisionnement, pour vérifier si le système cible est un utilisateur existant et pour déterminer les attributs d’utilisateur à exporter vers le système cible. 

**Afficher les détails**

La section afficher les détails affiche les propriétés de l’utilisateur qui ont été importées depuis le système source (par exemple, Azure AD).

**Conseils de dépannage**
* L’importation de l’utilisateur peut échouer lorsque l’attribut correspondant est manquant sur l’objet utilisateur dans le système source. Vous pouvez résoudre ce problème en mettant à jour l’objet utilisateur avec une valeur pour l’attribut correspondant ou en modifiant l’attribut correspondant dans votre configuration d’approvisionnement.  
* Si un attribut attendu est manquant dans la liste importée, assurez-vous que l’attribut a une valeur sur l’objet utilisateur dans le système source. Actuellement, le service d’approvisionnement ne prend pas en charge l’approvisionnement des attributs Null. 
* Vérifiez que la page de mappage de l’attribut de configuration de l’approvisionnement contient l’attribut que vous attendez. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Étape 3 : Déterminer si l'utilisateur se trouve dans l'étendue
Ensuite, le service d’approvisionnement détermine si l’utilisateur se trouve dans l’[étendue](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) pour l’approvisionnement. Le service prend en compte des aspects tels que le fait que l’utilisateur soit affecté à l’application, que l’étendue soit définie sur synchronisation affectée ou sur tout synchroniser ainsi que les filtres d’étendue définis dans votre configuration d’approvisionnement.  

**Afficher les détails**

La section afficher les détails affiche les conditions d’étendue qui ont été évaluées. Vous pouvez voir une ou plusieurs des propriétés suivantes :
* **Actif dans le système source** indique que la propriété de l’utilisateur est définie sur la valeur true dans Azure AD.
* **Affecté à l’application** indique que l’utilisateur est affecté à l’application dans Azure AD
* **Scope sync all (Étendue tout synchroniser)** indique que la configuration de l’étendue autorise tous les utilisateurs et les groupes du locataire.
* **User has required role (Rôle obligatoire de l’utilisateur)** indique que l’utilisateur dispose des rôles nécessaires pour être approvisionné dans l’application. 
* **Filtres d’étendue** s’affiche également si vous avez défini des filtres d’étendue pour votre application. Le filtre sera affiché au format suivant : {titre de filtre d’étendue} {attribut de filtre d’étendue} {opérateur de filtre d’étendue} {valeur de filtre d’étendue}. 

**Conseils de dépannage**
* Vérifiez que vous avez défini un rôle d’étendue valide. Par exemple, évitez d’utiliser l’opérateur [« Supérieur à »](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) avec une valeur non entière. 
* Si l’utilisateur n’a pas le rôle nécessaire, passez en revue les conseils décrits [ici](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role). 

### <a name="step-4-match-user-between-source-and-target"></a>Étape 4 : Faire correspondre l'utilisateur entre la source et la cible
Au cours de cette étape, le service tente de faire correspondre l’utilisateur qui a été récupéré à l’étape d’importation avec un utilisateur dans le système cible. 

**Afficher les détails**

La page afficher les détails affiche les propriétés du ou des utilisateurs qui ont été mis en correspondance dans le système cible. Les propriétés que vous voyez dans le volet contextuel varient comme suit :
* Si aucun utilisateur ne correspond au système cible, aucune propriété n’est visible.
* Si un utilisateur correspond au système cible, vous verrez les propriétés de l’utilisateur correspondant depuis le système cible.
* Si plusieurs utilisateurs sont mis en correspondance, vous verrez les propriétés des deux utilisateurs correspondants.
* Si plusieurs attributs correspondants font partie de vos mappages d’attributs, chaque attribut correspondant est évalué de manière séquentielle et les utilisateurs correspondants sont affichés. 

**Détails de la résolution des problèmes**
* Le service d’approvisionnement ne peut pas faire correspondre de manière unique un utilisateur de la source à un utilisateur dans la cible. Cela peut être résolu en veillant à ce que l’attribut correspondant soit unique. 
* Assurez-vous que l’application cible prend en charge le filtrage sur le ou les attributs définis dans le panneau des mappages d’attributs.  

### <a name="step-5-perform-action"></a>Étape 5 : Exécuter l’action
Enfin, le service d’approvisionnement effectue une action telle que créer, mettre à jour, supprimer ou ignorer l’utilisateur. 

**Afficher les détails**

La section afficher les détails affiche les attributs qui ont été modifiés dans l’application cible. Il s’agit de la sortie finale de l’activité du service d’approvisionnement et des attributs qui ont été exportés. Si cette étape échoue, les attributs affichés représentent les attributs que le service d’approvisionnement a tenté de modifier.  

**Conseils de dépannage**
* Les échecs d’exportation des modifications peuvent varier considérablement. Consultez la [documentation](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) relative aux journaux d’approvisionnement pour connaître les défaillances courantes.


## <a name="frequently-asked-questions"></a>Forum aux questions
**Devez-vous désactiver l’approvisionnement pour utiliser l’approvisionnement à la demande ?** Pour les applications qui utilisent un jeton de porteur à long terme ou un nom d’utilisateur et un mot de passe pour l’autorisation, aucune étape supplémentaire n’est requise. Les applications qui utilisent OAuth pour l’autorisation nécessitent actuellement que le travail d’approvisionnement soit arrêté avant d’utiliser l’approvisionnement à la demande. Les applications telles que G suite, Box, Workplace by Facebook et Slack appartiennent à cette catégorie. Le travail est en cours pour permettre l’exécution de l’approvisionnement à la demande pour toutes les applications, sans qu’il soit nécessaire d’arrêter l’approvisionnement. 

**Quelle est la durée de l’approvisionnement à la demande ?** Cela prend généralement moins de 30 secondes. 

## <a name="known-limitations"></a>Limites connues
Il existe actuellement quelques limitations connues. Publiez sur [UserVoice](https://aka.ms/appprovisioningfeaturerequest) afin de mieux hiérarchiser les améliorations à apporter à l’étape suivante. Notez que ces limitations sont spécifiques à la fonctionnalité d’approvisionnement à la demande. Consultez le didacticiel de l’application pour savoir si une application prend en charge l’approvisionnement des groupes, des suppressions, etc. 

* Les applications Workday, AWS et SuccessFactors ne prennent pas en charge l’approvisionnement à la demande.
* L’approvisionnement à la demande des groupes et des rôles n’est pas prise en charge.
* La désactivation ou la suppression d’utilisateurs et de groupes n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes d’approvisionnement](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
