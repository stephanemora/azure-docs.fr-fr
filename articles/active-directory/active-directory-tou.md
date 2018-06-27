---
title: Conditions d’utilisation d’Azure Active Directory | Microsoft Docs
description: Les conditions d’utilisation d’Azure AD vous permettent, ainsi qu’à votre entreprise, de définir les conditions d’utilisation que doivent accepter les utilisateurs des services Azure AD.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 06/18/2018
ms.author: rolyon
ms.openlocfilehash: 2919ce1d7c57b7a92420ac11b61503caa1fdd3b0
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267555"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Fonctionnalité Conditions d’utilisation d’Azure Active Directory
La fonctionnalité Conditions d’utilisation d’Azure AD offre aux organisations une méthode simple pour présenter des informations aux utilisateurs finaux. Cette présentation permet de garantir que les utilisateurs voient les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité. Cet article vous indique comment prendre en main la fonctionnalité Conditions d’utilisation d’Azure Active Directory.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Que puis-je faire avec Conditions d’utilisation ?
Cette fonctionnalité vous permet d’effectuer les opérations suivantes :
- Faire accepter vos conditions d’utilisation à vos employés ou à vos invités avant de leur octroyer l’accès à vos applications.
- Présenter les conditions d’utilisation générales pour tous les utilisateurs de votre organisation.
- Présenter les conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, docteurs/infirmières ou employés nationaux/internationaux dans des [groupes dynamiques](active-directory-groups-dynamic-membership-azure-portal.md)).
- Présenter les conditions d’utilisation spécifiques lors de l’accès aux applications à fort impact commercial, comme Salesforce.
- Présenter les conditions d’utilisation dans différentes langues.
- Répertorier qui a accepté ou refusé vos conditions d’utilisation.
- Afficher un journal d’audit de l’activité liée aux conditions d’utilisation.

## <a name="prerequisites"></a>Prérequis
Pour utiliser et configurer les conditions d’utilisation d’Azure AD, vous devez disposer des éléments suivants :

- Abonnement Azure AD Premium P1, P2, EMS E3 ou EMS E5.
    - Si vous ne disposez pas d’un de ces abonnements, vous pouvez [obtenir Azure AD Premium](fundamentals/active-directory-get-started-premium.md) ou [activer un essai d’Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Un des comptes d’administrateur suivants pour le répertoire que vous souhaitez configurer :
    - Administrateur général
    - Administrateur de sécurité
    - Administrateur de l’accès conditionnel

## <a name="terms-of-use-document"></a>Document Conditions d’utilisation

Le contenu proposé par la fonctionnalité Conditions d’utilisation d’Azure AD est au format PDF. Libre à vous de choisir le contenu de ce PDF. Vous pouvez par exemple utiliser le document d’un contrat existant, ce qui vous permet de collecter les contrats des utilisateurs finaux quand ils se connectent. Il est recommandé d’utiliser une taille de police de 24 dans le fichier PDF.

## <a name="add-terms-of-use"></a>Ajouter des conditions d’utilisation
Après avoir finalisé le document relatif à vos conditions d’utilisation, effectuez les étapes suivantes pour les ajouter.

1. Connectez-vous à Azure en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.

1. Accédez aux **conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

    ![Panneau Conditions d’utilisation](media/active-directory-tou/tou-blade.png)

1. Cliquez sur **Nouvelles conditions d’utilisation**.

    ![Ajouter des conditions d’utilisation](media/active-directory-tou/new-tou.png)

1. Entrer le **Nom** des conditions d’utilisation

2. Entrez le **Nom d’affichage**.  Il s’agit de l’en-tête correspondant à ce que les utilisateurs voient quand ils se connectent.

3. **Accédez** au fichier PDF finalisé de vos conditions et sélectionnez-le.

4. **Sélectionnez** la langue des conditions d’utilisation.  L’option de langue vous permet de charger plusieurs conditions d’utilisation, chacune dans une langue différente.  La version des conditions d’utilisation affichée pour l’utilisateur final dépend des préférences de son navigateur.

5. Activez ou désactivez l’option **Demander aux utilisateurs d’étendre les conditions d’utilisation**.  Si cette option est activée, les utilisateurs finaux seront tenus de visualiser les conditions d’utilisation avant de les accepter.

6. Dans **Accès conditionnel**, vous pouvez **appliquer** les conditions d’utilisation chargées en sélectionnant un modèle dans la liste déroulante ou une stratégie d’accès conditionnel personnalisée.  Les stratégies d’accès conditionnel personnalisées permettent de définir des conditions d’utilisation plus précises, jusqu’au niveau d’une application cloud ou d’un groupe d’utilisateurs.  Pour plus d’informations, consultez [Configuration de stratégies d’accès conditionnel personnalisées](active-directory-conditional-access-best-practices.md).

    >[!IMPORTANT]
    >Les contrôles de stratégie d’accès conditionnel (y compris les conditions d’utilisation) ne peuvent pas être appliqués sur les comptes de service.  Nous vous recommandons d’exclure tous les comptes de service de la stratégie d’accès conditionnel.

7. Cliquez sur **Créer**.

8. Si vous avez sélectionné un modèle d’accès conditionnel personnalisé, un nouvel écran s’affiche pour vous permettre de personnaliser la stratégie d’accès conditionnel.

    Vos nouvelles conditions d’utilisation doivent désormais s’afficher.

    ![Ajouter des conditions d’utilisation](media/active-directory-tou/create-tou.png)

## <a name="view-who-has-accepted-and-declined"></a>Voir qui a accepté et refusé
Le panneau des conditions d’utilisation affiche le nombre d’utilisateurs ayant accepté et refusé les conditions d’utilisation. Ce nombre et le nom des personnes ayant accepté/refusé sont stockés pour toute la durée de vie des conditions d’utilisation.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

    ![Événement d’audit](media/active-directory-tou/view-tou.png)

1. Vous pouvez cliquer sur les nombres sous **Accepté** ou **Refusé** pour afficher l’état actuel des utilisateurs.

    ![Événement d’audit](media/active-directory-tou/accepted-tou.png)

## <a name="view-audit-logs"></a>Afficher les journaux d’audit
Si vous souhaitez afficher d’autres activités, la fonctionnalité Conditions d’utilisation d’Azure AD inclut des journaux d’audit. Chaque consentement d’utilisateur déclenche un événement dans les journaux d’audit qui sont stockés pendant 30 jours. Vous pouvez afficher ces journaux dans le portail ou les télécharger sous forme de fichier .csv.

Pour démarrer les journaux d’audit, procédez comme suit :

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

1. Cliquez sur **Afficher les journaux d’audit**.

    ![Événement d’audit](media/active-directory-tou/audit-tou.png)

1. Sur l’écran des journaux d’audit Azure AD, vous pouvez filtrer les informations à l’aide des listes déroulantes pour cibler des éléments spécifiques du journal d’audit.

    ![Événement d’audit](media/active-directory-tou/audit-logs-tou.png)

1. Vous pouvez également cliquer sur **Télécharger** pour télécharger les informations dans un fichier .csv en vue d’une utilisation locale.

## <a name="what-terms-of-use-looks-like-for-users"></a>À quoi ressemblent les conditions d’utilisation pour les utilisateurs ?
Les utilisateurs dans l’étendue voient l’écran suivant au cours de la connexion une fois les conditions d’utilisation créées et appliquées.

![Événement d’audit](media/active-directory-tou/user-tou.png)

L’écran suivant montre comment se présente les conditions d’utilisation sur les appareils mobiles.

![Événement d’audit](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>Comment les utilisateurs peuvent consulter leurs conditions d’utilisation
Vous pouvez consulter et vérifier les conditions d’utilisation qu’ils ont acceptées en procédant comme suit.

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil** dans la liste déroulante.

    ![Profil](media/active-directory-tou/tou14.png)

1. Dans la page de votre profil, cliquez sur **Vérifier les conditions d’utilisation**.

    ![Événement d’audit](media/active-directory-tou/tou13a.png)

1. C’est là que vous pouvez consulter les conditions d’utilisation que vous avez acceptées. 

## <a name="delete-terms-of-use"></a>Supprimer des conditions d’utilisation
Pour supprimer d’anciennes conditions d’utilisation, effectuez les étapes suivantes :

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

1. Sélectionnez les conditions d’utilisation à supprimer.

1. Cliquez sur **Supprimer les conditions d’utilisation**.

1. Dans le message qui s’affiche vous demandant si vous souhaitez continuer, cliquez sur **Oui**.

    ![Ajouter des conditions d’utilisation](media/active-directory-tou/delete-tou.png)

    Vos conditions d’utilisation doivent désormais ne plus s’afficher.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilisateurs supprimés et conditions d’utilisation actives
Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu.  Au bout de 30 jours, cet utilisateur est définitivement supprimé.  En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme.  Une fois qu’un utilisateur a été définitivement supprimé, les données le concernant sont par la suite supprimées des conditions d’utilisation actives.  Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.

## <a name="policy-changes"></a>Modifications de stratégie
Les stratégies d’accès conditionnel prennent effet immédiatement. Dans ce cas, l’administrateur commence à voir des « clouds tristes » ou des « problèmes liés aux jetons Azure AD ». L’administrateur doit se déconnecter et se reconnecter pour satisfaire aux exigences de la nouvelle stratégie.

>[!IMPORTANT]
> Les utilisateurs dans l’étendue doivent se déconnecter et se reconnecter pour satisfaire aux exigences d’une nouvelle stratégie si :
> - une stratégie d’accès conditionnel est activée sur les conditions d’utilisation ;
> - une deuxième instance des conditions d’utilisation est créée.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Comment déterminer si un utilisateur a accepté les conditions d’utilisation et quand ?**</br>
R : Vous pouvez cliquer sur le nombre figurant sous « acceptées » en regard de vos conditions d’utilisation.  Pour plus d’informations, consultez [Voir qui a accepté et refusé](#view-who-has-accepted-and-declined).  En outre, un utilisateur qui accepte les conditions d’utilisation est consigné dans le journal d’audit. Vous pouvez rechercher le journal d’audit Azure AD pour voir les résultats.  

**Q : Si vous changez les conditions d’utilisation, les utilisateurs doivent-ils les accepter à nouveau ?**</br>
R : Oui. Si un administrateur change les conditions d’utilisation, les utilisateurs doivent les accepter à nouveau.

**Q : Les conditions d’utilisation prennent-elles en charge plusieurs langues ?**</br>
R. : Oui.  Actuellement, un administrateur peut configurer 18 langues différentes pour une seule condition d’utilisation. 

**Q : À quel moment les conditions d’utilisation sont-elles déclenchées ?**</br>
R : Les conditions d’utilisation sont déclenchées durant la procédure de connexion.

**Q : Quelles sont les applications pouvant être ciblées par des conditions d’utilisation ?**</br>
R : Vous pouvez créer une stratégie d’accès conditionnel sur les applications d’entreprise à l’aide de l’authentification moderne.  Pour plus d’informations, consultez [Applications d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**Q : Puis-je ajouter plusieurs conditions d’utilisation à une application ou un utilisateur donné ?**</br>
R : Oui. Pour cela, créez plusieurs stratégies d’accès conditionnel ciblant ces groupes ou applications. Si un utilisateur est concerné par plusieurs conditions d’utilisation, il accepte ces différentes conditions d’utilisation une par une.
 
**Q : Que se passe-t-il si un utilisateur refuse les conditions d’utilisation ?**</br>
R : L’utilisateur n’a pas accès à l’application. Il doit se reconnecter et accepter les conditions pour se voir octroyer des droits d’accès.
 
**Q : Combien de temps sont stockées les informations ?**</br>
R : Le nombre d’utilisateurs ayant accepté/refusé est stocké pour la durée de vie des conditions d’utilisation. Les journaux d’audit sont stockés pendant 30 jours.
