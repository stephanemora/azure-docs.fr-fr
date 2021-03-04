---
title: Sécurisation de l’authentification MFA basée sur le téléphone dans Azure AD B2C
titleSuffix: Azure AD B2C
description: Découvrez des conseils pour la sécurisation de l’authentification multifacteur (MFA) basée sur le téléphone dans votre locataire Azure AD B2C à l’aide des rapports et des alertes Log Analytics d’Azure Monitor. Utilisez notre classeur pour identifier les authentifications par téléphone frauduleuses et limiter les inscriptions frauduleuses. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033552"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Sécurisation de l’authentification multifacteur (MFA) basée sur le téléphone

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Avec l’authentification multifacteur (MFA) d’Azure Active Directory (Azure AD), les utilisateurs peuvent choisir de recevoir un appel vocal automatisé à un numéro de téléphone inscrit pour effectuer la vérification. Des utilisateurs malveillants peuvent profiter de cette méthode en créant plusieurs comptes et en effectuant des appels téléphoniques sans terminer le processus d’inscription à l’authentification MFA. Ces nombreuses connexions ayant échoué peuvent épuiser les tentatives d’inscription autorisées, empêchant les autres utilisateurs d’inscrire de nouveaux comptes dans votre locataire Azure AD B2C. Pour vous protéger contre ces attaques, vous pouvez utiliser Azure Monitor pour surveiller les échecs d’authentification par téléphone et limiter les inscriptions frauduleuses.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, créez un [espace de travail Log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Créer un classeur d’événements MFA basés sur le téléphone

Le référentiel [Rapports et alertes Azure AD B2C](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) dans GitHub contient des artefacts que vous pouvez utiliser pour créer et publier des rapports, des alertes et des tableaux de bord basés sur les journaux d’Azure AD B2C. Le classeur illustré ci-dessous met en évidence les échecs liés au téléphone.

### <a name="overview-tab"></a>Onglet Overview

Les informations suivantes s’affichent dans l’onglet **Vue d’ensemble** :

- Raisons de l’échec (nombre total d’authentifications par téléphone ayant échoué pour chaque raison)
- Bloqué en raison d’une mauvaise réputation
- Adresse IP des authentifications par téléphone ayant échoué (nombre total d’échecs d’authentification par téléphone pour chaque adresse IP)
- Numéros de téléphone avec adresse IP – Échecs d’authentification par téléphone
- Navigateur (échecs d’authentification par téléphone par navigateur client)
- Système d’exploitation (échecs d’authentification par téléphone par système d’exploitation client)

![Onglet Overview](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Onglet Détails

Les informations suivantes sont indiquées dans l’onglet **Détails** :

- Stratégie Azure AD B2C – Échecs d’authentification par téléphone
- Échecs d’authentification par téléphone par numéro de téléphone – Graphique de temps (chronologie réglable)
- Échecs d’authentification par téléphone par stratégie Azure AD B2C – Graphique de temps (chronologie réglable)
- Échecs d’authentification par téléphone par adresse IP – Graphique de temps (chronologie réglable)
- Sélectionner le numéro de téléphone pour afficher les détails de l’échec (sélectionnez un numéro de téléphone pour obtenir une liste détaillée des échecs)

![Onglet Détails 1 sur 3](media/phone-based-mfa/details-tab-1.png)

![Onglet Détails 2 sur 3](media/phone-based-mfa/details-tab-2.png)

![Onglet Détails 3 sur 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Utiliser le classeur pour identifier les connexions frauduleuses

Vous pouvez utiliser le classeur pour comprendre les événements MFA basés sur le téléphone et identifier l’utilisation potentiellement malveillante du service de téléphonie.

1. Déterminez ce qui est normal pour votre locataire en répondant à ces questions :

   - Quelles sont les régions à partir desquelles vous attendez une authentification MFA basée sur le téléphone ?
   - Examinez les raisons indiquées pour les échecs d’authentification MFA par téléphone. Ces raisons sont-elles considérées comme normales ou attendues ?

2. Reconnaissez les caractéristiques d’une inscription frauduleuse :

   - **Basée sur un emplacement** : Examinez les **Échecs d’authentification par téléphone par adresse IP** pour tous les comptes associés à des emplacements à partir desquels vous ne vous attendez pas à des inscriptions d’utilisateurs.

   > [!NOTE]
   > L’adresse IP fournie correspond à une région approximative.

   - **Basée sur la rapidité** : Examinez les **Heures supplémentaires pour les échecs d’authentification par téléphone (par jour)** qui indiquent les numéros de téléphone effectuant un nombre anormal de tentatives échouées d’authentification par téléphone par jour, classés du plus élevé (à gauche) au plus faible (à droite).

3. Limitez les connexions frauduleuses en suivant les étapes décrites dans la section suivante.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Limiter les connexions frauduleuses

Effectuez les actions suivantes pour limiter les connexions frauduleuses.

- Utilisez les versions **recommandées** des flux d’utilisateurs pour effectuer les opérations suivantes :
     
   - [Activez la fonctionnalité de code secret à usage unique (OTP)](phone-authentication-user-flows.md) pour l’authentification MFA (s’applique aux flux d’inscription et de connexion).
   - [Configurez une stratégie d’accès conditionnel](conditional-access-user-flow.md) pour bloquer les connexions basées sur l’emplacement (s’applique aux flux de connexion uniquement, pas aux flux d’inscription).
   - Utilisez les connecteurs d’API pour l’[intégration avec une solution anti-robot telle que reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (s’applique aux flux d’inscription).

- Supprimez les codes de pays qui ne sont pas pertinents pour votre organisation du menu déroulant dans lequel l’utilisateur vérifie son numéro de téléphone (cette modification s’appliquera aux futures connexions) :
    
   1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre locataire Azure AD B2C.

   2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.

   3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.

   4. Sélectionnez le flux d’utilisateur, puis sélectionnez **Langues**. Sélectionnez la langue correspondant à l’emplacement géographique de votre organisation pour ouvrir le volet de détails de la langue. (Pour cet exemple, nous sélectionnons **Anglais en** pour les États-Unis). Sélectionnez la **Page d’authentification multifacteur**, puis sélectionnez **Télécharger les valeurs par défaut (en)** .
 
      ![Charger de nouvelles valeurs de remplacement pour télécharger les valeurs par défaut](media/phone-based-mfa/download-defaults.png)

   5. Ouvrez le fichier JSON qui a été téléchargé à l’étape précédente. Dans le fichier, recherchez `DEFAULT` et remplacez la ligne par `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"`. Veillez à définir `Overrides` sur `true`.

   > [!NOTE]
   > Vous pouvez personnaliser la liste des codes de pays autorisés dans l’élément `countryList` (voir l’[Exemple de page d’authentification par facteur de téléphone](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Enregistrez le fichier JSON. Dans le volet de détails de la langue, sous **Charger de nouvelles valeurs de remplacement**, sélectionnez le fichier JSON modifié pour le charger.

   8. Fermez le volet et sélectionnez **Exécuter le flux d’utilisateur**. Pour cet exemple, vérifiez qu’**États-Unis** est le seul code pays disponible dans la liste déroulante :
 
      ![Liste déroulante des codes de pays](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrir [Identity Protection et l’accès conditionnel pour Azure AD B2C](conditional-access-identity-protection-overview.md) 

- Appliquer l’[accès conditionnel à des flux d’utilisateurs dans Azure Active Directory B2C](conditional-access-user-flow.md)