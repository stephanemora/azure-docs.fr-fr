---
title: 'Méthodes d’authentification et fonctionnalités : Azure Active Directory'
description: En savoir plus sur les différentes méthodes et fonctionnalités d’authentification disponibles dans Microsoft Azure Active Directory pour contribuer à l’amélioration et à la sécurisation des événements de connexion
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 4c224f40996625ead4592a3bf50e02f0877c76db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051308"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quelles sont les méthodes d’authentification et de vérification disponibles dans Microsoft Azure Active Directory ?

Dans le cadre de l’expérience de connexion pour les comptes Microsoft Azure Active Directory (Azure AD), un utilisateur peut s’authentifier de différentes manières. Un nom d’utilisateur et un mot de passe sont les moyens les plus courant pour qu’un utilisateur fournisse des informations d’identification. Grâce aux fonctionnalités d’authentification et de sécurité modernes d’Azure AD, ce mot de passe de base peut être complété ou remplacé par des méthodes d’authentification supplémentaires.

Un utilisateur d’Azure AD peut choisir de s’authentifier à l’aide de l’une des méthodes d’authentification suivantes :

* Nom d’utilisateur et mot de passe traditionnels
* Connexion sans mot de passe avec l’application Microsoft Authenticator
* Jeton de sécurité matériel OATH ou clé de sécurité FIDO2
* Connexion sans mot de passe par SMS

De nombreux comptes Azure AD prennent en charge la réinitialisation de mot de passe en libre-service (SSPR) ou Azure MFA. Ces fonctionnalités incluent des méthodes de vérification supplémentaires, comme un appel téléphonique ou des questions de sécurité. Il est recommandé de demander aux utilisateurs d’enregistrer plusieurs méthodes de vérification. Lorsqu’une méthode n’est pas disponible pour un utilisateur, il peut choisir de s’authentifier avec une autre méthode.

Le tableau suivant décrit les méthodes disponibles pour l’authentification principale ou secondaire :

| Méthode | Authentification principale | Authentification secondaire |
| --- | --- | --- |
| [Mot de passe](#password) | Oui | |
| [Application Microsoft Authenticator](#microsoft-authenticator-app) | Oui (préversion) | Authentification multifacteur et réinitialisation de mot de passe en libre-service |
| [Clés de sécurité FIDO2 (version préliminaire)](#fido2-security-keys) | Oui | MFA uniquement |
| [Jetons logiciels OATH](#oath-software-tokens) | Non | MFA |
| [Jetons matériels OATH (version préliminaire)](#oath-hardware-tokens-preview) | Oui | MFA |
| [SMS](#phone-options) | Oui (préversion) | Authentification multifacteur et réinitialisation de mot de passe en libre-service |
| [Appel vocal](#phone-options) | Non | Authentification multifacteur et réinitialisation de mot de passe en libre-service |
| [Question de sécurité](#security-questions) | Non | SSPR uniquement |
| [Adresse de messagerie](#email-address) | Non | SSPR uniquement |
| [Mots de passe d’application](#app-passwords) | Non | Authentification aultifacteur uniquement dans certains cas |

Cet article décrit les différentes méthodes d’authentification et de vérification disponibles dans Azure AD ainsi que les limitations ou restrictions spécifiques.

![Méthodes d’authentification en cours d’utilisation sur l’écran de connexion](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Mot de passe

Un mot de passe Azure AD est souvent l’une des méthodes d’authentification principales. Vous ne pouvez pas désactiver la méthode d’authentification par mot de passe.

Même si vous utilisez une méthode d’authentification telle que la [connexion par SMS](howto-authentication-sms-signin.md) lorsque l’utilisateur n’utilise pas son mot de passe pour se connecter, la méthode d’authentification par mot de passe reste disponible.

## <a name="microsoft-authenticator-app"></a>Application Microsoft Authenticator

L’application Authenticator fournit un niveau de sécurité supplémentaire pour votre compte Azure AD professionnel ou scolaire, ou pour votre compte Microsoft. Elle est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) et [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Avec l’application Microsoft Authenticator, les utilisateurs peuvent s’authentifier sans mot de passe lors de la connexion, ou avec une option de vérification supplémentaire lors de la réinitialisation du mot de passe en libre-service (SSPR) ou d’événements d’authentification multifacteur Azure.

Les utilisateurs peuvent recevoir une notification par le biais de l’application mobile leur permettant d’accepter ou de refuser. Ils peuvent également utiliser l’application Authenticator pour générer un code de vérification OATH pouvant être entré dans une interface de connexion. Si vous activez à la fois la notification et le code de vérification, les utilisateurs s’enregistrant sur l’application Authenticator peuvent utiliser une des deux méthodes pour vérifier leur identité.

Pour utiliser l’application Authenticator dans une invite de connexion au lieu de la combinaison nom d’utilisateur/mot de passe, consultez [Activer la connexion sans mot de passe avec l'application Microsoft Authenticator (préversion)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Les utilisateurs n’ont pas la possibilité d’enregistrer leur application mobile lorsqu’ils activent la réinitialisation de mot de passe en libre-service. En revanche, les utilisateurs peuvent inscrire leur application mobile sur [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou dans le cadre de l’inscription d’informations de sécurité combinée sur [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

### <a name="notification-through-mobile-app"></a>Notification via une application mobile

L’application Authenticator peut aider à empêcher tout accès non autorisé aux comptes et à arrêter les transactions frauduleuses en envoyant une notification à votre smartphone ou tablette. Les utilisateurs voient la notification et, si elle est légitime, sélectionnent **Vérifier**. Sinon, ils peuvent sélectionner **Refuser**.

![Capture d’écran d’exemple de l’invite du navigateur Web affichant la notification de l’application Authenticator permettant de terminer le processus de connexion](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Si le personnel de votre organisation travaille ou voyage en Chine, la méthode de *notification via application mobile* sur les appareils Android ne fonctionnera pas dans ce pays ou cette région. D’autres méthodes d’authentification doivent être proposées aux utilisateurs.

### <a name="verification-code-from-mobile-app"></a>Code de vérification de l’application mobile

L'application Authenticator peut être utilisée comme jeton logiciel pour générer un code de vérification OATH. Après avoir saisi votre nom d’utilisateur et votre mot de passe, vous entrez le code fourni par l’application Authenticator dans l’interface de connexion. Le code de vérification fournit un deuxième formulaire d’authentification.

Les utilisateurs peuvent combiner jusqu’à cinq jetons matériels OATH ou des applications d’authentification, comme l’application Microsoft Authenticator, configurées pour une utilisation à tout moment.

> [!WARNING]
> Pour garantir le niveau de sécurité le plus élevé quand une seule méthode est requise pour la réinitialisation de mot de passe en libre-service, le code de vérification est la seule option à la disposition des utilisateurs.
>
> Lorsque deux méthodes sont requises, les utilisateurs peuvent effectuer la réinitialisation avec soit une notification, soit un code de vérification en plus de n’importe quelle autre méthode activée.

## <a name="fido2-security-keys"></a>Clés de sécurité FIDO2

La FIDO (Fast Identity Online) Alliance permet de promouvoir les normes d’authentification ouverte et de réduire l’utilisation des mots de passe en tant qu’authentification. FIDO2 est la dernière norme qui incorpore la norme d’authentification Web (WebAuthn).

Pour utiliser les clés de sécurité FIDO2 dans une invite de connexion au lieu de la combinaison nom d’utilisateur/mot de passe, consultez [Activer la connexion sans mot de passe par clé de sécurité FIDO2 (version préliminaire)](howto-authentication-passwordless-security-key.md).

Les utilisateurs peuvent enregistrer, puis sélectionner une clé de sécurité FIDO2 dans l’interface de connexion en tant que principal moyen d’authentification. Ces clés de sécurité FIDO2 sont généralement des périphériques USB, mais elles peuvent également utiliser les technologies Bluetooth ou NFC. Avec un périphérique matériel gérant l’authentification, la sécurité d’un compte augmente car il n’existe aucun mot de passe qui pourrait être exposé ou deviné.

Les clés de sécurité FIDO2 sont actuellement en version préliminaire dans Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-tokens"></a>Jetons OATH

Les mots de passe à usage unique et durée définie (TOTP) OATH forment une norme ouverte qui spécifie le mode de génération des codes de mot de passe (OTP) à usage unique. Les mots de passe à usage unique et durée définie OATH peuvent être implémentés à l’aide de logiciels ou de matériels permettant de générer des codes. Azure AD ne prend pas en charge les HOTP OATH, une norme de génération de code différente.

### <a name="oath-software-tokens"></a>Jetons logiciels OATH

Les jetons logiciels OATH sont généralement des applications telles que l’application Microsoft Authenticator et d’autres applications d’authentification. Azure AD génère la clé secrète, ou la valeur de départ, qui est entrée dans l’application et utilisée pour générer chaque code OTP.

L’application Authenticator génère automatiquement des codes lorsqu’elle est configurée pour effectuer des notifications push, afin que l’utilisateur dispose d’une sauvegarde, même si son appareil n’a pas de connectivité. Les applications tierces qui utilisent les TOTP OATH pour générer des codes peuvent également être utilisées.

Certains jetons matériels TOTP OATH sont programmables, ce qui signifie qu’ils ne sont pas fournis avec une clé secrète ou une valeur de départ préprogrammée. Ces jetons matériels programmables peuvent être configurés à l’aide de la clé secrète ou de la valeur initiale obtenue à partir du flux d’installation du jeton logiciel. Les clients peuvent acheter ces jetons auprès du fournisseur de leur choix et utiliser la clé secrète ou la valeur initiale dans le processus d’installation de leur fournisseur.

### <a name="oath-hardware-tokens-preview"></a>Jetons matériels OATH (version préliminaire)

Azure AD prend en charge l’utilisation de jetons OATH-TOTP SHA-1 qui actualisent les codes toutes les 30 ou 60 secondes. Les clients peuvent acheter ces jetons auprès du fournisseur de leur choix.

Les jetons matériels OATH TOTP sont généralement fournis avec une clé secrète, ou valeur initiale, préprogrammée dans le jeton. Ces clés doivent être entrées dans Azure AD comme décrit dans les étapes suivantes. Les clés secrètes sont limitées à 128 caractères et cette limite peut ne pas être compatible avec tous les jetons. La clé secrète peut contenir uniquement les caractères *a à z* ou *A à Z* et les chiffres *1 à 7*, et doit être encodée en *Base32*.

Vous pouvez également configurer des jetons matériels OATH TOTP programmables qui peuvent être réamorcés avec Azure AD dans le processus d’installation des jetons logiciels.

Les jetons matériels OATH sont pris en charge dans le cadre d’une préversion publique. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Chargement des jetons OATH dans le panneau de jetons OATH MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Après avoir obtenu les jetons, vous devez les charger dans un fichier de valeurs séparées par des virgules (CSV), contenant l'UPN, le numéro de série, la clé secrète, l’intervalle de temps, le fabricant et le modèle, comme indiqué dans l’exemple suivant :

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Veillez à inclure la ligne d’en-tête dans votre fichier CSV.

Une fois que le fichier a été correctement mis en forme au format CSV, l’administrateur peut ensuite se connecter au Portail Azure et accéder à **Azure Active Directory > Sécurité > MFA > Jetons OATH** afin de charger le fichier CSV créé.

L’opération peut prendre plusieurs minutes selon la taille du fichier CSV. Sélectionnez le bouton **Actualiser** pour obtenir l’état actuel. Si le fichier contient des erreurs, vous pouvez télécharger un fichier CSV qui répertorie les erreurs à résoudre. Les noms de champs dans le fichier CSV téléchargé sont différents de ceux de la version chargée.

Une fois que toutes les erreurs ont été résolues, l’administrateur peut activer chaque clé en sélectionnant **Activer** pour le jeton et en entrant l’OTP affiché sur le jeton.

Les utilisateurs peuvent combiner jusqu’à cinq jetons matériels OATH ou des applications d’authentification, comme l’application Microsoft Authenticator, configurées pour une utilisation à tout moment.

## <a name="phone-options"></a>Options de téléphone

Pour une authentification directe à l’aide d’un message texte, vous pouvez [Configurer et activer des utilisateurs pour l’authentification par SMS (préversion)](howto-authentication-sms-signin.md). La connexion par SMS est idéale pour les travailleurs de première ligne. Avec la connexion par SMS, les utilisateurs n’ont pas besoin de connaître le nom d’utilisateur et le mot de passe pour accéder aux applications et aux services. L’utilisateur entre son numéro de téléphone mobile enregistré, reçoit un SMS contenant un code de vérification et l’entre dans l’interface de connexion.

Les utilisateurs peuvent également s’authentifier à l’aide d’un téléphone mobile ou d’un téléphone de bureau comme forme secondaire d’authentification utilisée lors de l’authentification multifacteur Azure ou de la réinitialisation de mot de passe en libre-service (SSPR).

Pour que tout fonctionne correctement, les numéros de téléphone doivent être au format *+CodePays NuméroTéléphone*, par exemple : *+1 4251234567*.

> [!NOTE]
> Il doit y avoir un espace entre l’indicatif du pays et le numéro de téléphone.
>
> La réinitialisation du mot de passe ne prend pas en charge les extensions de téléphone. Même au format *+1 4251234567X12345*, les extensions sont supprimées avant l’appel.

### <a name="mobile-phone-verification"></a>Vérification du téléphone mobile

Pour Azure MFA ou SSPR, les utilisateurs peuvent choisir de recevoir un SMS contenant un code de vérification à entrer dans l’interface de connexion ou de recevoir un appel téléphonique ainsi qu’une invite permettant d’entrer le code confidentiel défini.

Si les utilisateurs ne souhaitent pas que leur numéro de téléphone mobile soit visible dans le répertoire, tout en ayant la possibilité de l’utiliser pour la réinitialisation du mot de passe, les administrateurs ne doivent pas renseigner le numéro de téléphone dans le répertoire. Au lieu de cela, les utilisateurs doivent renseigner leur attribut de **téléphone d’authentification** via l’inscription d’informations de sécurité combinée sur [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Les administrateurs peuvent consulter ces informations dans le profil de l’utilisateur, mais elles ne sont pas publiées ailleurs.

![Capture d’écran du Portail Azure affichant les méthodes d’authentification avec un numéro de téléphone renseigné](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft ne garantit pas que les notifications Microsoft Azure Multi-Factor Authentication envoyées par SMS ou message vocal proviennent systématiquement du même numéro. Dans l’intérêt de nos utilisateurs, nous pouvons à tout moment ajouter ou supprimer des codes courts pour refléter les ajustements apportés aux itinéraires et améliorer la remise des SMS. Microsoft ne prend pas en charge les codes courts pour les pays/régions en dehors des États-Unis et du Canada.

#### <a name="text-message-verification"></a>Vérification des SMS

Avec la vérification des SMS dans le cadre de Microsoft Azure Multi-Factor Authentication et de SSPR, un SMS contenant un code de vérification est envoyé au numéro de téléphone mobile. Pour terminer le processus de connexion, le code de vérification fourni est entré dans l’interface de connexion.

#### <a name="phone-call-verification"></a>Vérification de l’appel téléphonique

Avec la vérification de l’appel téléphonique dans le cadre de Microsoft Azure Multi-Factor Authentication et de SSPR, un appel vocal automatique est effectué sur le numéro de téléphone enregistré par l’utilisateur. Pour terminer le processus de connexion, l’utilisateur est invité à entrer son numéro de PIN suivi de # à l’aide du clavier.

### <a name="office-phone-verification"></a>Vérification du téléphone de bureau

L’attribut téléphone de bureau est géré par l’administrateur Azure AD et ne peut pas être enregistré par un utilisateur.

Avec la vérification de l’appel téléphonique dans le cadre d’Azure MFA et de SSPR, un appel vocal automatique est effectué sur le numéro de téléphone enregistré par l’utilisateur. Pour terminer le processus de connexion, l’utilisateur est invité à entrer son numéro de PIN suivi de # à l’aide du clavier.

### <a name="troubleshooting-phone-options"></a>Résolution des problèmes liés aux options du téléphone

Si vous rencontrez des problèmes avec l’authentification par téléphone pour Azure AD, passez en revue les étapes de dépannage suivantes :

* ID de l’appelant bloqué sur un seul appareil.
   * Examinez les numéros bloqués configurés sur l’appareil.
* Numéro de téléphone erroné ou code de pays/région incorrect, ou confusion entre le numéro de téléphone personnel et le numéro de téléphone professionnel.
   * Résolvez les problèmes liés à l’objet utilisateur et aux méthodes d’authentification. Assurez-vous que les numéros de téléphone appropriés sont enregistrés.
* Code PIN entré incorrect.
   * Assurez-vous que l’utilisateur a utilisé le code PIN approprié comme enregistré pour son compte.
* Appel transféré vers la messagerie vocale.
   * Vérifiez que le téléphone de l'utilisateur est allumé et que le service est disponible dans sa zone ou utilisez une autre méthode.
* L’utilisateur est bloqué
   * Demander à un administrateur Azure AD de débloquer l’utilisateur dans le Portail Azure.
* L’option SMS n’est pas inscrite sur l’appareil.
   * Demandez à l’utilisateur de changer de méthode ou d’activer les SMS sur l’appareil.
* Fournisseurs de télécommunications en faute, par exemple : aucune entrée téléphone détectée, problèmes de tonalités DTMF manquantes, ID de l’appelant bloqué sur plusieurs appareils, ou SMS bloqués sur plusieurs appareils.
   * Microsoft utilise plusieurs fournisseurs de télécommunications pour acheminer les appels téléphoniques et les messages SMS pour l’authentification. Si vous rencontrez des problèmes parmi ceux mentionnés ci-dessus, demandez à un utilisateur d’essayer d’utiliser la méthode au moins cinq fois en 5 minutes, et soyez en possession de ses informations lorsque vous contactez le support Microsoft.

## <a name="security-questions"></a>Questions de sécurité

Les questions de sécurité ne sont pas utilisées comme méthode d’authentification durant un événement de connexion. Au lieu de cela, vous pouvez utiliser les questions de sécurité durant le processus de réinitialisation de mot de passe en libre-service (SSPR) pour confirmer votre identité. Les comptes administrateurs ne peuvent pas utiliser les questions de sécurité comme méthode de vérification avec SSPR.

Quand les utilisateurs s’enregistrent pour SSPR, ils sont invités à choisir les méthodes d’authentification à utiliser. S’ils choisissent d’utiliser des questions de sécurité, ils les choisissent parmi un ensemble de questions et fournissent leurs propres réponses.

![Capture d’écran du Portail Azure affichant les méthodes d’authentification et les options de question de sécurité](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur du répertoire et ne peuvent être posées qu’aux utilisateurs lors de l’inscription. L’administrateur ne peut pas lire ni modifier les questions ou les réponses des utilisateurs.

Les questions de sécurité peuvent être moins sécurisées que d’autres méthodes, car certaines personnes peuvent connaître les réponses aux questions d’un autre utilisateur. Si vous utilisez des questions de sécurité avec SSPR, il est recommandé de les utiliser conjointement avec une autre méthode. Un utilisateur peut être invité à utiliser l’application Microsoft Authenticator ou l’authentification par téléphone pour vérifier son identité pendant le processus SSPR, il peut également avoir à choisir des questions de sécurité, uniquement s’il n’a pas de téléphone ou d’appareil enregistré avec lui.

### <a name="predefined-questions"></a>Questions prédéfinies

Les questions de sécurité prédéfinies suivantes peuvent être utilisées comme méthode de vérification avec SSPR. Toutes ces questions de sécurité sont traduites et localisées dans l’ensemble des langues Office 365 en fonction des paramètres régionaux du navigateur de l’utilisateur :

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

### <a name="custom-security-questions"></a>Questions de sécurité personnalisées

Pour plus de flexibilité, vous pouvez définir vos questions de sécurité personnalisées. La longueur maximale d’une question de sécurité personnalisée est de 200 caractères.

Les questions de sécurité personnalisées ne sont pas automatiquement localisées, comme avec les questions de sécurité par défaut. Toutes les questions personnalisées s’affichent dans la même langue que celle dans laquelle elles sont entrées dans l’interface utilisateur d’administration, même si les paramètres régionaux du navigateur de l’utilisateur sont différents. Si vous avez besoin de questions localisées, vous devez utiliser les questions prédéfinies.

### <a name="security-question-requirements"></a>Conditions relatives aux questions de sécurité

Pour les questions de sécurité par défaut et personnalisées, les exigences et limitations suivantes s’appliquent :

* La limite minimale du nombre de caractères par réponse est de trois caractères.
* La limite maximale du nombre de caractères par réponse est de 40 caractères.
* L’utilisateur ne peut pas répondre plusieurs fois à la même question.
* L’utilisateur ne peut pas fournir la même réponse à plusieurs questions.
* N’importe quel jeu de caractères peut être utilisé pour définir les questions et les réponses, y compris les caractères Unicode.
* Le nombre de questions défini doit être supérieur ou égal au nombre de questions requises pour l’inscription.

## <a name="email-address"></a>Adresse de messagerie

Une adresse e-mail ne peut pas être utilisée en tant que méthode d’authentification directe. L’adresse e-mail est uniquement disponible en tant qu’option de vérification pour la réinitialisation du mot de passe en libre-service (SSPR). Lorsque l’adresse de messagerie est sélectionnée durant la réinitialisation de mot de passe en libre-service, un e-mail est envoyé à l’utilisateur pour terminer le processus d’authentification/vérification.

Lors de l’enregistrement pour la réinitialisation de mot de passe en libre-service, l’utilisateur fournit l’adresse de messagerie à utiliser. Il est recommandé d’utiliser un compte de messagerie différent du compte d’entreprise pour s’assurer qu’il peut y accéder durant la réinitialisation de mot de passe en libre-service.

## <a name="app-passwords"></a>Mots de passe d'application

Certaines anciennes applications sans navigateur ne comprennent pas les pauses ou les interruptions dans le processus d’authentification. Si un utilisateur a activé l’authentification multifacteur et tente d’utiliser l’une de ces anciennes applications sans navigateur, il ne peut généralement pas s’authentifier correctement. Un mot de passe d’application permet aux utilisateurs de continuer à s’authentifier avec les anciennes applications sans navigateur sans interruption.

Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d’application. Si vous avez besoin de permettre aux utilisateurs de créer des mots de passe d’application, sélectionnez l’option **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur** sous les *Paramètres de service* des propriétés des utilisateurs Microsoft Azure Multi-Factor Authentication.

![Capture d’écran du Portail Azure qui affiche les paramètres de service pour l’authentification multifacteur afin d’autoriser l’utilisateur des mots de passe d’application](media/concept-authentication-methods/app-password-authentication-method.png)

Si vous appliquez l’authentification multifacteur Azure via des stratégies d’accès conditionnel et non via l’authentification multifacteur par utilisateur, vous ne pouvez pas créer de mots de passe d’application. Les applications modernes qui utilisent des stratégies d’accès conditionnel pour contrôler l’accès n’ont pas besoin de mots de passe d’application.

Si votre organisation est fédérée à Azure AD pour l’authentification unique et que vous utilisez Azure Multi-Factor Authentication, tenez compte des points suivants :

* Le mot de passe de l’application est vérifié par Azure AD, il contourne ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez le mot de passe d’application. Les mots de passe des utilisateurs fédérés (SSO) sont stockés dans l’ID d’organisation. Si l’utilisateur quitte l’entreprise, ces informations doivent être stockées dans l’ID d’organisation à l’aide de DirSync. Les événements de désactivation ou de suppression de compte peuvent mettre jusqu’à trois heures à se synchroniser, ce qui peut retarder la désactivation ou la suppression des mots de passe d’application dans Azure AD.
* Les paramètres de contrôle d’accès client locaux ne sont pas honorés par les mots de passe d’application.
* Aucune authentification locale de journalisation ou de fonctionnalité d’audit n’est disponible pour les mots de passe d’application.
* Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d'une combinaison de noms d'utilisateur, de mots de passe et de mots de passe d'application, lors de l'utilisation de l'authentification multifacteur, selon l'emplacement de cette dernière.
    * Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’une organisation.
    * Pour les clients qui s'authentifient auprès d'Azure AD, vous devez utiliser le mot de passe d'application.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, consultez le [tutoriel sur la réinitialisation du mot de passe en libre-service][tutorial-sspr] et [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Pour en savoir plus sur les concepts de SSPR, consultez [Fonctionnement de la réinitialisation de mot de passe en libre-service dans Azure AD][concept-sspr].

Pour plus d’informations sur les concepts MFA, consultez [Azure Multi-Factor Authentication : fonctionnement][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md