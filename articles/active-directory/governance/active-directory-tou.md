---
title: Conditions d’utilisation d’Azure Active Directory | Microsoft Docs
description: Décrit comment utiliser les conditions d’utilisation d’Azure AD pour présenter des informations à des employés ou à des invités avant de leur octroyer l’accès.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2019
ms.author: rolyon
ms.openlocfilehash: be8a6ba83de81c5a82797ecf3d30c7a7fcaa65fa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162767"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Fonctionnalité Conditions d’utilisation d’Azure Active Directory
La fonctionnalité Conditions d’utilisation d’Azure AD offre aux organisations une méthode simple pour présenter des informations aux utilisateurs finaux. Cette présentation permet de garantir que les utilisateurs voient les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité. Cet article vous indique comment prendre en main les conditions d’utilisation.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vidéos de présentation

La vidéo suivante fournit une vue d’ensemble des conditions d’utilisation.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Pour voir d’autres vidéos, consultez :
- [Comment déployer les conditions d’utilisation dans Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Comment installer les conditions d’utilisation dans Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Que puis-je faire avec Conditions d’utilisation ?
La fonctionnalité Conditions d’utilisation d’Azure AD vous permet d’effectuer les opérations suivantes :
- Faire accepter vos conditions d’utilisation à vos employés ou à vos invités avant de leur octroyer l’accès.
- Faire accepter vos conditions d’utilisation à vos employés ou à vos invités sur chaque appareil avant de leur octroyer l’accès.
- Faire accepter vos conditions d’utilisation à vos employés ou à vos invités selon une planification périodique.
- Présenter les conditions d’utilisation générales pour tous les utilisateurs de votre organisation.
- Présenter les conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, docteurs/infirmières ou employés nationaux/internationaux dans des [groupes dynamiques](../users-groups-roles/groups-dynamic-membership.md)).
- Présenter les conditions d’utilisation spécifiques lors de l’accès aux applications à fort impact commercial, comme Salesforce.
- Présenter les conditions d’utilisation dans différentes langues.
- Lister qui a accepté ou refusé vos conditions d’utilisation.
- Aider à faire face à la réglementation sur la confidentialité.
- Consulter le journal des conditions d’utilisation pour en vérifier la conformité et procéder à un audit.
- Créer et gérer des conditions d’utilisation avec des [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (actuellement préversion).

## <a name="prerequisites"></a>Prérequis
Pour utiliser et configurer les conditions d’utilisation d’Azure AD, vous devez disposer des éléments suivants :

- Abonnement Azure AD Premium P1, P2, EMS E3 ou EMS E5.
    - Si vous ne disposez pas d’un de ces abonnements, vous pouvez [obtenir Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [activer un essai d’Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Un des comptes d’administrateur suivants pour le répertoire que vous souhaitez configurer :
    - Administrateur général
    - Security Administrator
    - Administrateur de l’accès conditionnel

## <a name="terms-of-use-document"></a>Document Conditions d’utilisation

Le contenu proposé par la fonctionnalité Conditions d’utilisation d’Azure AD est au format PDF. Libre à vous de choisir le contenu de ce PDF. Vous pouvez par exemple utiliser le document d’un contrat existant, ce qui vous permet de collecter les contrats des utilisateurs finaux quand ils se connectent. Pour prendre en charge les utilisateurs sur les appareils mobiles, il est recommandé d’utiliser une taille de police de 24 points dans le fichier PDF.

## <a name="add-terms-of-use"></a>Ajouter des conditions d’utilisation
Après avoir finalisé le document relatif à vos conditions d’utilisation, effectuez les étapes suivantes pour les ajouter.

1. Connectez-vous à Azure en tant qu’Administrateur général, Administrateur de la sécurité ou Administrateur de l’accès conditionnel.

1. Accédez aux **conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

    ![Panneau Conditions d’utilisation](./media/active-directory-tou/tou-blade.png)

1. Cliquez sur **Nouvelles conditions d’utilisation**.

    ![Ajouter des conditions d’utilisation](./media/active-directory-tou/new-tou.png)

1. Dans la zone **Nom**, entrez le nom des conditions d’utilisation à utiliser dans le portail Azure.

1. Dans la zone **Nom d’affichage**, entrez le titre présenté aux utilisateurs quand ils se connectent.

1. Pour **Document Conditions d’utilisation**, accédez au fichier PDF finalisé de vos conditions d’utilisation et sélectionnez-le.

1. Sélectionnez la langue de votre document Conditions d’utilisation. L’option de langue vous permet de charger plusieurs conditions d’utilisation, chacune dans une langue différente. La version des conditions d’utilisation affichée pour l’utilisateur final dépend des préférences de son navigateur.

1. Pour demander aux utilisateurs finaux d’afficher les conditions d’utilisation avant de les accepter, définissez **Demander aux utilisateurs d’étendre les conditions d’utilisation** avec la valeur **Activé**.

1. Pour demander aux utilisateurs finaux d’accepter vos conditions d’utilisation sur chaque appareil à partir duquel l’accès leur est octroyé, définissez **Demander le consentement des utilisateurs sur chaque appareil** avec la valeur **Activé**. Pour plus d’informations, consultez [Conditions d’utilisation par appareil](#per-device-terms-of-use).

1. Si vous voulez faire expirer les consentements pour les conditions d’utilisation selon une planification, définissez **Faire expirer les consentements** avec la valeur **Activé**. Si la valeur Activé est définie, deux paramètres de planification supplémentaires sont affichés.

    ![Faire expirer les consentements](./media/active-directory-tou/expire-consents.png)

1. Utilisez les paramètres **Expiration commençant le** et **Fréquence** pour spécifier la planification régissant l’expiration des conditions d’utilisation. Le tableau suivant présente deux exemples de paramètres et leur résultat :

    | Expiration commençant le | Fréquence | Résultat |
    | --- | --- | --- |
    | Date du jour  | Mensuelle | À compter d’aujourd’hui, les utilisateurs doivent accepter les conditions d’utilisation et les réaccepter chaque mois. |
    | Date future  | Mensuelle | À compter d’aujourd’hui, les utilisateurs doivent accepter les conditions d’utilisation. Quand la date future survient, les consentements expirent. Les utilisateurs doivent alors réaccepter les conditions d’utilisation chaque mois.  |

    Par exemple, si vous définissez « Expiration commençant le » avec la date **1 jan** et la fréquence avec la valeur **Mensuelle**, voici comment les expirations peuvent se produire pour deux utilisateurs :

    | Utilisateur | Date d’acceptation initiale | Première date d’expiration | Deuxième date d’expiration | Troisième date d’expiration |
    | --- | --- | --- | --- | --- |
    | Alice | 1 jan | 1 fév | 1 mar | 1 avr |
    | Bob | 15 jan | 1 fév | 1 mar | 1 avr |

1. Utilisez le paramètre **Durée avant nouvelle acceptation requise (jours)** pour spécifier le nombre de jours au bout duquel l’utilisateur doit réaccepter les conditions d’utilisation. Les utilisateurs peuvent ainsi suivre leur propre calendrier. Par exemple, si vous définissez une durée de **30** jours, voici comment les expirations peuvent se produire pour deux utilisateurs :

    | Utilisateur | Date d’acceptation initiale | Première date d’expiration | Deuxième date d’expiration | Troisième date d’expiration |
    | --- | --- | --- | --- | --- |
    | Alice | 1 jan | 31 jan | 2 mar | 1 avr |
    | Bob | 15 jan | 14 fév | 16 mar | 15 avr |

    Il est possible d’utiliser les paramètres **Faire expirer les consentements** et **Durée avant nouvelle acceptation requise (jours)** ensemble, mais généralement, vous utilisez l’un ou l’autre.

1. Sous **Accès conditionnel**, utilisez la liste **Appliquer avec des modèles de stratégie d’accès conditionnel** afin de sélectionner le modèle à utiliser pour appliquer les conditions d’utilisation.

    ![Modèles d’accès conditionnel](./media/active-directory-tou/conditional-access-templates.png)

    | Modèle | Description |
    | --- | --- |
    | **Accès aux applications cloud pour tous les invités** | Une stratégie d’accès conditionnel est créée pour tous les invités et toutes les applications cloud. Cette stratégie impacte le portail Azure. Une fois la stratégie créée, il peut vous être demandé de vous déconnecter et de vous connecter. |
    | **Accès aux applications cloud pour tous les utilisateurs** | Une stratégie d’accès conditionnel est créée pour tous les utilisateurs et toutes les applications cloud. Cette stratégie impacte le portail Azure. Une fois la stratégie créée, il vous est demandé de vous déconnecter et de vous connecter. |
    | **Stratégie personnalisée** | Sélectionnez les utilisateurs, groupes et applications auxquels ces conditions d’utilisation seront appliquées. |
    | **Créer la stratégie d’accès conditionnel plus tard** | Ces conditions d’utilisation apparaîtront dans la liste de contrôle d’autorisation lors de la création d’une stratégie d’accès conditionnel. |

    >[!IMPORTANT]
    >Les contrôles de stratégie d’accès conditionnel (y compris les conditions d’utilisation) ne peuvent pas être appliqués sur les comptes de service. Nous vous recommandons d’exclure tous les comptes de service de la stratégie d’accès conditionnel.

     Les stratégies d’accès conditionnel personnalisées permettent de définir des conditions d’utilisation plus précises, jusqu’au niveau d’une application cloud ou d’un groupe d’utilisateurs. Pour plus d’informations, consultez [Démarrage rapide : Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud](../conditional-access/require-tou.md).

1. Cliquez sur **Créer**.

    Si vous avez sélectionné un modèle d’accès conditionnel personnalisé, un nouvel écran s’affiche pour vous permettre de créer la stratégie d’accès conditionnel personnalisée.

    ![Stratégie personnalisée](./media/active-directory-tou/custom-policy.png)

    Vos nouvelles conditions d’utilisation doivent désormais s’afficher.

    ![Ajouter des conditions d’utilisation](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Afficher le rapport des utilisateurs ayant accepté et refusé les conditions d’utilisation
Le panneau des conditions d’utilisation affiche le nombre d’utilisateurs ayant accepté et refusé les conditions d’utilisation. Ce nombre et le nom des personnes ayant accepté/refusé sont stockés pour toute la durée de vie des conditions d’utilisation.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

    ![Panneau Conditions d’utilisation](./media/active-directory-tou/view-tou.png)

1. Pour les conditions d’utilisation, cliquez sur les nombres sous **Accepté** ou **Refusé** pour afficher l’état actuel des utilisateurs.

    ![Consentements pour les conditions d’utilisation](./media/active-directory-tou/accepted-tou.png)

1. Pour afficher l’historique d’un utilisateur individuel, cliquez sur le bouton de sélection (**...**), puis sur **Afficher l’historique**.

    ![Menu Afficher l’historique](./media/active-directory-tou/view-history-menu.png)

    Le volet Afficher l’historique présente l’historique des acceptations, des refus et des expirations.

    ![Volet Afficher l’historique](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Afficher les journaux d’audit Azure AD
Si vous souhaitez afficher d’autres activités, la fonctionnalité Conditions d’utilisation d’Azure AD inclut des journaux d’audit. Chaque consentement d’utilisateur déclenche un événement dans les journaux d’audit qui est stocké pendant **30 jours**. Vous pouvez afficher ces journaux dans le portail ou les télécharger sous forme de fichier .csv.

Pour démarrer avec les journaux d’audit Azure AD, effectuez les étapes suivantes :

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

1. Sélectionnez des conditions d’utilisation.

1. Cliquez sur **Afficher les journaux d’audit**.

    ![Panneau Conditions d’utilisation](./media/active-directory-tou/audit-tou.png)

1. Sur l’écran des journaux d’audit Azure AD, vous pouvez filtrer les informations à l’aide des listes fournies pour cibler des éléments spécifiques du journal d’audit.

    Vous pouvez également cliquer sur **Télécharger** pour télécharger les informations dans un fichier .csv en vue d’une utilisation locale.

    ![Journaux d’audit](./media/active-directory-tou/audit-logs-tou.png)

    Si vous cliquez sur un journal, un volet s’affiche avec des détails supplémentaires sur l’activité.

    ![Détails de l’activité](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>À quoi ressemblent les conditions d’utilisation pour les utilisateurs ?
Les utilisateurs dans l’étendue voient l’écran suivant au cours de la connexion une fois les conditions d’utilisation créées et appliquées.

![Connexion des utilisateurs sur le web](./media/active-directory-tou/user-tou.png)

L’écran suivant montre comment se présente les conditions d’utilisation sur les appareils mobiles.

![Connexion des utilisateurs sur un appareil mobile](./media/active-directory-tou/mobile-tou.png)

Les utilisateurs ne doivent accepter les conditions d’utilisation qu’une seule fois et ils ne les verront plus chaque fois qu’ils se reconnectent.

### <a name="how-users-can-review-their-terms-of-use"></a>Comment les utilisateurs peuvent consulter leurs conditions d’utilisation
Vous pouvez consulter et vérifier les conditions d’utilisation qu’ils ont acceptées en procédant comme suit.

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil**.

    ![Profil](./media/active-directory-tou/tou14.png)

1. Dans la page de votre profil, cliquez sur **Vérifier les conditions d’utilisation**.

    ![Profil - Vérifier les conditions d’utilisation](./media/active-directory-tou/tou13a.png)

1. C’est là que vous pouvez consulter les conditions d’utilisation que vous avez acceptées.

## <a name="edit-terms-of-use-details"></a>Modifier les détails des conditions d’utilisation
Vous pouvez modifier certains détails des conditions d’utilisation, mais il n’est pas possible de modifier un document existant. La procédure suivante explique comment modifier les détails.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

1. Sélectionnez les conditions d’utilisation à modifier.

1. Cliquez sur **Modifier les conditions**.

1. Dans le volet Modifier les conditions d’utilisation, modifiez le nom, le nom d’affichage ou demandez aux utilisateurs de développer les valeurs.

    Si vous souhaitez changer d’autres paramètres, comme le document PDF, le fait de demander le consentement des utilisateurs sur chaque appareil, l’expiration des consentements, la durée avant nouvelle acceptation ou encore la stratégie d’accès conditionnel, vous devez établir de nouvelles conditions d’utilisation.

    ![Modifier les conditions d’utilisation](./media/active-directory-tou/edit-tou.png)

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

    Une fois les changements enregistrés, les utilisateurs ne devront pas réaccepter ces modifications.

## <a name="add-a-terms-of-use-language"></a>Ajouter une langue aux conditions d'utilisation
La procédure suivante explique comment ajouter une langue aux conditions d’utilisation.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

1. Sélectionnez les conditions d’utilisation à modifier.

1. Dans le volet d’informations, cliquez sur l’onglet **Langues**.

    ![Ajouter des conditions d’utilisation](./media/active-directory-tou/languages-tou.png)

1. Cliquez sur **Ajouter une langue**.

1. Dans le volet Ajouter une langue aux conditions d’utilisation, chargez votre PDF localisé et sélectionnez la langue.

    ![Ajouter des conditions d’utilisation](./media/active-directory-tou/language-add-tou.png)

1. Cliquez sur **Ajouter** pour ajouter la langue.

## <a name="per-device-terms-of-use"></a>Conditions d’utilisation par appareil

Le paramètre **Demander le consentement des utilisateurs sur chaque appareil** vous permet de demander aux utilisateurs finaux d’accepter vos conditions d’utilisation sur chaque appareil à partir duquel l’accès leur est octroyé. L’utilisateur final doit joindre son appareil dans Azure AD. Quand l’appareil est joint, l’ID d’appareil est utilisé pour appliquer les conditions d’utilisation sur chaque appareil.

Voici une liste des plateformes et des logiciels pris en charge.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Autres |
> | --- | --- | --- | --- | --- |
> | **Application native** | Oui | OUI | Oui |  |
> | **Microsoft Edge** | Oui | OUI | Oui |  |
> | **Internet Explorer** | Oui | OUI | Oui |  |
> | **Chrome (avec extension)** | Oui | OUI | Oui |  |

Les conditions d’utilisation par appareil ont les contraintes suivantes :

- Un appareil ne peut être joint qu’à un seul locataire.
- Un utilisateur doit disposer d’autorisations pour joindre son appareil.
- L’application d’inscription à Intune n’est pas prise en charge.

Si l’appareil de l’utilisateur n’est pas joint, il reçoit un message lui demandant de le faire. Son expérience varie selon la plateforme et le logiciel utilisés.

### <a name="join-a-windows-10-device"></a>Joindre un appareil Windows 10

Si un utilisateur utilise Windows 10 et Microsoft Edge, il reçoit un message semblable au suivant pour lui demander de [joindre son appareil](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 et Microsoft Edge - Message demandant de joindre un appareil](./media/active-directory-tou/per-device-win10-edge.png)

S’il utilise Chrome, il est invité à installer l’[extension Windows 10 Accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Navigateurs

Si un utilisateur utilise un navigateur qui n’est pas pris en charge, il est invité à utiliser un autre navigateur.

![Navigateur non pris en charge](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Supprimer des conditions d’utilisation
Pour supprimer d’anciennes conditions d’utilisation, effectuez les étapes suivantes :

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

1. Sélectionnez les conditions d’utilisation à supprimer.

1. Cliquez sur **Supprimer les conditions d’utilisation**.

1. Dans le message qui s’affiche vous demandant si vous souhaitez continuer, cliquez sur **Oui**.

    ![Supprimer des conditions d’utilisation](./media/active-directory-tou/delete-tou.png)

    Vos conditions d’utilisation doivent désormais ne plus s’afficher.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilisateurs supprimés et conditions d’utilisation actives
Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu. Au bout de 30 jours, cet utilisateur est définitivement supprimé. En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](../fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme. Une fois qu’un utilisateur a été définitivement supprimé, les données le concernant sont par la suite supprimées des conditions d’utilisation actives. Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.

## <a name="policy-changes"></a>Modifications de stratégie
Les stratégies d’accès conditionnel prennent effet immédiatement. Dans ce cas, l’administrateur commence à voir des icônes de nuages tristes ou des erreurs liées aux jetons Azure AD. L’administrateur doit se déconnecter puis se reconnecter pour satisfaire aux exigences de la nouvelle stratégie.

>[!IMPORTANT]
> Les utilisateurs dans l’étendue doivent se déconnecter et se reconnecter pour satisfaire aux exigences d’une nouvelle stratégie si :
> - une stratégie d’accès conditionnel est activée sur les conditions d’utilisation ;
> - une deuxième instance des conditions d’utilisation est créée.

## <a name="b2b-guests-preview"></a>Invités B2B (préversion)

La plupart des organisations mettent en place un processus qui permet à leurs employés de consentir aux conditions d’utilisation et déclarations de confidentialité de leur organisation. Mais comment faire pour appliquer les mêmes consentements pour les invités Azure AD B2B quand ils sont ajoutés par le biais de SharePoint ou de Teams ? Grâce à l’accès conditionnel et aux conditions d’utilisation, vous pouvez appliquer une stratégie directement aux utilisateurs invités B2B. Durant le flux d’acceptation d’invitation, l’utilisateur se voit présenter les conditions d’utilisation. Cette prise en charge est actuellement en mode préliminaire.

Les conditions d’utilisation sont seulement affichées quand l’utilisateur dispose d’un compte invité dans Azure AD. SharePoint Online propose actuellement une [expérience ad-hoc de partage avec des destinataires externes](/sharepoint/what-s-new-in-sharing-in-targeted-release) pour partager un document ou un dossier sans contraindre l’utilisateur à avoir un compte invité. Dans ce cas, les conditions d’utilisation ne sont pas affichées.

![Tous les utilisateurs invités](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Prise en charge des applications cloud (préversion)

Les conditions d’utilisation peuvent être utilisées pour différentes applications cloud, notamment Azure Information Protection et Microsoft Intune. Cette prise en charge est actuellement en mode préliminaire.

### <a name="azure-information-protection"></a>Azure Information Protection

Vous pouvez configurer une stratégie d’accès conditionnel pour l’application Azure Information Protection et exiger l’affichage de conditions d’utilisation quand un utilisateur accède à un document protégé. L’affichage des conditions d’utilisation est alors déclenché avant l’accès initial de l’utilisateur au document protégé.

![Application cloud Azure Information Protection](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscription à Microsoft Intune

Vous pouvez configurer une stratégie d’accès conditionnel pour l’application d’inscription à Microsoft Intune et exiger l’affichage de conditions d’utilisation avant l’inscription d’un appareil dans Intune. Pour plus d’informations, consultez le [billet de blog sur le choix des conditions appropriées pour votre organisation](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Application cloud de Microsoft Intune](./media/active-directory-tou/cloud-app-intune.png)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Comment déterminer si un utilisateur a accepté les conditions d’utilisation et quand ?**<br />
R : Dans le panneau Conditions d’utilisation, cliquez sur le numéro sous **Accepté**. Vous pouvez également afficher ou rechercher l’activité d’acceptation dans les journaux d’audit Azure AD. Pour plus d’informations, consultez [Afficher le rapport des utilisateurs ayant accepté et refusé les conditions d’utilisation](#view-who-has-accepted-and-declined) et [Afficher les journaux d’audit Azure AD](#view-azure-ad-audit-logs).

**Q : Combien de temps sont stockées les informations ?**<br />
R : Le nombre d’utilisateurs ayant accepté et refusé les conditions d’utilisation est stocké pour la durée de vie des conditions d’utilisation. Les journaux d’audit Azure AD sont stockés pendant 30 jours.

**Q : Pourquoi le nombre de consentements est-il différent dans le rapport des conditions d’utilisation et dans les journaux d’audit Azure AD ?**<br />
R : Le rapport des conditions d’utilisation est stocké pendant toute la durée de vie de celles-ci, alors que les journaux d’audit Azure AD sont stockés pendant 30 jours. En outre, le rapport des conditions d’utilisation affiche uniquement l’état actuel du consentement utilisateur. Par exemple, si un utilisateur refuse, puis accepte les conditions d’utilisation, le rapport affichera uniquement son acceptation. Si vous avez besoin de consulter l’historique, vous pouvez utiliser les journaux d’audit Azure AD.

**Q : Si je modifie les détails des conditions d’utilisation, les utilisateurs doivent-ils les réaccepter ?**<br />
R : Non. Si un administrateur modifie les détails des conditions d’utilisation (nom, nom d’affichage, demander aux utilisateurs d’étendre ou ajouter une langue), les utilisateurs ne sont pas tenus de réaccepter les nouveaux termes.

**Q : Puis-je mettre à jour un document de conditions d’utilisation existant ?**<br />
R : Il n’est pas possible à l’heure actuelle de mettre à jour un document de conditions d’utilisation existant. Il vous faudra pour cela créer une nouvelle instance de conditions d’utilisation.

**Q : Si des liens hypertexte se trouvent dans les conditions d’utilisation d’un document PDF, les utilisateurs finaux peuvent-ils cliquer dessus ?**<br />
R : Par défaut, le fichier PDF est affiché au format JPEG, par conséquent, il n’est pas possible de cliquer sur les liens hypertexte. Les utilisateurs ont la possibilité de sélectionner **Des problèmes d’affichage ? Cliquez ici**, pour afficher le fichier PDF en mode natif, qui prend en charge les liens hypertexte.

**Q : Les conditions d’utilisation prennent-elles en charge plusieurs langues ?**<br />
R : Oui. Actuellement, un administrateur peut configurer 108 langues différentes pour des conditions d’utilisation. Un administrateur peut charger plusieurs documents PDF et les étiqueter avec une langue correspondante (jusqu’à 108). Quand les utilisateurs finaux se connectent, nous examinons les préférences linguistiques de leur navigateur et affichons le document correspondant. En l’absence de correspondance, nous affichons le document par défaut (c’est-à-dire le premier qui a été chargé).

**Q : À quel moment les conditions d’utilisation sont-elles déclenchées ?**<br />
R : Les conditions d’utilisation sont déclenchées durant la procédure de connexion.

**Q : Quelles sont les applications pouvant être ciblées par des conditions d’utilisation ?**<br />
R : Vous pouvez créer une stratégie d’accès conditionnel sur les applications d’entreprise à l’aide de l’authentification moderne. Pour plus d’informations, consultez [Applications d’entreprise](./../manage-apps/view-applications-portal.md).

**Q : Puis-je ajouter plusieurs conditions d’utilisation à une application ou un utilisateur donné ?**<br />
R : Oui. Pour cela, créez plusieurs stratégies d’accès conditionnel ciblant ces groupes ou applications. Si un utilisateur est concerné par plusieurs conditions d’utilisation, il accepte une instance à la fois.

**Q : Que se passe-t-il si un utilisateur refuse les conditions d’utilisation ?**<br />
R : L’utilisateur n’a pas accès à l’application. Il doit se reconnecter et accepter les conditions pour se voir octroyer des droits d’accès.

**Q : Est-il possible de refuser les conditions d’utilisation après les avoir acceptées ?**<br />
R : Vous pouvez [consulter les conditions d’utilisation précédemment acceptées](#how-users-can-review-their-terms-of-use), mais vous ne pouvez pas les refuser après les avoir acceptées.

**Q : Que se passe-t-il si j’utilise également les conditions générales d’Intune ?**<br />
R : Si vous avez configuré les conditions d’utilisation d’Azure AD et les [conditions d’utilisation d’Intune](/intune/terms-and-conditions-create), l’utilisateur doit accepter les deux. Pour plus d’informations, voir le [billet de blog sur le choix des conditions appropriées pour votre organisation](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud](../conditional-access/require-tou.md)
- [Meilleures pratiques l’accès conditionnel dans Azure Active Directory](../conditional-access/best-practices.md)
