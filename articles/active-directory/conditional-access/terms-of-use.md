---
title: Conditions d’utilisation - Azure Active Directory | Microsoft Docs
description: Découvrez comment utiliser des conditions d’utilisation pour présenter des informations avant d’accéder à des employés ou des invités de Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1abae0a454e17e8f633f68bc5853bfb4a4b24d14
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383176"
---
# <a name="azure-active-directory-terms-of-use"></a>Conditions d’utilisation de Azure Active Directory

Conditions d’utilisation de Azure AD fournit une méthode simple qui les organisations peuvent utiliser pour présenter des informations aux utilisateurs finaux. Cette présentation permet de garantir que les utilisateurs voient les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité. Cet article décrit comment bien démarrer avec les conditions d’utilisation.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vidéos de présentation

La vidéo suivante fournit une vue d’ensemble rapide des conditions d’utilisation.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Pour voir d’autres vidéos, consultez :
- [Comment déployer les conditions d’utilisation dans Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Comment déployer des conditions d’utilisation dans Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Que puis-je faire avec les conditions d’utilisation ?

Conditions d’utilisation de Azure AD propose les fonctionnalités suivantes :

- Exiger des employés ou des invités à accepter vos conditions d’utilisation avant d’accéder.
- Exiger des employés ou des invités à accepter vos conditions d’utilisation sur chaque appareil avant d’accéder.
- Exiger des employés ou des invités à accepter vos conditions d’utilisation selon une planification périodique.
- Exiger des employés ou des invités à accepter vos conditions d’utilisation avant d’inscrire les informations de sécurité dans Azure multi-Factor Authentication (MFA).
- Demander aux employés d’accepter vos conditions d’utilisation avant d’inscrire les informations de sécurité dans la réinitialisation de mot de passe libre-service Azure AD (SSPR).
- Présenter des conditions générales d’utilisation pour tous les utilisateurs dans votre organisation.
- Présenter des conditions spécifiques d’utilisation en fonction d’un utilisateur d’attributs (par ex. docteurs/infirmières ou employés nationaux/internationaux dans des [groupes dynamiques](../users-groups-roles/groups-dynamic-membership.md)).
- Présente les conditions d’utilisation spécifiques lors de l’accès aux applications d’impact commercial élevé, tels que Salesforce.
- Présente les conditions d’utilisation dans différentes langues.
- Liste qui a ou n’a pas accepté vos conditions d’utilisation.
- Aider à faire face à la réglementation sur la confidentialité.
- Afficher le journal de conditions d’activité de l’utilisation de conformité et d’audit.
- Créer et gérer des conditions d’utilisation [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (actuellement en version préliminaire).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser et configurer les conditions d’utilisation de Azure AD, vous devez avoir :

- Abonnement Azure AD Premium P1, P2, EMS E3 ou EMS E5.
   - Si vous ne disposez pas d’un de ces abonnements, vous pouvez [obtenir Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [activer un essai d’Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Un des comptes d’administrateur suivants pour le répertoire que vous souhaitez configurer :
   - Administrateur général
   - Security Administrator
   - Administrateur de l’accès conditionnel

## <a name="terms-of-use-document"></a>Document Conditions d’utilisation

Conditions d’utilisation de Azure AD utilise le format PDF pour présenter du contenu. Libre à vous de choisir le contenu de ce PDF. Vous pouvez par exemple utiliser le document d’un contrat existant, ce qui vous permet de collecter les contrats des utilisateurs finaux quand ils se connectent. Pour prendre en charge les utilisateurs sur les appareils mobiles, il est recommandé d’utiliser une taille de police de 24 points dans le fichier PDF.

## <a name="add-terms-of-use"></a>Ajouter des conditions d’utilisation

Une fois que vous avez finalisé votre document conditions d’utilisation, procédez comme suit pour l’ajouter.

1. Connectez-vous à Azure en tant qu’Administrateur général, Administrateur de la sécurité ou Administrateur de l’accès conditionnel.
1. Accédez aux **conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

   ![Panneau Conditions d’utilisation](./media/terms-of-use/tou-blade.png)

1. Cliquez sur **Nouvelles conditions d’utilisation**.

   ![Ajouter des conditions d’utilisation](./media/terms-of-use/new-tou.png)

1. Dans le **nom** , entrez un nom pour les conditions d’utilisation qui sera utilisé dans le portail Azure.
1. Dans la zone **Nom d’affichage**, entrez le titre présenté aux utilisateurs quand ils se connectent.
1. Pour **document conditions d’utilisation**, accédez à votre finalisées vos conditions d’utilisation PDF et sélectionnez-le.
1. Sélectionnez la langue pour votre document conditions d’utilisation. L’option de langue vous permet de charger plusieurs conditions d’utilisation, chacune dans une langue différente. La version des conditions d’utilisation affichée pour l’utilisateur final dépend des préférences de son navigateur.
1. Pour exiger que les utilisateurs finaux d’afficher les conditions d’utilisation avant de les accepter, définissez **demander aux utilisateurs d’étendre les conditions d’utilisation** à **sur**.
1. Pour exiger que les utilisateurs finaux accepter vos conditions d’utilisation sur chaque appareil qu’ils accèdent à partir de, définissez **obliger les utilisateurs à donner son consentement sur chaque appareil** à **sur**. Pour plus d’informations, consultez [conditions d’utilisation de chaque périphérique](#per-device-terms-of-use).
1. Si vous voulez les faire expirer les conditions d’utilisation des consentements selon une planification, définissez **expirer consentements** à **sur**. Si la valeur Activé est définie, deux paramètres de planification supplémentaires sont affichés.

   ![Faire expirer les consentements](./media/terms-of-use/expire-consents.png)

1. Utiliser le **faire expirer à partir du** et **fréquence** paramètres pour spécifier la planification pour les conditions d’utilisent des délais d’expiration. Le tableau suivant présente deux exemples de paramètres et leur résultat :

   | Expiration commençant le | Fréquence | Résultat |
   | --- | --- | --- |
   | Date du jour  | Mensuelle | À compter d’aujourd'hui, les utilisateurs doivent accepter les conditions d’utilisation et acceptent ensuite chaque mois. |
   | Date future  | Mensuelle | À compter d’aujourd'hui, les utilisateurs doivent accepter les conditions d’utilisation. Quand la date future survient, les consentements expirent. Les utilisateurs doivent alors réaccepter les conditions d’utilisation chaque mois.  |

   Par exemple, si vous définissez « Expiration commençant le » avec la date **1 jan** et la fréquence avec la valeur **Mensuelle**, voici comment les expirations peuvent se produire pour deux utilisateurs :

   | Utilisateur | Date d’acceptation initiale | Première date d’expiration | Deuxième date d’expiration | Troisième date d’expiration |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 fév | 1 mar | 1 avr |
   | Bob | 15 jan | 1 fév | 1 mar | 1 avr |

1. Utiliser le **durée avant l’acceptation de la re nécessite (jours)** paramètre pour spécifier le nombre de jours avant que l’utilisateur doit acceptent les conditions d’utilisation. Les utilisateurs peuvent ainsi suivre leur propre calendrier. Par exemple, si vous définissez une durée de **30** jours, voici comment les expirations peuvent se produire pour deux utilisateurs :

   | Utilisateur | Date d’acceptation initiale | Première date d’expiration | Deuxième date d’expiration | Troisième date d’expiration |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 jan | 2 mar | 1 avr |
   | Bob | 15 jan | 14 fév | 16 mar | 15 avr |

   Il est possible d’utiliser les paramètres **Faire expirer les consentements** et **Durée avant nouvelle acceptation requise (jours)** ensemble, mais généralement, vous utilisez l’un ou l’autre.

1. Sous **accès conditionnel**, utilisez le **appliquer avec le modèle de stratégie d’accès conditionnel** liste pour sélectionner le modèle pour appliquer les conditions d’utilisation.

   ![Modèles d’accès conditionnel](./media/terms-of-use/conditional-access-templates.png)

   | Modèle | Description |
   | --- | --- |
   | **Accès aux applications cloud pour tous les invités** | Une stratégie d’accès conditionnel est créée pour tous les invités et toutes les applications cloud. Cette stratégie impacte le portail Azure. Une fois la stratégie créée, il peut vous être demandé de vous déconnecter et de vous connecter. |
   | **Accès aux applications cloud pour tous les utilisateurs** | Une stratégie d’accès conditionnel est créée pour tous les utilisateurs et toutes les applications cloud. Cette stratégie impacte le portail Azure. Une fois la stratégie créée, il vous est demandé de vous déconnecter et de vous connecter. |
   | **Stratégie personnalisée** | Sélectionnez les utilisateurs, groupes et les applications qui s’appliqueront à ces conditions d’utilisation. |
   | **Créer la stratégie d’accès conditionnel plus tard** | Ces conditions d’utilisation apparaîtront dans la liste de contrôle d’autorisation lors de la création d’une stratégie d’accès conditionnel. |

   >[!IMPORTANT]
   >Les contrôles de stratégie d’accès conditionnel (y compris les conditions d’utilisation) ne peuvent pas être appliqués sur les comptes de service. Nous vous recommandons d’exclure tous les comptes de service de la stratégie d’accès conditionnel.

    Stratégies d’accès conditionnel personnalisées permettent granulaires conditions d’utilisation, à une application cloud spécifique ou un groupe d’utilisateurs. Pour plus d’informations, consultez [Démarrage rapide : Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud](require-tou.md).

1. Cliquez sur **Créer**.

   Si vous avez sélectionné un modèle d’accès conditionnel personnalisé, un nouvel écran s’affiche pour vous permettre de créer la stratégie d’accès conditionnel personnalisée.

   ![Stratégie personnalisée](./media/terms-of-use/custom-policy.png)

   Vous devez maintenant voir vos nouvelles conditions d’utilisation.

   ![Ajouter des conditions d’utilisation](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Afficher le rapport des utilisateurs ayant accepté et refusé les conditions d’utilisation

Le panneau des conditions d’utilisation affiche le nombre d’utilisateurs ayant accepté et refusé les conditions d’utilisation. Ces nombres et qui a accepté/refusé sont stockées pendant la durée de vie des termes du contrat d’utilisation.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).

   ![Panneau Conditions d’utilisation](./media/terms-of-use/view-tou.png)

1. Pour les conditions d’utilisation, cliquez sur les nombres sous **accepté** ou **refusé** pour afficher l’état actuel pour les utilisateurs.

   ![Consentements pour les conditions d’utilisation](./media/terms-of-use/accepted-tou.png)

1. Pour afficher l’historique d’un utilisateur individuel, cliquez sur le bouton de sélection ( **...** ), puis sur **Afficher l’historique**.

   ![Menu Afficher l’historique](./media/terms-of-use/view-history-menu.png)

   Le volet Afficher l’historique présente l’historique des acceptations, des refus et des expirations.

   ![Volet Afficher l’historique](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Afficher les journaux d’audit Azure AD

Si vous souhaitez afficher l’activité supplémentaire, conditions d’utilisation de Azure AD inclut des journaux d’audit. Chaque consentement d’utilisateur déclenche un événement dans les journaux d’audit qui est stocké pendant **30 jours**. Vous pouvez afficher ces journaux d’activité dans le portail ou les télécharger sous forme de fichier .csv.

Pour démarrer avec les journaux d’audit Azure AD, effectuez les étapes suivantes :

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).
1. Sélectionnez les conditions d’utilisation.
1. Cliquez sur **Afficher les journaux d’audit**.

   ![Panneau Conditions d’utilisation](./media/terms-of-use/audit-tou.png)

1. Sur l’écran des journaux d’audit Azure AD, vous pouvez filtrer les informations à l’aide des listes fournies pour cibler des éléments spécifiques du journal d’audit.

   Vous pouvez également cliquer sur **Télécharger** pour télécharger les informations dans un fichier .csv en vue d’une utilisation locale.

   ![Journaux d’audit](./media/terms-of-use/audit-logs-tou.png)

   Si vous cliquez sur un journal, un volet s’affiche avec des détails supplémentaires sur l’activité.

   ![Détails de l’activité](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Ressemble à quelles conditions d’utilisation pour les utilisateurs

Une fois que les conditions d’utilisation créées et appliquées, les utilisateurs, qui sont dans la portée, seront affiche l’écran suivant pendant la connexion.

![Connexion des utilisateurs sur le web](./media/terms-of-use/user-tou.png)

Les utilisateurs peuvent afficher les conditions d’utilisation et, si nécessaire, utilisez les boutons pour effectuer un zoom avant et arrière.

![Afficher les conditions d’utilisation avec des boutons de zoom](./media/terms-of-use/zoom-buttons.png)

L’écran suivant montre comment se présente les conditions d’utilisation sur les appareils mobiles.

![Connexion des utilisateurs sur un appareil mobile](./media/terms-of-use/mobile-tou.png)

Les utilisateurs sont uniquement requis pour accepter les conditions d’utilisation qu’une seule fois et ils ne voient pas les conditions d’utilisation à nouveau lors des connexions ultérieures.

### <a name="how-users-can-review-their-terms-of-use"></a>Comment les utilisateurs peuvent consulter leurs conditions d’utilisation

Vous pouvez consulter et consultez les conditions d’utilisation qu’ils ont acceptées à l’aide de la procédure suivante.

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil**.

   ![Profil](./media/terms-of-use/tou14.png)

1. Dans la page de votre profil, cliquez sur **Vérifier les conditions d’utilisation**.

   ![Profil - Vérifier les conditions d’utilisation](./media/terms-of-use/tou13a.png)

1. C’est là que vous pouvez consulter les conditions d’utilisation que vous avez acceptées.

## <a name="edit-terms-of-use-details"></a>Modifier les conditions d’utilisation détails

Vous pouvez modifier certains détails des conditions d’utilisation, mais vous ne pouvez pas modifier un document existant. La procédure suivante explique comment modifier les détails.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).
1. Sélectionnez les conditions d’utilisation que vous souhaitez modifier.
1. Cliquez sur **Modifier les conditions**.
1. Dans le volet Modifier les conditions d’utilisation, modifiez le nom, le nom d’affichage ou demandez aux utilisateurs de développer les valeurs.

   S’il existe d’autres paramètres que vous souhaitez modifier, telles que des documents PDF, demander aux utilisateurs de donner leur consentement sur chaque appareil, faire expirer les consentements, durée avant reacceptance, ou une stratégie d’accès conditionnel, vous devez créer les nouvelles conditions d’utilisation.

   ![Modifier les conditions d’utilisation](./media/terms-of-use/edit-tou.png)

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

   Une fois les changements enregistrés, les utilisateurs ne devront pas réaccepter ces modifications.

## <a name="add-a-terms-of-use-language"></a>Ajouter des conditions d’utilisation langage

La procédure suivante décrit comment ajouter des conditions d’utilisation langage.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).
1. Sélectionnez les conditions d’utilisation que vous souhaitez modifier.
1. Dans le volet d’informations, cliquez sur l’onglet **Langues**.

   ![Ajouter des conditions d’utilisation](./media/terms-of-use/languages-tou.png)

1. Cliquez sur **Ajouter une langue**.
1. Dans le volet Ajouter une langue aux conditions d’utilisation, chargez votre PDF localisé et sélectionnez la langue.

   ![Ajouter des conditions d’utilisation](./media/terms-of-use/language-add-tou.png)

1. Cliquez sur **Ajouter** pour ajouter la langue.

## <a name="per-device-terms-of-use"></a>Conditions d’utilisation de chaque appareil

Le **obliger les utilisateurs à donner son consentement sur chaque appareil** paramètre vous permet les utilisateurs doivent accepter vos conditions d’utilisation sur chaque appareil qu’ils accèdent à partir de. L’utilisateur final doit joindre son appareil dans Azure AD. Lorsque l’appareil est joint, l’ID d’appareil est utilisé pour appliquer les conditions d’utilisation sur chaque appareil.

Voici une liste des plateformes et des logiciels pris en charge.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Autres |
> | --- | --- | --- | --- | --- |
> | **Application native** | Oui | OUI | Oui |  |
> | **Microsoft Edge** | Oui | OUI | Oui |  |
> | **Internet Explorer** | Oui | OUI | Oui |  |
> | **Chrome (avec extension)** | Oui | OUI | Oui |  |

Conditions d’utilisation de chaque appareil doit respecter les contraintes suivantes :

- Un appareil ne peut être joint qu’à un seul locataire.
- Un utilisateur doit disposer d’autorisations pour joindre son appareil.
- L’application d’inscription à Intune n’est pas prise en charge.
- Les utilisateurs Azure AD B2B ne sont pas pris en charge.

Si l’appareil de l’utilisateur n’est pas joint, il reçoit un message lui demandant de le faire. Son expérience varie selon la plateforme et le logiciel utilisés.

### <a name="join-a-windows-10-device"></a>Joindre un appareil Windows 10

Si un utilisateur utilise Windows 10 et Microsoft Edge, il reçoit un message semblable au suivant pour lui demander de [joindre son appareil](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 et Microsoft Edge - Message demandant de joindre un appareil](./media/terms-of-use/per-device-win10-edge.png)

S’il utilise Chrome, il est invité à installer l’[extension Windows 10 Accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Navigateurs

Si un utilisateur utilise un navigateur qui n’est pas pris en charge, il est invité à utiliser un autre navigateur.

![Navigateur non pris en charge](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Supprimer des conditions d’utilisation

Vous pouvez supprimer les anciennes conditions d’utilisation à l’aide de la procédure suivante.

1. Connectez-vous à Azure et accédez à **Conditions d’utilisation** sur [https://aka.ms/catou](https://aka.ms/catou).
1. Sélectionnez les conditions d’utilisation à supprimer.
1. Cliquez sur **Supprimer les conditions d’utilisation**.
1. Dans le message qui s’affiche vous demandant si vous souhaitez continuer, cliquez sur **Oui**.

   ![Supprimer des conditions d’utilisation](./media/terms-of-use/delete-tou.png)

   Vous devez voir n’est plus vos conditions d’utilisation.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilisateurs supprimés et actives conditions d’utilisation

Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu. Au bout de 30 jours, cet utilisateur est définitivement supprimé. En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](../fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme. Un utilisateur a été définitivement supprimé, les données suivantes sur cet utilisateur seront supprimées à partir des conditions d’utilisation actives. Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.

## <a name="policy-changes"></a>Modifications de stratégie

Les stratégies d’accès conditionnel prennent effet immédiatement. Dans ce cas, l’administrateur commence à voir des icônes de nuages tristes ou des erreurs liées aux jetons Azure AD. L’administrateur doit se déconnecter puis se reconnecter pour satisfaire aux exigences de la nouvelle stratégie.

> [!IMPORTANT]
> Les utilisateurs dans l’étendue doivent se déconnecter et se reconnecter pour satisfaire aux exigences d’une nouvelle stratégie si :
>
> - une stratégie d’accès conditionnel est activée sur les conditions d’utilisation ;
> - une deuxième instance des conditions d’utilisation est créée.

## <a name="b2b-guests-preview"></a>Invités B2B (préversion)

La plupart des organisations ont un processus en place pour leurs employés à donner son consentement pour les termes du contrat de leur organisation d’instructions d’utilisation et de confidentialité. Mais comment faire pour appliquer les mêmes consentements pour les invités Azure AD B2B quand ils sont ajoutés par le biais de SharePoint ou de Teams ? À l’aide de l’accès conditionnel et les conditions d’utilisation, vous pouvez appliquer une stratégie directement vers les utilisateurs invités B2B. Pendant le flux d’échange d’invitation, l’utilisateur est présenté avec les conditions d’utilisation. Cette prise en charge est actuellement en mode préliminaire.

Les conditions d’utilisation sont seulement affichées quand l’utilisateur dispose d’un compte invité dans Azure AD. SharePoint Online possède actuellement un [ad hoc expérience du destinataire partage externe](/sharepoint/what-s-new-in-sharing-in-targeted-release) pour partager un document ou un dossier qui ne nécessite pas de l’utilisateur dispose d’un compte invité. Dans ce cas, les conditions d’utilisation s’affiche pas.

![Tous les utilisateurs invités](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Prise en charge des applications cloud (préversion)

Les conditions d’utilisation peuvent être utilisées pour différentes applications cloud, notamment Azure Information Protection et Microsoft Intune. Cette prise en charge est actuellement en mode préliminaire.

### <a name="azure-information-protection"></a>Azure Information Protection

Vous pouvez configurer une stratégie d’accès conditionnel pour l’application Azure Information Protection et nécessitent des conditions d’utilisation lorsqu’un utilisateur accède à un document protégé. Cette opération déclenche les conditions d’utilisation avant un utilisateur accédant à un document protégé pour la première fois.

![Application cloud Azure Information Protection](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscription à Microsoft Intune

Vous pouvez configurer une stratégie d’accès conditionnel pour l’application d’inscription à Microsoft Intune et nécessitent des conditions d’utilisation avant l’inscription d’un appareil dans Intune. Pour plus d’informations, consultez le [billet de blog sur le choix des conditions appropriées pour votre organisation](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Application cloud de Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> L’application de l’inscription à Intune n’est pas pris en charge pour [conditions d’utilisation de chaque périphérique](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Comment voir si un utilisateur a accepté les conditions d’utilisation ?**<br />
R : Dans le panneau Conditions d’utilisation, cliquez sur le numéro sous **Accepté**. Vous pouvez également afficher ou rechercher l’activité d’acceptation dans les journaux d’audit Azure AD. Pour plus d’informations, consultez Afficher le rapport des utilisateurs ayant accepté et refusé les conditions d’utilisation et [Afficher les journaux d’audit Azure AD](#view-azure-ad-audit-logs).

**Q : Combien de temps sont stockées les informations ?**<br />
R : Compte de l’utilisateur dans les conditions d’utiliser un rapport et qui a accepté/refusé sont stocké pendant la durée de vie des termes du contrat d’utilisation. Les journaux d’audit Azure AD sont stockés pendant 30 jours.

**Q : Pourquoi un nombre différent des consentements dans les conditions d’utilisation des rapports et Azure AD journaux d’audit ?**<br />
R : Les conditions d’utiliser un rapport sont stockées pendant la durée de vie de ce conditions d’utilisation, lors de l’audit d’Azure AD sont stockés pendant 30 jours. En outre, les conditions d’utiliser un rapport affiche uniquement l’état de consentement de l’utilisateur actuel. Par exemple, si un utilisateur refuse, puis accepte les conditions d’utiliser un rapport n’affichent que l’utilisateur accepte. Si vous avez besoin de consulter l’historique, vous pouvez utiliser les journaux d’audit Azure AD.

**Q : Si la modification du détail des conditions d’utilisation, utilisateurs doivent-ils accepter à nouveau ?**<br />
R : Non, si un administrateur modifie les détails des conditions d’utilisation (nom, nom d’affichage, demander aux utilisateurs d’étendre ou ajouter une langue), il ne nécessite pas aux utilisateurs de réaccepter les nouveaux termes.

**Q : Puis-je mettre à jour une existante document conditions d’utilisation ?**<br />
R : Actuellement, vous ne pouvez pas mettre à jour un existant document conditions d’utilisation. Pour modifier un document conditions d’utilisation, vous devrez créer des conditions d’utiliser l’instance.

**Q : S’il existe des liens hypertexte dans les conditions d’utilisation des documents PDF, les utilisateurs finaux seront en mesure de cliquer dessus ?**<br />
R : Par défaut, le fichier PDF est affiché au format JPEG, par conséquent, il n’est pas possible de cliquer sur les liens hypertexte. Les utilisateurs ont la possibilité de sélectionner **Des problèmes d’affichage ? Cliquez ici**, pour afficher le fichier PDF en mode natif, qui prend en charge les liens hypertexte.

**Q : Conditions d’utilisation peuvent prendre en charge plusieurs langues ?**<br />
R : Oui. Actuellement il n’y 108 langues différentes, un administrateur peut configurer pour un seul conditions d’utilisation. Un administrateur peut charger plusieurs documents PDF et les étiqueter avec une langue correspondante (jusqu’à 108). Quand les utilisateurs finaux se connectent, nous examinons les préférences linguistiques de leur navigateur et affichons le document correspondant. En l’absence de correspondance, nous affichons le document par défaut (c’est-à-dire le premier qui a été chargé).

**Q : Est lorsque les conditions d’utilisation déclenchée ?**<br />
R : Les conditions d’utilisation est déclenchée lors de l’expérience de connexion.

**Q : Quelles applications puis-je cibler une conditions d’utilisation ?**<br />
R : Vous pouvez créer une stratégie d’accès conditionnel sur les applications d’entreprise à l’aide de l’authentification moderne. Pour plus d’informations, consultez [Applications d’entreprise](./../manage-apps/view-applications-portal.md).

**Q : Puis-je ajouter plusieurs conditions d’utilisation pour un utilisateur donné ou une application ?**<br />
R : Oui. Pour cela, créez plusieurs stratégies d’accès conditionnel ciblant ces groupes ou applications. Si un utilisateur est dans l’étendue de plusieurs conditions d’utilisation, ils acceptent un conditions d’utilisation à la fois.

**Q : Que se passe-t-il si un utilisateur refuse les conditions d’utilisation ?**<br />
R : L’utilisateur n’a pas accès à l’application. Il doit se reconnecter et accepter les conditions pour se voir octroyer des droits d’accès.

**Q : Il est possible d’unaccept des conditions d’utilisation qui avait été acceptée ?**<br />
R : Vous pouvez [vérifier les conditions d’utilisation précédemment acceptées](#how-users-can-review-their-terms-of-use), mais il n’existe actuellement aucun moyen pour unaccept.

**Q : Que se passe-t-il si j’utilise également les conditions générales d’Intune ?**<br />
R : Si vous avez configuré les deux conditions d’utilisation de Azure AD et [Intune termes et conditions](/intune/terms-and-conditions-create), l’utilisateur doit accepter tous les deux. Pour plus d’informations, voir le [billet de blog sur le choix des conditions appropriées pour votre organisation](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**Q : Quels points de terminaison utilise les termes du contrat de service d’utilisation pour l’authentification ?**<br />
R : Conditions d’utilisation utilise des points de terminaison suivants pour l’authentification : https://tokenprovider.termsofuse.identitygovernance.azure.com et https://account.activedirectory.windowsazure.com. Si votre organisation dispose d’une liste verte d’URL pour l’inscription, vous devez ajouter ces points de terminaison à votre liste verte, ainsi que les points de terminaison Azure AD pour se connecter.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud](require-tou.md)
- [Meilleures pratiques l’accès conditionnel dans Azure Active Directory](best-practices.md)
