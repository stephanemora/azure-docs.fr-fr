---
title: Guide de résolution des problèmes de l’Explorateur de stockage Azure | Microsoft Docs
description: Vue d’ensemble des techniques de débogage pour l’Explorateur de stockage Azure
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3e26365c4273611c81682a760695522575f3875d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225040"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guide de dépannage de l’Explorateur de stockage Azure

L’Explorateur Stockage Microsoft Azure est une application autonome qui vous permet d’utiliser facilement les données du Stockage Azure sur Windows, macOS et Linux. L’application peut se connecter aux comptes de stockage hébergés sur Azure, sur les clouds nationaux et sur Azure Stack.

Ce guide résume les solutions aux problèmes couramment rencontrés dans l’Explorateur de stockage.

## <a name="role-based-access-control-permission-issues"></a>Problèmes de contrôle d’autorisation d’accès en fonction du rôle

[Contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) fournit une gestion précise de l’accès de ressources Azure en combinant des jeux d’autorisations dans _rôles_. Voici quelques suggestions que vous pouvez suivre pour obtenir le RBAC fonctionne dans l’Explorateur de stockage.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Que dois-je voir mes ressources dans l’Explorateur de stockage ?

Si vous rencontrez des problèmes pour accéder aux ressources de stockage à l’aide de RBAC, il peut être, car vous n’avez pas reçu les rôles appropriés. Les sections suivantes décrivent les autorisations de que l’Explorateur de stockage nécessite actuellement pour accéder à vos ressources de stockage.

Contactez votre administrateur de compte Azure si vous ne savez pas qu'avoir les rôles appropriés ou les autorisations.

#### <a name="read-listget-storage-accounts"></a>Lecture : Répertorier/obtenir le ou les comptes de stockage

Vous devez être autorisé à répertorier les comptes de stockage. Vous pouvez obtenir cette autorisation par affectation du rôle « Lecteur ».

#### <a name="list-storage-account-keys"></a>Répertorier les clés du compte de stockage

Explorateur de stockage peut également utiliser des clés de compte pour authentifier les demandes. Vous pouvez accéder aux clés avec des rôles plus puissants, tels que le rôle « Contributeur ».

> [!NOTE]
> Clés d’accès accorder des autorisations illimitées à toute personne qui les contient. Par conséquent, il est généralement pas recommandé qu’ils être fournie aux utilisateurs de compte. Si vous devez révoquer les clés d’accès, vous pouvez les régénérer à partir de la [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Rôles de données

Vous devez avoir au moins un rôle qui accorde l’accès lire les données à partir de ressources. Par exemple, si vous avez besoin afficher ou télécharger des objets BLOB, vous devez au moins le rôle « Lecteur de données de Blob de stockage ».

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Pourquoi avez besoin d’un rôle de couche de gestion pour voir mes ressources dans l’Explorateur de stockage ?

Stockage Azure a deux couches d’accès : _gestion_ et _données_. Abonnements et comptes de stockage sont accessibles via la couche de gestion. Conteneurs, objets BLOB et autres ressources de données sont accessibles via la couche données. Par exemple, si vous souhaitez obtenir la liste de vos comptes de stockage Azure, vous devez envoyer une demande au point de terminaison de gestion. Si vous voulez une liste de conteneurs d’objets blob dans un compte, vous envoyez une requête au point de terminaison de service approprié.

Rôles RBAC peuvent contenir des autorisations d’accès de couche données ou de gestion. Le rôle « Lecteur », vous accorde par exemple, des ressources de gestion des accès en lecture seule à l’aide de la couche.

À proprement parler, le rôle « Lecteur » ne fournit aucune autorisation de la couche données et n’est pas nécessaire pour accéder à la couche données.

Explorateur de stockage vous permet de facilement accéder à vos ressources en rassemblant les informations nécessaires pour se connecter à vos ressources Azure pour vous. Par exemple, pour afficher vos conteneurs d’objets blob, Explorateur de stockage envoie une demande de conteneurs de liste pour le point de terminaison de service blob. Pour obtenir ce point de terminaison, l’Explorateur de stockage recherche dans la liste des abonnements et comptes de stockage que vous avez accès à. Toutefois, pour rechercher vos abonnements et les comptes de stockage, l’Explorateur de stockage doit également accéder à la couche de gestion.

Si vous n’avez pas un rôle accordant toute gestion des autorisations de couche, l’Explorateur de stockage ne peut pas obtenir les informations que nécessaires pour se connecter à la couche données.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Que se passe-t-il si je n’obtiens la gestion des autorisations de couche besoin à partir de mon administrateur ?

Nous n’avez pas encore une solution de RBAC pour l’instant. Pour résoudre ce problème, vous pouvez demander un URI SAS pour [attacher à votre ressource](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Error: certificat auto-signé dans la chaîne d’approbation (et erreurs similaires)

Les erreurs de certificat sont provoquées par une des deux situations suivantes :

1. L’application est connectée via un « proxy transparent » ; dans cette configuration, un serveur (par exemple, le serveur de votre société) intercepte le trafic HTTPS, le déchiffre, puis le chiffre à l’aide d’un certificat auto-signé.
2. Vous exécutez une application qui injecte un certificat SSL auto-signé dans les messages HTTPS que vous recevez. Les exemples d’applications qui injectent des certificats incluent un antivirus et un logiciel d’inspection de trafic réseau.

Lorsque l'Explorateur Stockage voit un certificat auto-signé ou non approuvé, il ne peut plus savoir si le message HTTPS reçu a été modifié. Si vous avez une copie du certificat auto-signé, vous pouvez ordonner à l’Explorateur de stockage de lui faire confiance en procédant comme suit :

1. Obtenez une copie Base-64 chiffrée X.509 (.cer) du certificat
2. Cliquez sur **Modifier** > **Certificats SSL** > **Importer les certificats**, puis utilisez le sélecteur de fichiers pour rechercher, sélectionner et ouvrir le fichier .cer

Ce problème peut également provenir de plusieurs certificats (racine et intermédiaires). Les deux certificats doivent être ajoutés pour résoudre l’erreur.

Si vous ne savez pas d'où provient le certificat, vous pouvez essayer de le savoir en procédant comme suit :

1. Installez Open SSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (n’importe quelle version légère devrait suffire)
    * Mac et Linux : doit être inclus dans votre système d’exploitation
2. Exécutez Open SSL.
    * Windows : ouvrez le répertoire d’installation, cliquez sur **/bin/**, puis double-cliquez sur **openssl.exe**.
    * Mac et Linux : exécutez **openssl** à partir d’un terminal.
3. Exécutez `s_client -showcerts -connect microsoft.com:443`
4. Recherchez les certificats auto-signés. Si vous ne savez pas quels certificats sont auto-signés, recherchez ceux dont le sujet `("s:")` et émetteur `("i:")` sont les mêmes.
5. Après avoir trouvé les certificats auto-signés pour chacun d’eux, copiez et collez tout depuis **---BEGIN CERTIFICATE---** jusqu’à **---END CERTIFICATE---** (les deux inclus) dans un nouveau fichier .cer.
6. Ouvrez l’Explorateur de stockage, cliquez sur **Modifier** > **Certificats SSL** > **Importer les certificats**, puis utilisez le sélecteur de fichiers pour rechercher, sélectionner et ouvrir les fichiers .cer que vous avez créés.

Si vous ne trouvez aucun certificat auto-signé à l’aide des étapes précédentes, contactez-nous par l’intermédiaire de l’outil de commentaires pour obtenir de l’aide. Vous pouvez choisir de lancer l’Explorateur de stockage à partir de la ligne de commande avec l’indicateur `--ignore-certificate-errors`. Une fois lancé avec cet indicateur, Explorateur de stockage ignore les erreurs de certificat.

## <a name="sign-in-issues"></a>Problèmes de connexion

### <a name="blank-sign-in-dialog"></a>Vide-boîte de dialogue Connexion

Vides connectez-vous boîtes de dialogue sont le plus souvent provoquées par ADFS demandant l’Explorateur de stockage pour effectuer une redirection, qui est pris en charge par Canon à. Pour contourner ce problème, vous pouvez essayer d'utiliser le flux de code de l'appareil dans le cadre de la connexion. Pour ce faire, suivez les étapes suivantes :

1. « Accédez à la version préliminaire » -> « Utiliser appareil Code connectez-vous ».
2. Ouvrez la boîte de dialogue de connexion (via l'icône de connexion de la barre verticale de gauche ou via l'option « Ajouter un compte » du panneau du compte).
3. Choisissez quel environnement que vous souhaitez vous connecter à.
4. Cliquez sur le bouton « Se connecter ».
5. Suivez les instructions figurant sur le panneau.

Si vous avez des problèmes de connexion dans le compte que vous souhaitez utiliser, car votre navigateur par défaut est déjà connecté à un autre compte, vous pouvez :

1. copier manuellement le lien et le code dans une session privée de votre navigateur, ou
2. copier manuellement le lien et le code dans un autre navigateur.

### <a name="reauthentication-loop-or-upn-change"></a>Boucle de réauthentification ou modification du nom UPN

Si vous êtes dans une boucle de réauthentification ou avez changé le nom UPN de l’un de vos comptes, essayez ce qui suit :

1. Supprimez tous les comptes, puis fermez l’Explorateur Stockage.
2. Supprimez le dossier .IdentityService de votre ordinateur. Sur Windows, le dossier se situe dans `C:\users\<username>\AppData\Local`. Pour Mac et Linux, vous pouvez trouver le dossier à la racine de votre répertoire utilisateur.
3. Si vous utilisez Mac ou Linux, vous devez également supprimer l’entrée Microsoft.Developer.IdentityService du magasin de clés de votre système d’exploitation. Sur Mac, le magasin de clés est l’application « Gnome Keychain ». Pour Linux, l’application est généralement appelée « Keyring », mais le nom peut être différent en fonction de votre distribution.

### <a name="conditional-access"></a>Accès conditionnel

L’accès conditionnel n’est pas pris en charge lorsque de l’Explorateur Stockage est utilisé sur Windows 10, Linux ou macOS. Ceci est dû à une limitation de la bibliothèque de AAD utilisée par l’Explorateur Stockage.

## <a name="mac-keychain-errors"></a>Erreurs de trousseau Mac

Il peut arriver que le trousseau macOS soit dans un état qui provoque des problèmes dans la bibliothèque d’authentification de l’Explorateur Stockage. Pour obtenir la chaîne de clé hors de cet état, essayez les étapes suivantes :

1. Fermez l’Explorateur Stockage.
2. Ouvrez le trousseau (**cmd + espace**, tapez keychain et appuyez sur Entrée).
3. Sélectionnez le trousseau « login ».
4. Cliquez sur l’icône de cadenas pour verrouiller le trousseau (le verrou s’anime alors en position fermée ; cela peut prendre quelques secondes, selon les applications ouvertes).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Lancez l’Explorateur Stockage.
6. Une fenêtre contextuelle doit s’afficher avec un message du type « Le hub de service souhaite accéder au trousseau ». Entrez alors le mot de passe de votre compte Administrateur Mac et cliquez sur **Toujours autoriser** (ou **Autoriser** si **Toujours autoriser** n’est pas proposé).
7. Essayez de vous connecter.

### <a name="general-sign-in-troubleshooting-steps"></a>Étapes générales de résolution des problèmes de connexion

* Si vous êtes sur macOS, et la fenêtre de connexion s’affiche jamais sur le « en attente pour l’authentification... » boîte de dialogue, puis recommencez [ces étapes](#mac-keychain-errors)
* Redémarrez l’Explorateur de stockage
* Si la fenêtre d’authentification est vide, patientez au moins une minute avant de fermer la boîte de dialogue d’authentification.
* Vérifiez que vos paramètres de proxy et de certificat sont correctement configurés pour votre ordinateur et pour l’Explorateur Stockage.
* Si vous êtes sur Windows et que vous avez accès à Visual Studio 2017 sur le même ordinateur et que vous vous connecter, essayez de vous connecter à Visual Studio 2017. Après une connexion réussie à Visual Studio 2017, vous devez être en mesure d’ouvrir l’Explorateur Stockage et de voir votre compte dans le panneau des comptes.

Si aucune de ces méthodes ne fonctionne, [ouvrez un problème sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Abonnements manquants et locataires en échec

Si vous ne parvenez pas à récupérer vos abonnements après vous être connecté avec succès, essayez l’une des méthodes de résolution de problèmes suivantes :

* Vérifiez que votre compte a accès aux abonnements attendus. Vous pouvez vérifier l’accès en vous connectant au portail de l’environnement Azure que vous essayez d’utiliser.
* Assurez-vous que vous vous êtes connecté à l'aide de l'environnement Azure approprié (Azure, Azure Chine 21Vianet, Azure Allemagne, Azure Gouvernement des États-Unis ou environnement personnalisé).
* Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.
* Essayez de supprimer et de rajouter le compte.
* S’il existe un lien « Plus d’informations », examinez les messages d’erreur signalés pour les locataires en échec. Si les messages d’erreur que vous voyez ne vous aident pas, n’hésitez pas à [ouvrir un problème sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Impossible de supprimer le compte ou la ressource de stockage joints

Si vous ne pouvez pas supprimer un compte joint ou une ressource de stockage joints via l’interface utilisateur, vous pouvez supprimer manuellement toutes les ressources jointes en supprimant les dossiers suivants :

* Windows : `%AppData%/StorageExplorer`
* MacOS : `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux : `~/.config/StorageExplorer`

> [!NOTE]
> Fermez l’Explorateur de stockage avant de supprimer les dossiers ci-dessus.

> [!NOTE]
> Si vous avez importé des certificats SSL, sauvegardez le contenu du répertoire `certs`. Vous pourrez utiliser la sauvegarde plus tard pour réimporter vos certificats SSL.

## <a name="proxy-issues"></a>Problèmes de proxy

Tout d’abord, vérifiez que les informations suivantes que vous avez entrées sont toutes correctes :

* URL de proxy et numéro de port
* Nom d’utilisateur et mot de passe si requis par le proxy

> [!NOTE]
> Explorateur de stockage ne prend pas en charge les fichiers de configuration automatique de proxy pour la configuration des paramètres de proxy.

### <a name="common-solutions"></a>Solutions courantes

Si vous rencontrez encore des problèmes, essayez les méthodes de résolution des problèmes suivantes :

* Si vous pouvez vous connecter à Internet sans utiliser votre proxy, vérifiez que l’Explorateur de stockage fonctionne sans les paramètres de proxy activés. Si c’est le cas, le dysfonctionnement est peut-être lié à vos paramètres de proxy. Contactez l’administrateur de votre proxy pour identifier les problèmes.
* Vérifiez que les autres applications utilisant le serveur proxy fonctionnent normalement.
* Vérifiez que vous pouvez vous connecter au portail de l’environnement Azure que vous essayez d’utiliser.
* Vérifiez que vous pouvez recevoir des réponses de vos points de terminaison de service. Entrez une des URL de point de terminaison dans votre navigateur. Si vous pouvez vous connecter, vous devez recevoir une réponse XML InvalidQueryParameterValue ou similaire.
* Si quelqu’un d’autre utilise également l’Explorateur de stockage avec votre serveur proxy, vérifiez que cette personne peut se connecter. Si elle le peut, vous devrez peut-être contacter l’administrateur de votre serveur proxy.

### <a name="tools-for-diagnosing-issues"></a>Outils pour diagnostiquer les problèmes

Si vous disposez d’outils de mise en réseau, tels que Fiddler pour Windows, vous pouvez peut-être diagnostiquer les problèmes comme suit :

* Si vous devez passer par votre serveur proxy, vous devez peut-être configurer votre outil de mise en réseau pour vous connecter via le proxy.
* Vérifiez le numéro de port utilisé par votre outil de mise en réseau.
* Entrez l’URL de l’hôte local et le numéro de port de l’outil de mise en réseau en tant que paramètres de proxy dans l’Explorateur de stockage. Si cette opération est effectuée correctement, l’outil de mise en réseau démarre la journalisation des demandes réseau effectuées par l’Explorateur de stockage sur les points de terminaison de service et de gestion. Par exemple, si vous entrez https://cawablobgrs.blob.core.windows.net/ pour votre point de terminaison d’objets blob dans un navigateur, vous recevez une réponse semblable à la suivante, qui suggère que la ressource existe, bien que vous ne puissiez pas y accéder.

![Exemple de code](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contacter l’administrateur du serveur proxy

Si vos paramètres de proxy sont corrects, vous devrez peut-être contacter l’administrateur de votre serveur proxy, puis :

* Vérifier que votre proxy ne bloque pas le trafic vers les points de terminaison de gestion ou de ressources Azure.
* Vérifier le protocole d’authentification utilisé par votre serveur proxy. L’Explorateur de stockage ne prend pas en charge les proxys NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Message d’erreur indiquant qu’il est impossible de récupérer les enfants

Si vous êtes connecté à Azure par le biais d’un proxy, vérifiez que vos paramètres de proxy sont corrects. Si vous obtenez l’accès à une ressource du propriétaire de l’abonnement ou le compte, vérifiez que vous avez lu ou répertorier les autorisations pour cette ressource.

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>La chaîne de connexion n’a pas de paramètres de configuration complets

Si vous voyez ce message d’erreur, il est possible que vous n’avez pas les autorisations nécessaires pour obtenir les clés de votre compte de stockage. Pour vérifier si c’est le cas, accédez au portail et recherchez votre compte de stockage. Vous pouvez le faire rapidement en cliquant sur le nœud pour votre compte de stockage et en cliquant sur « Ouvrir dans le portail ». Une fois que vous le faites, accédez au panneau Clés d’accès. Si vous ne disposez pas des autorisations pour afficher les clés, vous verrez une page avec le message « Vous n’avez pas accès ». Pour contourner ce problème, vous pouvez soit obtenir la clé de compte de quelqu'un d’autre et attacher avec le nom et la clé, ou vous pouvez demander à quelqu'un pour une SAP pour le compte de stockage et l’utiliser pour joindre le compte de stockage.

Si vous ne voyez pas les clés de compte, signaler un problème sur GitHub, donc nous pouvons vous aider à résoudre le problème.

## <a name="issues-with-sas-url"></a>Problèmes liés à l’URL SAP

Si vous vous connectez à un service à l’aide d’une URL SAP et que vous rencontrez cette erreur :

* Vérifiez que l’URL fournit les autorisations nécessaires pour lire ou répertorier les ressources.
* Vérifiez que l’URL n’a pas expiré.
* Si l’URL SAP est basée sur une stratégie d’accès, vérifiez que cette dernière n’a pas été révoquée.

Si vous avez attaché accidentellement une URL de SAP non valide et que vous n’arrivez pas à la détacher, suivez les étapes suivantes :

1. Lors de l’exécution de l’Explorateur de stockage, appuyez sur F12 pour ouvrir la fenêtre Outils de développement.
2. Cliquez sur l’onglet Application, puis cliquez sur Stockage local > file:// dans l’arborescence à gauche.
3. Recherchez la clé associée au type de service de l’URI SAS problématique. Par exemple, si l’URI SAS incorrect concerne un conteneur d’objets blob, recherchez la clé nommée `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. La valeur de la clé doit être un tableau JSON. Recherchez l’objet associé à l’URI incorrecte, et supprimez-le.
5. Appuyez sur Ctrl+R pour recharger l’Explorateur de stockage.

## <a name="linux-dependencies"></a>Dépendances Linux

En règle générale, les packages suivants sont requis pour exécuter l’Explorateur de stockage sur Linux :

* [Runtime .NET core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgnome-keyring-common` et `libgnome-keyring-dev`
* `libgconf-2-4`

Selon votre distribution, il peut y avoir différentes ou d’autres packages que vous devez installer.

Explorateur de stockage est officiellement pris en charge sur Ubuntu 18.04, 16.04 et 14.04. Étapes d’installation pour une nouvelle machine sont les suivantes :

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Télécharger l’Explorateur de stockage
2. Installer le Runtime .NET Core, vérifié la version plus récente est : [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) (si vous avez déjà installé une version plus récente, vous devrez peut-être corriger l’Explorateur de stockage, voir ci-dessous)
3. Exécutez `sudo apt-get install libgconf-2-4`
4. Exécutez `sudo apt install libgnome-keyring-common libgnome-keyring-dev`

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Télécharger l’Explorateur de stockage
2. Installer le Runtime .NET Core, vérifié la version plus récente est : [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) (si vous avez déjà installé une version plus récente, vous devrez peut-être corriger l’Explorateur de stockage, voir ci-dessous)
3. Exécutez `sudo apt install libgnome-keyring-dev`

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Télécharger l’Explorateur de stockage
2. Installer le Runtime .NET Core, vérifié la version plus récente est : [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) (si vous avez déjà installé une version plus récente, vous devrez peut-être corriger l’Explorateur de stockage, voir ci-dessous)
3. Exécutez `sudo apt install libgnome-keyring-dev`

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Mise à jour corrective de l’Explorateur de stockage pour les versions plus récentes de .NET Core 
Si vous avez une version de .NET Core supérieures à 2.0 installé et exécutez l’Explorateur de stockage version 1.7.0 ou une version antérieure, vous devrez très probablement patch Explorateur de stockage en procédant comme suit :
1. Télécharger la version 1.5.43 de StreamJsonRpc [à partir de nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Recherchez le lien « Télécharger le package » sur le côté droit de la page.
2. Après avoir téléchargé le package, remplacez son extension de fichier `.nupkg` à `.zip`
3. Décompressez le package
4. Accédez à `streamjsonrpc.1.5.43/lib/netstandard1.1/`
5. Copie `StreamJsonRpc.dll` aux emplacements suivants dans le dossier de l’Explorateur de stockage :
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Portail de l’ouvrir dans l’Explorateur à partir d’Azure ne fonctionne pas

Si le bouton « Ouvrir dans l’Explorateur » sur le portail Azure ne fonctionne pas pour vous, assurez-vous que vous utilisez un navigateur compatible. Les navigateurs suivants ont été testés pour la compatibilité.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Étapes suivantes

Si aucune des solutions ne vous convient, [ouvrez un ticket d’incident sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Vous pouvez aussi accéder rapidement à GitHub à l’aide du bouton « Faire part d’un problème à GitHub » en bas à gauche de l’écran.

![Commentaires](./media/storage-explorer-troubleshooting/feedback-button.PNG)
