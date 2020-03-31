---
title: Problèmes de configuration de l’authentification unique par mot de passe pour une application non galerie
description: Problèmes courants qui se posent lorsque vous configurez l’authentification unique (SSO) avec mot de passe pour des applications personnalisées qui ne figurent pas dans la galerie d’applications Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274146"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problèmes de configuration de l’authentification unique avec mot de passe pour une application hors galerie

Cet article décrit les problèmes courants qui peuvent se produire lorsque vous configurez l’*authentification unique avec mot de passe* (SSO) pour une application hors galerie.

## <a name="capture-sign-in-fields-for-an-app"></a>Capturer les champs de connexion d’une application

La capture des champs de connexion est prise en charge uniquement pour les pages de connexion HTML. Elle n’est pas prise en charge pour les pages de connexion non standard, comme celles qui utilisent Adobe Flash ou d’autres technologies non HTML.

Deux options s’offrent à vous pour capturer des champs de connexion pour vos applications personnalisées :

- La **capture automatique des champs de connexion** fonctionne avec la plupart des pages de connexion HTML, *si celles-ci utilisent des ID DIV connus* pour les champs de nom d’utilisateur et de mot de passe. Le code HTML dans la page est décomposé pour rechercher des ID DIV répondant à certains critères. Ces métadonnées sont enregistrées afin de pouvoir être relues ultérieurement dans l’application.

- La **capture manuelle des champs de connexion** est utilisée si le fournisseur de l’application *n’étiquète pas les champs de saisie de connexion*. La capture manuelle est également utilisée si le fournisseur *affiche plusieurs champs qui ne peuvent pas être détectés automatiquement*. Azure Active Directory (Azure AD) peut stocker les données de tous les champs de la page de connexion, si vous lui indiquez l’emplacement de ces champs sur la page.

En règle générale, si la capture automatique des champs de connexion ne fonctionne pas, essayez l’option manuelle.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capturer automatiquement les champs de connexion d’une application

Pour configurer l’authentification unique avec mot de passe à l’aide de la capture automatique des champs de connexion, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/). Connectez-vous en tant qu’administrateur général ou coadministrateur.

2. Dans le volet de navigation situé à gauche, sélectionnez **Tous les services** pour ouvrir l’extension Azure AD.

3. Saisissez **Azure Active Directory** dans la zone de recherche de filtre, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le volet de navigation Azure AD.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   > [!NOTE]
   > Si vous ne voyez pas l’application que vous voulez, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications**. Définissez l’option **Afficher** sur « Toutes les applications ».

6. Sélectionnez l’application que vous voulez configurer pour l’authentification unique.

7. Après le chargement de l’application, sélectionnez **Authentification unique** dans le volet de navigation à gauche.

8. Sélectionnez le mode **Authentification par mot de passe**.

9. Entrez l’**URL de connexion**, qui correspond à l’URL de la page où les utilisateurs entrent leur nom d’utilisateur et leur mot de passe pour se connecter. *Assurez-vous que les champs de connexion sont visibles dans la page correspondant à l’URL que vous fournissez*.

10. Sélectionnez **Enregistrer**.

    La page est automatiquement décomposée pour afficher les zones de saisie du nom d’utilisateur et du mot de passe. Vous pouvez maintenant utiliser Azure AD pour transmettre en toute sécurité des mots de passe à cette application à l’aide de l’extension de navigateur du volet d’accès.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturer manuellement les champs de connexion d’une application

Pour capturer manuellement les champs de connexion, vous devez avoir installé l’extension de navigateur du volet d’accès. En outre, votre navigateur ne doit pas s’exécuter en mode *InPrivate*, *Incognito* ni *Privé*.

Pour installer l’extension, consultez la section [Installer l’extension de navigateur du volet d’accès](#install-the-access-panel-browser-extension) de cet article.

Pour configurer l’authentification unique avec mot de passe pour une application à l’aide de la capture manuelle des champs de connexion, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/). Connectez-vous en tant qu’administrateur général ou coadministrateur.

2. Dans le volet de navigation situé à gauche, sélectionnez **Tous les services** pour ouvrir l’extension Azure AD.

3. Saisissez **Azure Active Directory** dans la zone de recherche de filtre, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le volet de navigation Azure AD.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   > [!NOTE] 
   > Si vous ne voyez pas l’application que vous voulez, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications**. Définissez l’option **Afficher** sur « Toutes les applications ».

6. Sélectionnez l’application que vous voulez configurer pour l’authentification unique.

7. Après le chargement de l’application, sélectionnez **Authentification unique** dans le volet de navigation à gauche.

8. Sélectionnez le mode **Authentification par mot de passe**.

9. Entrez l’**URL de connexion**, qui correspond à la page où les utilisateurs entrent leur nom d’utilisateur et leur mot de passe pour se connecter. *Assurez-vous que les champs de connexion sont visibles dans la page correspondant à l’URL que vous fournissez*.

10. Sélectionnez **Configurer les paramètres d’authentification unique par mot de passe *&lt;nom de l’application&gt;*** .

11. Sélectionnez **Détecter manuellement les champs de connexion**.

14. Sélectionnez **OK**.

15. Sélectionnez **Enregistrer**.

16. Suivez les instructions pour utiliser le volet d’accès.

## <a name="troubleshoot-problems"></a>Résoudre les problèmes

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>J’obtiens une erreur « Nous n’avons trouvé aucun champ de connexion à cette URL »

Vous obtenez ce message d’erreur en cas d’échec de la détection automatique de champs de connexion. Pour résoudre ce problème, essayez la détection manuelle des champs de connexion. Consultez la section [Capturer manuellement les champs de connexion d’une application](#manually-capture-sign-in-fields-for-an-app) de cet article.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>J’obtiens une erreur « Impossible d’enregistrer la configuration de l’authentification unique »

Rarement, la mise à jour de la configuration de l’authentification unique échoue. Pour résoudre ce problème, essayez d’enregistrer à nouveau cette configuration.

Si vous continuez à obtenir l’erreur, créez une demande de support. Incluez les informations qui sont décrites dans les sections [Afficher les détails de notification du portail](#view-portal-notification-details) et [Envoyer les détails de notification à un ingénieur du support technique pour obtenir de l’aide](#send-notification-details-to-a-support-engineer-to-get-help) de cet article.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Je ne peux pas détecter manuellement les champs de connexion pour mon application

Vous pouvez observer les comportements suivants lorsque la détection manuelle ne fonctionne pas :

- Le processus de capture manuelle semble fonctionner, mais les champs capturés ne sont pas corrects.

- Les champs corrects ne sont pas mis en surbrillance lors de l’exécution du processus de capture.

- Le processus de capture vous redirige comme prévu vers la page de connexion de l’application, mais rien ne se produit.

- La capture manuelle semble fonctionner, mais l’authentification unique n’a pas lieu lorsque les utilisateurs accèdent à l’application à partir du volet d’accès.

Si vous rencontrez l’un quelconque de ces problèmes, procédez comme suit :

- Assurez-vous d’avoir *installé et activé* la dernière version de l’extension de navigateur du volet d’accès. Consultez la section [Installer l’extension de navigateur du volet d’accès](#install-the-access-panel-browser-extension) de cet article.

- Assurez-vous que votre navigateur n’est pas en mode *Incognito*, *InPrivate* ni *Privé* pendant le processus de capture. L’extension du volet d’accès n’est pas prise en charge dans ces modes.

- Assurez-vous que vos utilisateurs ne tentent pas de se connecter à l’application à partir du volet d’accès alors qu’ils sont en *mode Incognito*, *InPrivate* ou *Privé*.

- Essayez à nouveau le processus de capture manuelle. Assurez-vous que les marqueurs rouges sont au-dessus des champs appropriés.

- Si le processus de capture manuelle semble cesser de répondre ou que la page de connexion ne réagit pas, tentez de réexécuter le processus de capture manuelle. Mais cette fois, à la fin du processus, appuyez sur la touche F12 pour ouvrir la console de développement de votre navigateur. Sélectionnez l’onglet **Console**. Tapez **window.location=" *&lt;URL de connexion que vous avez spécifiée lors de la configuration de l’application&gt;* "** , puis appuyez sur Entrée. Cette manipulation force une redirection de page qui met fin au processus de capture et stocke les champs qui ont été capturés.

### <a name="contact-support"></a>Contacter le support technique

Si vous avez encore des problèmes, créez une demande de support Microsoft. Décrivez ce que vous avez essayé. Incluez les détails qui sont décrits dans les sections [Afficher les détails de notification du portail](#view-portal-notification-details) et [Envoyer les détails de notification à un ingénieur du support technique pour obtenir de l’aide](#send-notification-details-to-a-support-engineer-to-get-help) de cet article (le cas échéant).

## <a name="install-the-access-panel-browser-extension"></a>Installer l’extension de navigateur du volet d’accès

Procédez comme suit :

1. Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans un navigateur pris en charge. Connectez-vous à Azure AD en tant qu’*utilisateur*.

2. Sélectionnez **application avec authentification unique par mot de passe** dans le volet d’accès.

3. Lorsque vous êtes invité à installer le logiciel, sélectionnez **Installer maintenant**.

4. Vous êtes redirigé vers une page de téléchargement pour votre navigateur. Choisissez d’**ajouter** l’extension.

5. Si vous y êtes invité, sélectionnez **Activer** ou **Autoriser**.

6. Après l’installation, redémarrez votre navigateur.

7. Connectez-vous au volet d’accès. Essayez d’ouvrir vos applications avec authentification unique par mot de passe.

Vous pouvez également télécharger directement l’extension de navigateur pour Chrome et Firefox via ces liens :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Afficher les détails de notification du portail

Pour afficher les détails de toute notification du portail, procédez comme suit :

1. Sélectionnez l’icône **Notifications** (cloche) dans le coin supérieur droit du portail Azure.

2. Sélectionnez n’importe quelle notification indiquant un état d’*erreur*. (Elles comportent un « ! » rouge.)

   > [!NOTE]
   > Vous ne pouvez pas sélectionner les notifications qui se trouvent dans l’état *Succès* ou *En cours*.

3. Le volet **Détails de la notification** s’affiche. Lisez les informations pour en savoir plus sur le problème.

5. Si vous avez encore besoin d’aide, partagez ces informations avec un ingénieur du support technique ou le groupe du produit. Sélectionnez l’icône **copier** à droite de la zone **Erreur de copie** pour copier les détails de notification à partager.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Envoyer les détails de notification à un ingénieur du support technique pour obtenir de l’aide

Il est important de partager avec le support technique *tous* les détails qui sont listés dans cette section afin de bénéficier d’une aide rapide. Pour les enregistrer, vous pouvez effectuer une capture d’écran ou sélectionner **Erreur de copie**.

Les informations suivantes expliquent ce que signifie chaque élément de notification et fournissent des exemples.

### <a name="essential-notification-items"></a>Éléments essentiels d’une notification

- **Titre** : titre descriptif de la notification.

   Exemple : *Paramètres de proxy d’application*

- **Description** : ce qui s’est produit suite à l’opération.

   Exemple : *L’URL interne entrée est déjà utilisée par une autre application.*

- **ID de notification** : ID unique de la notification.

    Exemple : *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID de demande client**: ID de la demande spécifique effectuée par votre navigateur.

    Exemple : *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Horodatage UTC** : horodatage de l’affichage de la notification, au format UTC.

    Exemple : *2017-03-23T19:50:43.7583681Z*

- **ID de transaction interne** : ID interne utilisé pour rechercher l’erreur dans nos systèmes.

    Exemple : **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** : L’utilisateur qui a exécuté l’opération.

    Exemple : *tperkins\@f128.info*

- **ID de locataire** : ID unique du locataire dont l’utilisateur ayant exécuté l’opération est membre.

    Exemple : *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID de l’objet utilisateur** : ID unique de l’utilisateur qui a exécuté l’opération.

    Exemple : *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Éléments détaillés d’une notification

- **Nom d’affichage** : (peut être vide) nom d’affichage plus détaillé de l’erreur.

    Exemple : *Paramètres de proxy d’application*

- **État**: état spécifique de la notification.

    Exemple : *Échec*

- **ID de l’objet** : (peut être vide) ID de l’objet sur lequel l’opération a été exécutée.

   Exemple : *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Détails** : description détaillée de ce qui s’est produit suite à l’opération.

    Exemple : *L’URL interne « <https://bing.com/> » n’est pas valide car elle est déjà en cours d’utilisation.*

- **Erreur de copie** : Vous permet de sélectionner l’**icône de copie** à droite de la zone de texte **Erreur de copie** pour copier les détails de notification à partager avec le support technique.

    Exemple :   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md)
