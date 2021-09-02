---
title: Redirection multimédia sur Azure Virtual Desktop - Azure
description: Comment utiliser la redirection multimédia pour Azure Virtual Desktop (préversion).
author: Heidilohr
ms.topic: how-to
ms.date: 08/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 574a10f6ef79ff3d40f5d62e49db9ebf198d2a79
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563778"
---
# <a name="multimedia-redirection-for-azure-virtual-desktop-preview"></a>Redirection multimédia pour Azure Virtual Desktop (préversion)

> [!IMPORTANT]
> La redirection multimédia pour Azure Virtual Desktop est actuellement disponible en préversion.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
>À l’heure actuelle, Azure Virtual Desktop ne prend pas en charge la redirection multimédia sur Azure Virtual Desktop pour Microsoft 365 Government (GCC), les environnements GCC-High et Microsoft 365 DoD.
>
>La redirection multimédia sur Azure Virtual Desktop est uniquement disponible pour le client Windows Desktop sur les machines Windows 10. La redirection multimédia nécessite le client Windows Desktop, version 1.2.2222 ou ultérieure.

La redirection multimédia (MMR) permet une lecture fluide des vidéos lorsque vous les visionnez dans votre navigateur Azure Virtual Desktop. La redirection multimédia déplace l’élément multimédia du navigateur vers l’ordinateur local pour un traitement et un rendu plus rapides. Microsoft Edge et Google Chrome prennent tous deux en charge la fonctionnalité de redirection multimédia. Toutefois, la préversion publique de la redirection multimédia pour Azure Virtual Desktop a limité la lecture sur YouTube. Pour tester YouTube dans le cadre du déploiement de votre organisation, vous devez [activer une extension](#managing-group-policies-for-the-multimedia-redirection-browser-extension).

## <a name="requirements"></a>Configuration requise

Avant d’utiliser la redirection multimédia sur Azure Virtual Desktop, vous devez effectuer les opérations suivantes :

1. [Installez le client Windows Desktop](./user-documentation/connect-windows-7-10.md#install-the-windows-desktop-client) sur un appareil Windows 10 ou Windows 10 IoT Enterprise qui répond à la [configuration matérielle requise pour Teams sur un PC Windows](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/). L’installation de la version 1.2.2222 ou ultérieure du client installe également le plug-in de redirection multimédia (MsMmrDVCPlugin.dll) sur l’appareil client. Pour en savoir plus sur les mises à jour et les nouvelles versions, consultez [Nouveautés du client Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew).

2. [Configurez l’ordinateur client pour le groupe Insider](create-host-pools-azure-marketplace.md).

3. Installez [le service de redirection multimédia](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) et toutes les extensions de navigateur requises sur la machine virtuelle.

4. Configurez l’ordinateur client pour permettre à vos utilisateurs d’accéder au programme Insiders. Pour configurer le client pour le groupe Insider, définissez les informations de Registre suivantes :

   - **Key**: HKLM\\Software\\Microsoft\\MSRDC\\Policies
   - **Type**: REG_SZ
   - **Name**: ReleaseRing
   - **Data**: insider

   Pour en savoir plus sur le programme Insiders, consultez [Client Windows Desktop pour les administrateurs](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin#configure-user-groups).

5. Utilisez [le programme d'installation MSI (MsMmrHostMri)](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) pour installer les extensions de redirection multimédia pour votre navigateur Internet sur votre machine virtuelle Azure. À l’heure actuelle, la redirection multimédia pour Azure Virtual Desktop ne prend en charge que Microsoft Edge et Google Chrome.

## <a name="managing-group-policies-for-the-multimedia-redirection-browser-extension"></a>Gestion des stratégies de groupe pour l’extension de navigateur de redirection multimédia

L’utilisation du programme d’installation MSI pour la redirection multimédia installe les extensions de navigateur. Toutefois, ce service étant toujours en préversion publique, l’expérience utilisateur peut varier. Pour plus d'informations sur les problèmes connus, consultez [Problèmes connus](#known-issues-and-limitations).

Dans certains cas, vous pouvez modifier la stratégie de groupe pour gérer les extensions de navigateur et améliorer l’expérience utilisateur. Par exemple :

- Vous pouvez installer l’extension sans interaction de l’utilisateur.
- Vous pouvez limiter les sites web qui utilisent la redirection multimédia.
- Vous pouvez épingler l’icône d’extension dans Google Chrome par défaut. L’icône d’extension est déjà épinglée par défaut dans Microsoft Edge. Vous ne devez donc modifier ce paramètre que dans Chrome.

### <a name="configure-microsoft-edge-group-policies-for-multimedia-redirection"></a>Configurer des stratégies de groupe Microsoft Edge pour la redirection multimédia

Pour configurer les stratégies de groupe, vous devez modifier le modèle d’administration Microsoft Edge. Pour afficher les options de configuration des extensions, accédez à **Extensions Microsoft Edge pour les modèles d’administration** > **Configurer les paramètres de gestion des extensions**.

Le code suivant est un exemple de stratégie de groupe Microsoft Edge qui commande au navigateur d’installer l’extension de redirection multimédia et n’autorise le chargement de la redirection multimédia que sur YouTube :

```cmd
{ "joeclbldhdmoijbaagobkhlpfjglcihd": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "update_url": "https://edge.microsoft.com/extensionwebstorebase/v1/crx" } }
```

Pour en savoir plus sur la configuration de la stratégie de groupe, consultez [Stratégie de groupe Microsoft Edge](/DeployEdge/configure-microsoft-edge).

### <a name="configure-google-chrome-group-policies-for-multimedia-redirection"></a>Configurer des stratégies de groupe Google Chrome pour la redirection multimédia

Pour configurer des stratégies de groupe Google Chrome, vous devez modifier le modèle d’administration Google Chrome. Pour afficher les options de configuration des extensions, accédez à **Modèles d’administration** > **Google** > **Extensions Google Chrome** > **Paramètres de gestion des extensions**.

L’exemple suivant est similaire à l’exemple de code dans [Configurer des stratégies de groupe Microsoft Edge pour la redirection multimédia](#configure-microsoft-edge-group-policies-for-multimedia-redirection). Cette stratégie force l’installation de l’extension de redirection multimédia avec l’icône épinglée dans le menu supérieur droit, et n’autorise le chargement de la redirection multimédia que sur YouTube.

```cmd
{ "lfmemoeeciijgkjkgbgikoonlkabmlno": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "toolbar_pin": "force_pinned", "update_url": "https://clients2.google.com/service/update2/crx" } }
```

Informations supplémentaires sur la configuration de la [stratégie de groupe Google Chrome](https://support.google.com/chrome/a/answer/187202#zippy=%2Cwindows).

## <a name="run-the-multimedia-redirection-extension-manually-on-a-browser"></a>Exécuter l’extension de redirection multimédia manuellement sur un navigateur

La MMR utilise des applications distantes et le bureau de session pour les navigateurs Microsoft Edge et Google Chrome. Une fois que vous avez respecté [la configuration requise](#requirements), ouvrez votre navigateur pris en charge. Si vous n’avez pas installé les navigateurs ou l’extension avec une stratégie de groupe, les utilisateurs devront exécuter manuellement l’extension. Cette section vous explique comment exécuter manuellement l’extension dans l’un des navigateurs actuellement pris en charge.

### <a name="microsoft-edge"></a>Microsoft Edge

Pour exécuter l’extension sur Microsoft Edge manuellement, recherchez le point d’exclamation jaune dans le menu de dépassement de capacité. Vous devez voir une invite pour activer l’extension de la redirection multimédia pour Azure Virtual Desktop. Sélectionnez **Activer l’extension**.

### <a name="google-chrome"></a>Google Chrome

Pour exécuter l’extension sur Google Chrome manuellement, recherchez le message de notification indiquant que la nouvelle extension a été installée, comme illustré dans la capture d’écran suivante. 

![Capture d’écran de la barre des tâches Google Chrome. Un onglet de notification indique « Nouvelle extension ajoutée ».](media/chrome-notification.png)

Sélectionnez la notification pour permettre à vos utilisateurs d’activer l’extension. Les utilisateurs doivent également épingler l’extension qui leur permettra de voir si la redirection multimédia est connectée.

### <a name="the-multimedia-redirection-status-icon"></a>Icône d’état de la redirection multimédia

Pour déterminer rapidement si la redirection multimédia est active dans votre navigateur, nous avons ajouté les états d’icône suivants :

| État de l’icône  | Définition  |
|-----------------|-----------------|
| [Icône du programme Azure Virtual Desktop par défaut sans état appliqué.](/media/icon-default.png) | Apparence de l’icône par défaut sans état appliqué. |
| [Icône du programme Azure Virtual Desktop avec un carré rouge et un x indiquant que la redirection multimédia ne fonctionne pas.](/media/icon-disconnect.png) | Le carré rouge avec un « X » à l’intérieur signifie que le client n’a pas pu se connecter à la redirection multimédia. |
| [Icône du programme Azure Virtual Desktop avec un carré vert et une coche à l’intérieur, indiquant que la redirection multimédia fonctionne.](/media/icon-connect.png) | Le carré vert avec une coche à l’intérieur signifie que le client a pu se connecter à la redirection multimédia. |

La sélection de l’icône affiche un menu contextuel contenant une case à cocher que vous pouvez sélectionner pour activer ou désactiver la redirection multimédia sur tous les sites web. Elle répertorie également les numéros de version pour chaque composant du service.

## <a name="send-feedback-during-public-preview"></a>Envoyer des commentaires pendant la préversion publique

Si vous rencontrez des problèmes, vous pouvez nous en informer dans le concentrateur de commentaires sur le client et l’hôte de la machine virtuelle.

Pour nous envoyer des commentaires :

1. Ouvrez le **concentrateur de commentaires** à la fois sur le client et le serveur.

2. Sélectionnez **Signaler un problème**.

3. Utilisez le même titre pour les deux signalements de problème, mais spécifiez l’emplacement à partir duquel vous envoyez le rapport en mentionnant « [Client] » ou « [Hôte] » au début.

    Par exemple, si vous signalez un problème à partir du client, indiquez-le comme suit :

    >[Client] Titre de votre rapport

    Si vous signalez un problème à partir de l’hôte, indiquez-le comme suit :

    >[Hôte] Titre de votre rapport

4. Dans le champ **Expliquer de manière plus détaillée**, décrivez le problème que vous rencontrez. Nous vous recommandons également d’inclure l’URL de la vidéo que vous regardiez lorsque le problème s’est produit.

5. Une fois terminé, sélectionnez **Suivant**.

6. Sélectionnez la bulle **Problème**, puis **Applications** et **Bureau à distance** dans les deux menus déroulants, comme illustré dans la capture d’écran suivante.

    ![Capture d'écran de la fenêtre « 2. Choisir une catégorie ». L’utilisateur a sélectionné la bulle Problème, puis Applications et Bureau à distance dans les menus déroulants en dessous.](media/problem-category.png)

7. Sélectionnez **Suivant**.

8. Vérifiez s’il existe dans la liste un problème similaire à celui que vous envisagez d’envoyer.
   
   - Si une bulle s’affiche pour établir un lien vers un bogue actif, assurez-vous que la description du bogue correspond au problème que vous signalez. Si c’est le cas, sélectionnez la bulle, puis **Lier au bogue**, comme illustré dans la capture d’écran ci-dessous.

    ![Capture d'écran de la fenêtre « 3. Recherche de commentaires similaires ». L’utilisateur a sélectionné la bulle pour l’option « Lien au bogue numéro 32350300 actif ».](media/link-to-bug.png)

    - Si vous ne trouvez pas de problème similaire, sélectionnez **Créer un bogue**.

    ![Capture d'écran de la fenêtre « 3. Recherche de commentaires similaires ». Cette fois, l’option « Lier au bogue » est absente, et l’utilisateur a sélectionné « Créer un bogue » à la place.](media/make-new-bug.png)

9. Sélectionnez **Suivant**.

10. Dans la fenêtre **Ajouter plus de détails**, sélectionnez **Inclure des données sur le Bureau à distance (par défaut)** , puis répondez à toutes les questions de manière aussi détaillée que possible.

    Si vous souhaitez ajouter un enregistrement vidéo du problème, sélectionnez **Inclure des données sur le Bureau à distance (par défaut)** et ensuite le bouton **Démarrer l’enregistrement**. Lors de l’enregistrement, ouvrez Bureau à distance et exécutez la procédure qui a donné lieu au problème. Lorsque vous avez terminé, retournez au navigateur, puis testez la vidéo pour vous assurer qu’elle est correctement enregistrée.

    Une fois que vous avez terminé, acceptez d’envoyer les diagnostics et fichiers joints à Microsoft, puis sélectionnez **Envoyer**.

### <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Les problèmes suivants sont ceux que nous connaissons déjà. Vous ne devez donc pas les signaler :

- La redirection multimédia fonctionne uniquement sur le client Windows Desktop, et non sur le client web.

- À l’heure actuelle, la redirection multimédia ne prend pas en charge le contenu protégé. Les vidéos de Pluralsight et Netflix ne fonctionnent donc pas.

- Pendant la préversion publique, la redirection multimédia est désactivée sur tous les sites à l’exception de YouTube. Toutefois, si vous avez l’extension, vous pouvez activer la redirection multimédia pour tous les sites web. Nous avons ajouté l’extension pour permettre aux organisations de tester la fonctionnalité sur leurs sites web.

- Il existe un faible risque que le programme d’installation MSI ne puisse pas installer l’extension pendant les tests internes. Si vous rencontrez ce problème, vous devez installer l’extension de redirection multimédia à partir du magasin Microsoft Edge ou du magasin Google Chrome.

    - [Extension de navigateur pour la redirection multimédia (Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
    - [Extension de navigateur pour la redirection multimédia (Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- L’installation de l’extension sur les ordinateurs hôtes à l’aide du programme d’installation MSI invite les utilisateurs à accepter l’extension à la première ouverture du navigateur ou affiche un avertissement ou un message d’erreur. Si les utilisateurs refusent cette invite, l’extension risque de ne pas se charger. Pour éviter ce problème, installez les extensions en [modifiant la stratégie de groupe](#managing-group-policies-for-the-multimedia-redirection-browser-extension).

- Lorsque vous redimensionnez la fenêtre de la vidéo, la taille de la fenêtre s’ajuste plus rapidement que la vidéo proprement dite. Ce problème survient également lors de la réduction et de l’agrandissement de la fenêtre.

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes intéressé par la diffusion vidéo en continu sur d’autres composants d’Azure Virtual Desktop, consultez [Teams pour Azure Virtual Desktop](teams-on-avd.md).
