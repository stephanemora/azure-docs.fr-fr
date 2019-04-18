---
title: Méthodes d’authentification - Azure Active Directory
description: Quelles sont les méthodes d’authentification disponibles dans Azure AD pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service ?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0c9af1a9ad8b816809f661d368133997f55329d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360639"
---
# <a name="what-are-authentication-methods"></a>Que sont les méthodes d’authentification ?

En tant qu’administrateur, choix de méthodes d’authentification pour Azure multi-Factor Authentication et de réinitialisation de mot de passe libre-service (SSPR) il est recommandé de demander aux utilisateurs d’inscrire plusieurs méthodes d’authentification. Quand une méthode d’authentification n’est pas disponible pour un utilisateur, ils peuvent choisir de s’authentifier avec une autre méthode.

Les administrateurs peuvent définir, dans la stratégie, les méthodes d’authentification disponibles pour les utilisateurs avec les fonctionnalités d’authentification multifacteur et de réinitialisation de mot de passe en libre-service. Certaines méthodes d’authentification peuvent ne pas être disponibles pour toutes les fonctionnalités. Pour plus d’informations sur la configuration de vos stratégies consultez les articles [comment déployer avec succès la réinitialisation de mot de passe libre-service](howto-sspr-deployment.md) et [planification basée sur le cloud Azure multi-Factor Authentication](howto-mfa-getstarted.md)

Microsoft recommande vivement aux administrateurs de permettre aux utilisateurs de sélectionner plus que le nombre minimal requis de méthodes d’authentification au cas où une ne serait pas accessible.

|Méthode d'authentification|Usage|
| --- | --- |
| Mot de passe | Authentification multifacteur et réinitialisation de mot de passe en libre-service |
| Questions de sécurité | Réinitialisation de mot de passe en libre-service uniquement |
| Adresse de messagerie | Réinitialisation de mot de passe en libre-service uniquement |
| Application Microsoft Authenticator | Authentification multifacteur et préversion publique pour la réinitialisation de mot de passe en libre-service |
| Jetons matériels OATH | Préversion publique pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service |
| sms | Authentification multifacteur et réinitialisation de mot de passe en libre-service |
| Appel vocal | Authentification multifacteur et réinitialisation de mot de passe en libre-service |
| Mots de passe d'application | Authentification aultifacteur uniquement dans certains cas |

![Méthodes d’authentification en cours d’utilisation sur l’écran de connexion](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Les jetons matériels OATH pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service, et la notification d’application mobile ou le code d’application mobile en tant que méthodes de réinitialisation de mot de passe en libre-service Azure AD, sont des fonctionnalités disponibles dans la préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Mot de passe

Votre mot de passe Azure AD est considéré comme une méthode d’authentification. C’est la seule méthode qui **ne peut pas être désactivée**.

## <a name="security-questions"></a>Questions de sécurité

Les questions de sécurité sont disponibles **uniquement pour la réinitialisation de mot de passe en libre-service Azure AD** avec des comptes non administrateurs.

Si vous utilisez des questions de sécurité, nous vous recommandons de les utiliser conjointement avec une autre méthode. Les questions de sécurité peuvent être moins sécurisées que d’autres méthodes, car certaines personnes peuvent connaître les réponses aux questions d’un autre utilisateur.

> [!NOTE]
> Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur du répertoire et ne peuvent être posées qu’aux utilisateurs lors de l’inscription. L’administrateur ne peut pas lire ni modifier les questions ou les réponses des utilisateurs.
>

### <a name="predefined-questions"></a>Questions prédéfinies

* Dans quelle ville avez-vous rencontré votre premier(ère) époux/épouse ou compagnon/compagne ?
* Dans quelle ville vos parents se sont-ils rencontrés ?
* Dans quelle ville vit votre frère ou sœur le/la plus proche ?
* Dans quelle ville votre père est-il né ?
* Dans quelle ville était votre premier travail ?
* Dans quelle ville votre mère est-elle née ?
* Dans quelle ville étiez-vous pour le Nouvel an de l’année 2000 ?
* Quel était le nom de votre professeur préféré au lycée ?
* Quel est le nom d’un établissement d’enseignement supérieur auquel vous avez postulé mais auquel vous n’êtes finalement pas allé ?
* Quel est le nom de l’endroit où vous avez organisé la réception de votre premier mariage ?
* Quel est le deuxième prénom de votre père ?
* Quel est votre aliment préféré ?
* Quels sont les nom et prénom de votre grand-mère maternelle ?
* Quel est le deuxième prénom de votre mère ?
* Quel est le mois et l’année de naissance de votre frère/sœur aîné(e) ? (par exemple, novembre 1985)
* Quel est le deuxième prénom de votre frère/sœur aîné(e) ?
* Quels sont les nom et prénom de votre grand-père paternel ?
* Quel est le deuxième prénom de votre frère/sœur cadet(te) ?
* Où avez-vous effectué votre dernière année de l’école primaire ?
* Quels étaient les nom et prénom de votre meilleur ami quand vous étiez enfant ?
* Quels étaient les nom et prénom de votre premier(ère) petit(e) ami(e) ?
* Quel était le nom de votre instituteur préféré à l’école primaire ?
* Quels étaient la marque et le modèle de votre première voiture ou moto ?
* Quel était le nom de votre toute première école ?
* Quel était le nom de l’hôpital ou de la clinique où vous êtes né ?
* Quel était le nom de la rue de la première maison de votre enfance ?
* Quel était le nom de votre héros d’enfance ?
* Quel était le nom de votre peluche préférée ?
* Quel était le nom de votre premier animal domestique ?
* Quel était votre surnom quand vous étiez enfant ?
* Quel était votre sport préféré au lycée ?
* Quel a été votre premier travail ?
* Quels étaient les quatre derniers chiffres de votre numéro de téléphone quand vous étiez enfant ?
* Quand vous étiez petit, qu’auriez-vous voulu être ?
* Quelle est la personne la plus célèbre que vous ayez jamais rencontrée ?

Toutes les questions de sécurité prédéfinies sont traduites et localisées dans l’ensemble des langues Office 365 en fonction des paramètres régionaux du navigateur de l’utilisateur.

### <a name="custom-security-questions"></a>Questions de sécurité personnalisées

Les questions de sécurité personnalisées ne sont pas localisées. Toutes les questions personnalisées s’affichent dans la même langue que celle dans laquelle elles sont entrées dans l’interface utilisateur d’administration, même si les paramètres régionaux du navigateur de l’utilisateur sont différents. Si vous avez besoin de questions localisées, vous devez utiliser les questions prédéfinies.

La longueur maximale d’une question de sécurité personnalisée est de 200 caractères.

### <a name="security-question-requirements"></a>Conditions relatives aux questions de sécurité

* La limite minimale du nombre de caractères par réponse est de trois caractères.
* La limite maximale du nombre de caractères par réponse est de 40 caractères.
* L’utilisateur ne peut pas répondre plusieurs fois à la même question.
* L’utilisateur ne peut pas fournir la même réponse à plusieurs questions.
* N’importe quel jeu de caractères peut être utilisé pour définir les questions et les réponses, y compris les caractères Unicode.
* Le nombre de questions défini doit être supérieur ou égal au nombre de questions requises pour l’inscription.

## <a name="email-address"></a>Adresse de messagerie

L’adresse de messagerie est disponible **uniquement pour la réinitialisation de mot de passe en libre-service Azure AD**.

Microsoft recommande d’utiliser un compte de messagerie qui n’exige pas le mot de passe de l’utilisateur Azure AD pour y accéder.

## <a name="microsoft-authenticator-app"></a>Application Microsoft Authenticator

L’application Microsoft Authenticator fournit un niveau supplémentaire de sécurité pour votre compte professionnel ou scolaire Azure AD, ou votre compte Microsoft.

L’application Microsoft Authenticator est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) et [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Les utilisateurs n’auront pas la possibilité d’inscrire leur application mobile lors de l’inscription pour la réinitialisation de mot de passe en libre-service. En revanche, les utilisateurs peuvent inscrire leur application mobile sur [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou dans la préversion de l’inscription des informations de sécurité sur [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Notification via une application mobile

L’application Microsoft Authenticator peut aider à empêcher tout accès non autorisé aux comptes et à arrêter les transactions frauduleuses en envoyant une notification à votre smartphone ou tablette. Les utilisateurs voient la notification et, si elle est légitime, sélectionnent Vérifier. Sinon, ils peuvent sélectionner Refuser.

> [!WARNING]
> Quand une seule méthode est requise pour la réinitialisation de mot de passe en libre-service, le code de vérification est la seule option à la disposition des utilisateurs **pour garantir le niveau de sécurité le plus élevé**.
>
> Lorsque deux méthodes sont requises, les utilisateurs seront en mesure d’effectuer la réinitialisation avec **SOIT** une notification, **SOIT** un code de vérification en plus de n’importe quelle autre méthode activée.
>

Si vous activez l’utilisation de la notification par le biais de l’application mobile et du code de vérification de l’application mobile, les utilisateurs qui inscrivent l’application Microsoft Authenticator à l’aide d’une notification peuvent utiliser aussi bien la notification que le code pour vérifier leur identité.

> [!NOTE]
> Si votre organisation a personnel travaillant dans ou en déplacement à la Chine, le **Notification via application mobile** méthode sur **les appareils Android** ne fonctionne pas dans ce pays. Autres méthodes doivent être accessibles aux utilisateurs.

### <a name="verification-code-from-mobile-app"></a>Code de vérification de l’application mobile

L'application Microsoft Authenticator ou une autre application tierce peut être utilisée comme jeton logiciel pour générer un code de vérification OATH. Après avoir saisi votre nom d’utilisateur et votre mot de passe, vous entrez le code fourni par l’application dans l’écran de connexion. Le code de vérification fournit un deuxième formulaire d’authentification.

> [!WARNING]
> Quand une seule méthode est requise pour la réinitialisation de mot de passe en libre-service, le code de vérification est la seule option à la disposition des utilisateurs **pour garantir le niveau de sécurité le plus élevé**.
>

Les utilisateurs devront peut-être une combinaison de jetons de matériels jusqu'à cinq serment ou des applications de l’authentificateur, telles que l’application Microsoft Authenticator configuré pour une utilisation à tout moment.

## <a name="oath-hardware-tokens-public-preview"></a>Jetons matériels OATH (préversion publique)

OATH est une norme ouverte qui spécifie le mode de génération des codes de mot de passe (OTP) à usage unique. Azure AD prendra maintenant en charge l’utilisation des jetons OATH-TOTP SHA-1 de 30 secondes ou 60 secondes. Les clients peuvent se procurer ces jetons auprès du fournisseur de leur choix. Les clés secrètes sont limités à 128 caractères, ce qui n’est peut-être pas compatibles avec tous les jetons.

![Chargement des jetons OATH dans le panneau de jetons OATH du serveur MFA](media/concept-authentication-methods/oath-tokens-azure-ad.png)

Les jetons matériels OATH seront pris en charge dans le cadre d’une préversion publique. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Après avoir obtenu les jetons, vous devez les charger dans un fichier de valeurs séparées par des virgules (CSV), contenant l’UPN, le numéro de série, le secret, l’intervalle de temps, le fabricant et le modèle, comme dans l’exemple suivant.

```csv
upn,serial number,secret key,timeinterval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Veillez à inclure la ligne d’en-tête dans votre fichier CSV comme indiqué ci-dessus.

Une fois que le fichier a été correctement mis en forme au format CSV, l’administrateur peut ensuite se connecter au portail Azure et accéder à **Azure Active Directory**, **Serveur MFA**, **Jetons OATH** afin de charger le fichier CSV créé.

L’opération peut prendre plusieurs minutes selon la taille du fichier CSV. Cliquez sur le bouton **Actualiser** pour obtenir l’état actuel. Si le fichier contient des erreurs, vous pouvez télécharger un fichier CSV de toutes les erreurs pour les résoudre plus facilement.

Une fois que toutes les erreurs ont été résolues, l’administrateur peut activer chaque clé en cliquant sur **Activer** pour activer le jeton et en entrant l’OTP affiché sur le jeton.

Les utilisateurs devront peut-être une combinaison de jetons de matériels jusqu'à cinq serment ou des applications de l’authentificateur, telles que l’application Microsoft Authenticator configuré pour une utilisation à tout moment.

## <a name="mobile-phone"></a>Téléphone mobile

Deux options sont disponibles pour les utilisateurs de téléphones mobiles.

Si les utilisateurs ne souhaitent pas que leur numéro de téléphone mobile soit visible dans le répertoire, tout en ayant la possibilité de l’utiliser pour la réinitialisation du mot de passe, les administrateurs ne doivent pas le renseigner dans le répertoire. Les utilisateurs doivent renseigner leur attribut **Téléphone d’authentification** par le biais du [portail d’inscription à la réinitialisation du mot de passe](https://aka.ms/ssprsetup). Les administrateurs peuvent consulter ces informations dans le profil de l’utilisateur, mais elles ne sont pas publiées ailleurs.

Pour que tout fonctionne correctement, les numéros de téléphone doivent être au format *+CodePays NuméroTéléphone*, par exemple : +1 4255551234.

> [!NOTE]
> Il doit y avoir un espace entre l’indicatif du pays et le numéro de téléphone.
>
> La réinitialisation du mot de passe ne prend pas en charge les extensions de téléphone. Même au format +1 4255551234X12345, les extensions sont supprimées avant l’appel.

### <a name="text-message"></a>SMS

Un SMS est envoyé au numéro de téléphone mobile. Il contient un code de vérification. Entrez le code de vérification fourni dans l’interface de connexion pour continuer.

### <a name="phone-call"></a>appel téléphonique

Un appel vocal automatisé est passé au numéro de téléphone indiqué. Répondez à l’appel et appuyez sur la touche # du clavier du téléphone pour vous authentifier.

> [!IMPORTANT]
> À compter de mars de 2019 les options d’appel téléphonique ne sera pas disponible aux utilisateurs MFA et SSPR dans les locataires gratuit/essai Azure AD. Les messages SMS ne sont pas affectées par cette modification. Appel téléphonique continueront à être disponibles pour les utilisateurs dans payé des locataires Azure AD. Cette modification affecte uniquement les locataires gratuit/essai Azure AD.

## <a name="office-phone"></a>Téléphone de bureau

Un appel vocal automatisé est passé au numéro de téléphone indiqué. Répondez à l’appel et appuyez sur la touche # du clavier du téléphone pour vous authentifier.

Pour que tout fonctionne correctement, les numéros de téléphone doivent être au format *+CodePays NuméroTéléphone*, par exemple : +1 4255551234.

L’attribut Téléphone de bureau est géré par l’administrateur.

> [!IMPORTANT]
> À compter de mars de 2019 les options d’appel téléphonique ne sera pas disponible aux utilisateurs MFA et SSPR dans les locataires gratuit/essai Azure AD. Les messages SMS ne sont pas affectées par cette modification. Appel téléphonique continueront à être disponibles pour les utilisateurs dans payé des locataires Azure AD. Cette modification affecte uniquement les locataires gratuit/essai Azure AD.

> [!NOTE]
> Il doit y avoir un espace entre l’indicatif du pays et le numéro de téléphone.
>
> La réinitialisation du mot de passe ne prend pas en charge les extensions de téléphone. Même au format +1 4255551234X12345, les extensions sont supprimées avant l’appel.

## <a name="app-passwords"></a>Mots de passe d'application

Certaines applications sans navigateur ne prennent pas en charge l’authentification multifacteur. Si un utilisateur a été activé pour l’authentification multifacteur et tente d’utiliser des applications sans navigateur, il ne peut pas s’authentifier. Un mot de passe d’application permet aux utilisateurs de continuer pour s’authentifier

Si vous appliquez l’authentification multifacteur via des stratégies d’accès conditionnel et non via l’authentification multifacteur par utilisateur, vous ne pouvez pas créer des mots de passe d’application. Les applications qui utilisent des stratégies d’accès conditionnel pour contrôler l’accès n’ont pas besoin de mots de passe d’application.

Si votre organisation est fédérée (SSO) avec Azure AD et si vous vous apprêtez à utiliser l’authentification Azure MFA, voici ce que vous devez absolument savoir :

* Le mot de passe est vérifié par Azure AD et il contourne ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez le mot de passe d’application. Les mots de passe des utilisateurs fédérés (SSO) sont stockés dans l’ID d’organisation. Si l’utilisateur quitte l’entreprise, ces informations doivent être stockées dans l’ID d’organisation à l’aide de DirSync. La désactivation/suppression de compte peut mettre jusqu’à trois heures à se synchroniser, ce qui peut retarder la désactivation/suppression des mots de passe dans Azure AD.
* Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
* Aucune authentification locale de journalisation/fonctionnalité d’audit n’est disponible pour les mots de passe d’application.
* Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de noms d’utilisateur, de mots de passe et de mots de passe d’application durant l’utilisation de la vérification en deux étapes avec les clients, selon l’emplacement où ils s’authentifient. Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.
* Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d'application. Si vous avez besoin de permettre aux utilisateurs de créer des mots de passe d’application, sélectionnez l’option **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur** sous les paramètres de service.

## <a name="next-steps"></a>Étapes suivantes

[Activer la réinitialisation du mot de passe en libre-service pour votre organisation](quickstart-sspr.md)

[Activer Azure Multi-Factor Authentication pour votre organisation](howto-mfa-getstarted.md)

[Activer l’inscription de combiné dans votre client](howto-registration-mfa-sspr-combined.md)

[Documentation relative à la configuration de la méthode d’authentification de l’utilisateur final](https://aka.ms/securityinfoguide)
