---
title: Conditions d’utilisation d’Azure Active Directory | Microsoft Docs
description: Les conditions d’utilisation d’Azure AD vous permettent, ainsi qu’à votre entreprise, de définir les conditions d’utilisation que doivent accepter les utilisateurs des services Azure AD.
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: billmath
ms.openlocfilehash: 428d70474ba928a9e0c774aeb16395ef6a4cea2e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192953"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Fonctionnalité Conditions d’utilisation d’Azure Active Directory
La fonctionnalité Conditions d’utilisation d’Azure AD offre aux organisations une méthode simple pour présenter des informations aux utilisateurs finaux.  Cette présentation permet de garantir que les utilisateurs voient les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité.

Le contenu proposé par la fonctionnalité Conditions d’utilisation d’Azure AD est au format PDF.   Libre à vous de choisir le contenu de ce PDF. Vous pouvez par exemple utiliser le document d’un contrat existant, ce qui vous permet de collecter les contrats des utilisateurs finaux quand ils se connectent.  Vous pouvez utiliser les conditions d’utilisation pour des applications ou des groupes d’utilisateurs. Vous pouvez aussi avoir plusieurs conditions d’utilisation pour des finalités différentes.

Le reste de ce document explique comment utiliser les conditions d’utilisation d’Azure AD.  

## <a name="why-use-azure-ad-terms-of-use"></a>À quoi servent les conditions d’utilisation d’Azure AD ?
Vous avez des difficultés à faire accepter vos conditions d’utilisation à vos employés ou à vos invités avant de leur octroyer l’accès à vos applications ? Vous avez besoin d’aide pour savoir qui a accepté les conditions d’utilisation de l’entreprise et qui les a refusées ?  La fonctionnalité Conditions d’utilisation d’Azure AD offre aux organisations une méthode simple pour présenter des informations aux utilisateurs finaux.  Cette présentation permet de garantir qu’ils voient les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité.

Vous pouvez utiliser la fonctionnalité Conditions d’utilisation d’Azure Active Directory dans les scénarios suivants :
-   Conditions d’utilisation générales pour tous les utilisateurs de votre organisation.
-   Conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, docteurs/infirmières ou employés nationaux/internationaux dans des [groupes dynamiques](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-   Conditions d’utilisation spécifiques en fonction de l’accès aux applications à fort impact commercial, comme Salesforce.


## <a name="prerequisites"></a>Prérequis

Pour configurer les conditions d’utilisation d’Azure AD, effectuez les étapes suivantes :

1. Connectez-vous à Azure AD en tant qu’administrateur général, administrateur de la sécurité ou administrateur à accès conditionnel de l’annuaire pour lequel vous souhaitez configurer les conditions d’utilisation d’Azure AD.
2. Vérifiez que l’annuaire est associé à un abonnement Azure AD Premium P1, P2, EMS E3 ou EMS E5.  Si ce n’est pas le cas, [abonnez-vous à Azure AD Premium](active-directory-get-started-premium.md) ou [optez pour un essai](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Affichez le tableau de bord des conditions d’utilisation d’Azure AD à l’adresse [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>Les contrôles de stratégie d’accès conditionnel (y compris les conditions d’utilisation) ne peuvent pas être appliqués sur les comptes de service.  Nous vous recommandons d’exclure tous les comptes de service de la stratégie d’accès conditionnel.

## <a name="add-company-terms-of-use"></a>Ajouter les conditions d’utilisation de l’entreprise
Après avoir finalisé vos conditions d’utilisation, effectuez les étapes suivantes pour les ajouter.

### <a name="to-add-terms-of-use"></a>Pour ajouter des conditions d’utilisation
1. Accédez au tableau de bord à l’adresse [https://aka.ms/catou](https://aka.ms/catou)
2. Cliquez sur Ajouter.</br>
![Ajouter des conditions d’utilisation](media/active-directory-tou/tou12.png)
3. Entrer le **nom** des conditions d’utilisation
4. Entrez **Nom d’affichage**.  L’en-tête correspond à ce que les utilisateurs voient quand ils se connectent.
5. **Accédez** au fichier PDF finalisé de vos conditions et sélectionnez-le.  Il est recommandé d’utiliser une taille de police de 24.
6. **Sélectionnez** la langue des conditions d’utilisation.  L’option de langue vous permet de charger plusieurs conditions d’utilisation, chacune dans une langue différente.  La version des conditions d’utilisation affichée pour l’utilisateur final dépend des préférences de son navigateur.
7. Activez ou désactivez l’option **Demander aux utilisateurs d’étendre les conditions d’utilisation**.  Si cette option est activée, les utilisateurs finaux seront tenus de visualiser les conditions d’utilisation avant de les accepter.
8. Dans **Accès conditionnel**, vous pouvez **appliquer** les conditions d’utilisation chargées en sélectionnant un modèle dans la liste déroulante ou une stratégie d’accès conditionnel personnalisée.  Les stratégies d’accès conditionnel personnalisées permettent de définir des conditions d’utilisation plus précises, jusqu’au niveau d’une application cloud ou d’un groupe d’utilisateurs.  Pour plus d’informations, consultez [Configuration de stratégies d’accès conditionnel personnalisées](active-directory-conditional-access-best-practices.md).
9. Cliquez sur **Créer**.
10. Si vous avez sélectionné un modèle d’accès conditionnel personnalisé, un nouvel écran s’affiche pour vous permettre de personnaliser la stratégie d’accès conditionnel.
11. Vos nouvelles conditions d’utilisation doivent désormais s’afficher.</br>

![Ajouter des conditions d’utilisation](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Supprimer des conditions d’utilisation
Pour supprimer d’anciennes conditions d’utilisation, effectuez les étapes suivantes :

### <a name="to-delete-terms-of-use"></a>Pour supprimer des conditions d’utilisation
1. Accédez au tableau de bord à l’adresse [https://aka.ms/catou](https://aka.ms/catou)
2. Sélectionnez les conditions d’utilisation à supprimer.
3. Cliquez sur **Supprimer**.
4. Vos nouvelles conditions d’utilisation doivent désormais ne plus s’afficher.


## <a name="viewing-current-user-status"></a>Affichage de l’état utilisateur actuel
Vous remarquerez que vos conditions d’utilisation affichent le nombre d’utilisateurs les ayant acceptées et les ayant refusées.

![Événement d’audit](media/active-directory-tou/tou15.png)

Vous pouvez cliquer sur les nombres sous **acceptées** ou **refusées** pour afficher l’état actuel des utilisateurs.

![Événement d’audit](media/active-directory-tou/tou16.png)

## <a name="audit-terms-of-use"></a>Auditer les conditions d’utilisation
Si vous souhaitez afficher l’historique des acceptations et des refus en plus de l’état actuel, la fonctionnalité Conditions d’utilisation d’Azure AD propose une fonction d’audit simple à utiliser.  Cette fonction d’audit vous permet de voir qui a accepté vos conditions d’utilisation, et à quelle date.  

Vous pouvez utiliser la fonction d’audit de deux façons, selon ce que vous souhaitez faire.  


Pour démarrer l’audit, procédez comme suit :

### <a name="to-audit-terms-of-use"></a>Pour auditer les conditions d’utilisation
1. Accédez au tableau de bord à l’adresse [https://aka.ms/catou](https://aka.ms/catou)
2. Cliquez sur Afficher les journaux d’audit.</br>
![Événement d’audit](media/active-directory-tou/tou8.png)
3.  Sur l’écran des journaux d’audit Azure AD, vous pouvez filtrer les informations à l’aide des menus déroulants pour cibler des éléments spécifiques du journal d’audit.
[Événement d’audit](media/active-directory-tou/tou9.png)
4.  Vous pouvez également télécharger les informations dans un fichier .csv en vue d’une utilisation locale.

## 

## <a name="what-users-see"></a>Ce que voient les utilisateurs
Les utilisateurs dans l’étendue voient ce qui suit une fois les conditions d’utilisation créées et appliquées.  Ces écrans s’affichent durant la procédure de connexion.
-   Il est recommandé d’utiliser une police de taille 24 dans le document PDF.
![Événement d’audit](media/active-directory-tou/tou10.png)
-   L’écran apparaît comme suit sur les mobiles.</br></br>
![Événement d’audit](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>Vérifier les conditions d’utilisation
Vous pouvez consulter et vérifier les conditions d’utilisation qu’ils ont acceptées.  Pour consulter les conditions d’utilisation, suivez la procédure ci-après :

1. Allez à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com) et connectez-vous.
2. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil** dans la liste déroulante.
![Profil](media/active-directory-tou/tou14.png)

3. Dans votre profil, cliquez sur **Vérifier les conditions d’utilisation**.
![Événement d’audit](media/active-directory-tou/tou13a.png)

4.  C’est là que vous pouvez consulter les conditions d’utilisation que vous avez acceptées. 

## <a name="removing-users-from-an-active-terms-of-use"></a>Suppression d’utilisateurs des conditions d’utilisation actives

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu.  Au bout de 30 jours, cet utilisateur est définitivement supprimé.  En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](active-directory-users-restore.md) avant que cette période arrive à son terme.  Une fois qu’un utilisateur a été définitivement supprimé, les données le concernant sont par la suite supprimées des conditions d’utilisation actives.  Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.



## <a name="additional-information"></a>Informations supplémentaires
Tenez compte des points suivants pour faciliter la mise en place de conditions d’utilisation.

>[!IMPORTANT]
> Les utilisateurs dans l’étendue doivent se déconnecter et se reconnecter pour satisfaire aux exigences d’une nouvelle stratégie si :
> - une stratégie d’accès conditionnel est activée sur les conditions d’utilisation ;
> - une deuxième instance des conditions d’utilisation est créée.
>
>Les stratégies d’accès conditionnel prennent effet immédiatement. Dans ce cas, l’administrateur commence à voir des « clouds tristes » ou des « problèmes liés aux jetons Azure AD ». L’administrateur doit se déconnecter et se reconnecter pour satisfaire aux exigences de la nouvelle stratégie.





## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Comment déterminer si un utilisateur a accepté les conditions d’utilisation et quand ?**</br>
R : Vous pouvez cliquer sur le nombre figurant sous « acceptées » en regard de vos conditions d’utilisation.  Pour plus d’informations, consultez [Affichage de l’état utilisateur actuel](#viewing-current-user-status).  En outre, un utilisateur qui accepte les conditions d’utilisation est consigné dans le journal d’audit. Vous pouvez rechercher le journal d’audit Azure AD pour voir les résultats.  

**Q : Si vous changez les conditions d’utilisation, les utilisateurs doivent-ils les accepter à nouveau ?**</br>
R : Oui. Si un administrateur change les conditions d’utilisation, les utilisateurs doivent les accepter à nouveau.

**Q : Les conditions d’utilisation prennent-elles en charge plusieurs langues ?**</br>
R. : Oui.  Actuellement, un administrateur peut configurer 18 langues différentes pour une seule condition d’utilisation. 

**Q : À quel moment les conditions d’utilisation sont-elles déclenchées ?**</br>
R : Les conditions d’utilisation sont déclenchées durant la procédure de connexion.

**Q : Quelles sont les applications pouvant être ciblées par des conditions d’utilisation ?**</br>
R : Vous pouvez créer une stratégie d’accès conditionnel sur les applications d’entreprise à l’aide de l’authentification moderne.  Pour plus d’informations, consultez [Applications d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**Q : Puis-je ajouter plusieurs conditions d’utilisation à une application ou un utilisateur donné ?**</br>
R : Oui. Pour cela, créez plusieurs stratégies d’accès conditionnel ciblant ces groupes ou applications. Si un utilisateur est concerné par plusieurs conditions d’utilisation, il accepte ces différentes conditions d’utilisation une par une.
 
**Q : Que se passe-t-il si un utilisateur refuse les conditions d’utilisation ?**</br>
R : L’utilisateur n’a pas accès à l’application. Il doit se reconnecter et accepter les conditions pour se voir octroyer des droits d’accès.
