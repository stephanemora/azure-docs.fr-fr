---
title: Guide de résolution des problèmes de l’Explorateur de stockage Azure | Microsoft Docs
description: Vue d’ensemble des techniques de débogage pour l’Explorateur Stockage Azure
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dbd4e9c6e8a58738ac0a8db6c64133301d1aebe5
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107950583"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guide de résolution des problèmes de l’Explorateur de stockage Azure

L’Explorateur Stockage Microsoft Azure est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux. L’application peut se connecter aux comptes de stockage hébergés sur Azure, les clouds nationaux et Azure Stack.

Ce guide résume les solutions aux problèmes couramment rencontrés dans l’Explorateur Stockage.

## <a name="azure-rbac-permissions-issues"></a>Problèmes d’autorisations Azure RBAC

Le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) permet une gestion d’accès très précise pour les ressources Azure en combinant des jeux d’autorisations dans des _rôles_. Voici quelques stratégies permettant d’optimiser le fonctionnement d’Azure RBAC dans Explorateur Stockage.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Comment accéder à mes ressources dans l’Explorateur Stockage ?

Si vous avez des difficultés à accéder aux ressources de stockage via Azure RBAC, c’est que les rôles qui vous ont été attribués ne sont peut-être pas les bons. Les sections suivantes décrivent les autorisations que l’Explorateur Stockage vous demande actuellement pour pouvoir accéder à vos ressources de stockage. Contactez l’administrateur de votre compte Azure si vous n’êtes pas sûr de disposer des autorisations ou des rôles appropriés.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problème d’autorisations « Lecture : Répertorier/obtenir le ou les comptes de stockage »

Vous devez être autorisé à lister les comptes de stockage. Pour obtenir cette autorisation, le rôle _Lecteur_ doit vous être attribué.

#### <a name="list-storage-account-keys"></a>Répertorier les clés de compte de stockage

L’Explorateur Stockage peut également utiliser des clés de compte pour authentifier les requêtes. Vous pouvez accéder aux clés de compte par le biais de rôles plus puissants, comme le rôle _Contributeur_.

> [!NOTE]
> Les clés d’accès accordent des autorisations illimitées à toute personne qui les détient. Par conséquent, nous vous déconseillons de transmettre ces clés aux utilisateurs du compte. Si vous devez révoquer des clés d’accès, vous pouvez les regénérer à partir du [portail Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Rôles de données

Vous devez disposer d’au moins un rôle qui accorde l’accès en lecture aux données à partir des ressources. Par exemple, si vous voulez lister ou télécharger des objets blob, vous devez au moins détenir le rôle _Lecteur des données Blob du stockage_.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Pourquoi ai-je besoin d’un rôle d’accès à la couche de gestion pour voir mes ressources dans l’Explorateur Stockage ?

Le stockage Azure présente deux couches d’accès : _gestion_ et _données_. Les abonnements et les comptes de stockage sont accessibles via la couche de gestion. Les conteneurs, objets blob et autres ressources de données sont accessibles via la couche de données. Par exemple, si vous souhaitez obtenir la liste de vos comptes de stockage depuis Azure, vous devez envoyer une requête au point de terminaison de gestion. Si vous voulez consulter la liste des conteneurs d’objets blob dans un compte, vous envoyez une requête au point de terminaison du service approprié.

Les rôles Azure peuvent vous octroyer des autorisations d’accès à ces couches de données ou de gestion. Le rôle Lecteur vous accorde, par exemple, un accès en lecture seule aux ressources de la couche de gestion.

À proprement parler, le rôle Lecteur ne fournit aucune autorisation d’accès à la couche de données, et il n’est pas nécessaire pour accéder à la couche de données.

L’Explorateur Stockage vous permet d’accéder facilement à vos ressources en rassemblant les informations nécessaires pour vous connecter à vos ressources Azure. Par exemple, pour afficher vos conteneurs d’objets blob, l’Explorateur Stockage envoie une demande « lister les conteneurs » au point de terminaison du service blob. Pour obtenir ce point de terminaison, l’Explorateur Stockage recherche la liste des abonnements et comptes de stockage auxquels vous avez accès. Pour rechercher vos abonnements et comptes de stockage, l’Explorateur Stockage a aussi besoin d’un accès à la couche de gestion.

Si vous ne disposez d’un rôle qui accorde une autorisation d’accès à la couche de gestion, l’Explorateur Stockage ne peut pas obtenir les informations dont il a besoin pour se connecter à la couche de données.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Que se passe-t-il si je n’obtiens pas les autorisations d’accès à la couche de gestion que mon administrateur doit me fournir ?

Si vous souhaitez accéder à des conteneurs d’objets blob, à des conteneurs ou répertoires ADLS Gen2, ou à des files d’attente, vous pouvez les attacher à ces ressources à l’aide de vos informations d’identification Azure.

1. Ouvrez la boîte de dialogue de connexion.
1. Sélectionnez le type de ressource auquel vous voulez vous connecter.
1. Sélectionnez **Se connecter à l’aide d’Azure Active Directory (Azure AD)** . Sélectionnez **Suivant**.
1. Sélectionnez le compte d’utilisateur et le locataire associés à la ressource à laquelle vous effectuez l’attachement. Sélectionnez **Suivant**.
1. Entrez l’URL de la ressource, puis entrez un nom d’affichage unique pour la connexion. Sélectionnez **Suivant**, puis **Se connecter**.

Pour les autres types de ressource, nous n’avons actuellement pas de solution liée à Azure RBAC. En guise de solution de contournement, vous pouvez demander une URL SAS, puis l’attacher à votre ressource en effectuant les étapes suivantes :

1. Ouvrez la boîte de dialogue de connexion.
1. Sélectionnez le type de ressource auquel vous voulez vous connecter.
1. Sélectionnez **Signature d’accès partagé (SAS)** . Sélectionnez **Suivant**.
1. Entrez l’URL SAS que vous avez reçue, et entrez un nom d’affichage unique pour la connexion. Sélectionnez **Suivant**, puis **Se connecter**.
 
Pour plus d’informations sur l’attachement à des ressources, consultez [Attacher à une ressource individuelle](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Rôles intégrés Azure recommandés

Il existe plusieurs rôles Azure intégrés qui peuvent fournir les autorisations nécessaires pour utiliser l’Explorateur Stockage. Certains de ces rôles sont les suivants :
- [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) : Gérez tout, notamment l’accès aux ressources.
- [Collaborateur](../../role-based-access-control/built-in-roles.md#contributor) : Gérez tout, à l’exception de l’accès aux ressources.
- [Lecteur](../../role-based-access-control/built-in-roles.md#reader) : Lisez et listez les ressources.
- [Contributeur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor) : Gérez en totalité les comptes de stockage.
- [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) : Ayez un accès total aux données et conteneurs d’objets blob du Stockage Azure.
- [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : Lire, écrire et supprimer des conteneurs et objets blob du stockage Azure.
- [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) : Lire et répertorier des conteneurs et objets blob du stockage Azure.

> [!NOTE]
> Les rôles Propriétaire, Contributeur et Contributeur de compte de stockage donnent accès à la clé de compte.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erreur : Certificat auto-signé dans la chaîne de certificats (et erreurs similaires)

Les erreurs de certificat se produisent généralement dans l’une des situations suivantes :

- L’application est connectée via un _proxy transparent_, ce qui signifie qu’un serveur (par exemple, le serveur de votre société) intercepte le trafic HTTPS, le déchiffre, puis le chiffre à l’aide d’un certificat auto-signé.
- Vous exécutez une application qui injecte un certificat TLS/SSL auto-signé dans les messages HTTPS que vous recevez. Les antivirus et les logiciels d’inspection du trafic réseau sont des exemples de ce type d’application.

Quand l’Explorateur Stockage voit un certificat auto-signé ou non approuvé, il ne sait plus si le message HTTPS reçu a été modifié. Si vous avez une copie du certificat auto-signé, vous pouvez donner instruction à l’Explorateur Stockage de lui faire confiance en effectuant les étapes suivantes :

1. Obtenez une copie X.509 encodée en base 64 du certificat (.cer).
2. Accédez à **Modifier** > **Certificats SSL** > **Importer les certificats**, puis utilisez le sélecteur de fichiers pour rechercher, sélectionner et ouvrir le fichier .cer.

Ce problème peut aussi se produire s’il existe plusieurs certificats (racine et intermédiaire). Pour corriger cette erreur, les deux certificats doivent être ajoutés.

Si vous ne savez pas d’où provient le certificat, déterminez-le en suivant ces étapes :

1. Installez OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) : n’importe quelle version légère devrait suffire.
    * Mac et Linux : normalement fourni avec votre système d’exploitation.
2. Exécutez OpenSSL.
    * Windows : ouvrez le répertoire d’installation, sélectionnez **/bin/** , puis double-cliquez sur **openssl.exe**.
    * Mac et Linux : exécutez `openssl` à partir d’un terminal.
3. Exécutez `s_client -showcerts -connect microsoft.com:443`.
4. Recherchez les certificats auto-signés. Si vous ne parvenez pas à identifier les certificats auto-signés avec certitude, notez les endroits où le sujet `("s:")` et l’émetteur `("i:")` sont identiques.
5. Chaque fois que vous trouvez un certificat auto-signé, copiez et collez tout ce qui compris entre `-----BEGIN CERTIFICATE-----` et `-----END CERTIFICATE-----` dans un nouveau fichier .cer.
6. Ouvrez l’Explorateur Stockage, puis accédez à **Modifier** > **Certificats SSL** > **Importer des certificats**. Servez-vous ensuite du sélecteur de fichiers pour rechercher, sélectionner et ouvrir les fichiers .cer que vous avez créés.

Si vous ne trouvez aucun certificat auto-signé en suivant ces étapes, contactez-nous via l’outil de commentaires. Vous pouvez aussi ouvrir l’Explorateur Stockage à partir de la ligne de commande en utilisant l’indicateur `--ignore-certificate-errors`. Quand il est ouvert avec cet indicateur, l’Explorateur Stockage ignore les erreurs de certificat.

## <a name="sign-in-issues"></a>Problèmes de connexion

### <a name="understanding-sign-in"></a>Comprendre la connexion

Vérifiez que vous avez lu la documentation [Se connecter à l’Explorateur Stockage](./storage-explorer-sign-in.md).

### <a name="frequently-having-to-reenter-credentials"></a>Il arrive souvent de devoir entrer de nouveau les informations d’identification

Le fait de devoir entrer de nouveau les informations d’identification est probablement le résultat des stratégies d’accès conditionnel définies par votre administrateur AAD. Lorsque l’Explorateur Stockage vous demande de saisir à nouveau les informations d’identification à partir du panneau de compte, vous devriez voir un lien **Détails de l’erreur...** . Cliquez dessus pour voir pourquoi l’Explorateur Stockage vous demande de saisir à nouveau les informations d’identification. Les erreurs de stratégie d’accès conditionnel qui nécessitent une réentrée des informations d’identification peuvent ressembler à ceci :
- Le jeton d’actualisation a expiré...
- Vous devez utiliser l’authentification multifacteur pour accéder à...
- En raison d’un changement de configuration effectué par l’administrateur...

Pour réduire la fréquence de saisie des informations d’identification en raison d’erreurs telles que celles mentionnées ci-dessus, vous devez contacter votre administrateur AAD.

### <a name="conditional-access-policies"></a>Stratégies d'accès conditionnel

Si vous avez des stratégies d’accès conditionnel qui doivent être satisfaites pour votre compte, assurez-vous que vous utilisez la valeur de **navigateur web par défaut** pour le paramètre **Se connecter avec**. Pour plus d’informations sur ce paramètre, consultez [Modification de l’emplacement de connexion](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="browser-complains-about-http-redirect-during-sign-in"></a>Le navigateur se plaint d’une redirection HTTP pendant la connexion

Lorsque Explorateur Stockage effectue la connexion dans votre navigateur web, une redirection vers `localhost` est effectuée à la fin du processus de connexion. Les navigateurs affichent parfois un avertissement ou une erreur indiquant que la redirection est effectuée via HTTP au lieu de HTTPS. Certains navigateurs peuvent également essayer de forcer la redirection à s’effectuer via HTTPS. Si l’un ou l’autre de ces cas se produit, vous disposez de plusieurs options en fonction de votre navigateur :
- Ignorez l’avertissement.
- Ajoutez une exception pour `localhost`.
- Désactivez la redirection forcée via HTTPS, globalement ou uniquement pour `localhost`.

Si vous n’êtes pas en mesure d’utiliser l’une de ces options, vous pouvez également [modifier l’emplacement de la connexion](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Impossible d’acquérir le jeton, le locataire est filtré

Si vous voyez un message d’erreur indiquant qu’un jeton ne peut pas être acquis parce qu’un locataire est filtré, cela signifie que vous essayez d’accéder à une ressource qui se trouve dans un locataire que vous avez exclu. Pour annuler le filtrage du locataire, accédez au **panneau Compte** et assurez-vous que la case à cocher correspondant au locataire spécifié dans l’erreur est cochée. Reportez-vous à [Gestion des comptes](./storage-explorer-sign-in.md#managing-accounts) pour plus d’informations sur le filtrage des locataires dans l’Explorateur Stockage.

### <a name="authentication-library-failed-to-start-properly"></a>La bibliothèque d’authentification n’a pas pu démarrer correctement

Si au démarrage vous voyez un message d’erreur indiquant que la bibliothèque d’authentification de l’Explorateur Stockage n’a pas pu démarrer correctement, vérifiez que votre environnement d’installation remplit toutes les [conditions préalables](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites). Le fait de ne pas répondre aux conditions préalables est la cause la plus probable de ce message d’erreur.

Si vous pensez que votre environnement d’installation remplit toutes les conditions préalables, [ouvrez un problème sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new). Lorsque vous ouvrez votre problème, veillez à inclure les éléments suivants :
- Votre système d’exploitation.
- La version de l’Explorateur Stockage que vous essayez d’utiliser.
- Si vous avez vérifié les conditions préalables.
- [Journaux d’authentification](#authentication-logs) d’un lancement infructueux de l’Explorateur Stockage. La journalisation de l’authentification détaillée est automatiquement activée après ce type d’erreur.

### <a name="blank-window-when-using-integrated-sign-in"></a>Fenêtre vide lors de l’utilisation de la connexion intégrée

Si vous avez choisi d’utiliser la **connexion intégrée** et que vous voyez une fenêtre de signe vide, vous devrez probablement basculer vers une autre méthode de connexion. Les boîtes de dialogue de connexion vides apparaissent le plus souvent quand un serveur ADFS (Active Directory Federation Services) demande à l’Explorateur Stockage d’effectuer une redirection, ce qui n’est pas pris en charge par Electron.

Pour passer à une autre méthode de connexion en modifiant le paramètre **Se connecter avec** sous **Paramètres** > **Application** > **Connexion**. Pour plus d’informations sur les différents types de méthodes de connexion, consultez [Modification de l’emplacement de connexion](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="reauthentication-loop-or-upn-change"></a>Boucle de réauthentification ou modification du nom UPN

Si vous êtes dans une boucle de réauthentification ou que vous avez changé le nom UPN de l’un de vos comptes, essayez ces étapes :

1. Ouvrir l’explorateur de stockage
2. Accédez à Aide > Réinitialiser
3. Assurez-vous qu’au moins Authentification est coché. Vous pouvez désélectionner les autres éléments que vous ne souhaitez pas réinitialiser.
4. Cliquez sur le bouton Réinitialiser
5. Redémarrez l’Explorateur Stockage, puis réessayez de vous connecter.

Si vous continuez à rencontrer des problèmes après avoir effectué une réinitialisation, essayez les étapes suivantes :

1. Ouvrir l’explorateur de stockage
2. Supprimez tous les comptes, puis fermez l’Explorateur Stockage.
3. Supprimez le dossier `.IdentityService` de votre machine. Sur Windows, le dossier se situe dans `C:\users\<username>\AppData\Local`. Pour Mac et Linux, vous pouvez trouver le dossier à la racine de votre répertoire utilisateur.
4. Si vous exécutez Mac ou Linux, vous devez aussi supprimer l’entrée Microsoft.Developer.IdentityService du magasin de clés de votre système d’exploitation. Sur Mac, le magasin de clés est l’application *Gnome Keychain*. Dans Linux, l’application est généralement appelée _Keyring_, mais le nom peut être différent selon votre distribution.
6. Redémarrez l’Explorateur Stockage, puis réessayez de vous connecter.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS : erreurs de trousseau ou aucune fenêtre de connexion

Il peut arriver que le trousseau macOS entre dans un état qui s’avère problématique pour la bibliothèque d’authentification de l’Explorateur Stockage. Pour sortir le trousseau de cet état, suivez ces étapes :

1. Fermez l’Explorateur Stockage.
2. Ouvrez le trousseau (appuyez sur Commande + Barre d’espace, tapez **trousseau**, puis appuyez sur Entrée).
3. Sélectionnez le trousseau « login ».
4. Sélectionnez l’icône de cadenas pour verrouiller le trousseau. (À la fin du processus, le cadenas est fermé. Cette opération peut prendre quelques secondes, selon les applications qui sont ouvertes).

    ![Icône de cadenas](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Ouvrez l’Explorateur de stockage.
6. Vous obtenez un message tel que « Le hub de service souhaite accéder au trousseau ». Entrez le mot de passe de votre compte Administrateur Mac et sélectionnez **Toujours autoriser** (ou **Autoriser** si **Toujours autoriser** n’est pas proposé).
7. Essayez de vous connecter.

### <a name="default-browser-doesnt-open"></a>Le navigateur par défaut ne s’ouvre pas

Si votre navigateur par défaut ne s’ouvre pas quand vous essayez de vous connecter, essayez toutes les techniques suivantes :
- Redémarrez l’Explorateur de stockage
- Ouvrir votre navigateur manuellement avant de commencer la connexion
- Essayez d’utiliser la **connexion intégrée**. Pour obtenir des instructions sur la façon de procéder, consultez [Modification de l’emplacement de connexion](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="other-sign-in-issues"></a>Autres problèmes de connexion

Si aucun des éléments ci-dessus ne s’applique à votre problème de connexion ou s’il ne parvient pas à résoudre votre problème de connexion, [ouvrez un ticket sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Abonnements manquants et locataires en échec

Si vous ne parvenez pas à récupérer vos abonnements après vous être connecté, essayez l’une des méthodes de dépannage suivantes :

* Vérifiez que votre compte a accès aux abonnements attendus. Vous pouvez vérifier votre accès en vous connectant au portail de l’environnement Azure que vous essayez d’utiliser.
* Vérifiez que vous êtes connecté via l’environnement Azure approprié (Azure, Azure Chine 21Vianet, Azure Allemagne, Azure US Government ou Environnement personnalisé).
* Si vous vous trouvez derrière un serveur proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur Stockage.
* Essayez de supprimer et de rajouter le compte.
* S’il existe un lien « Plus d’informations » ou « Détails de l’erreur », examinez les messages d’erreur signalés pour les locataires en échec. Si vous ne savez pas comment répondre aux messages d’erreur, n’hésitez pas à [signaler un problème dans GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Impossible de supprimer un compte ou une ressource de stockage attachés

Si vous ne pouvez pas supprimer un compte ou une ressource de stockage attachés via l’interface utilisateur, vous pouvez supprimer manuellement toutes les ressources attachées en supprimant les dossiers suivants :

* Windows : `%AppData%/StorageExplorer`
* MacOS : `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux : `~/.config/StorageExplorer`

> [!NOTE]
> Fermez l’Explorateur Stockage avant de supprimer ces dossiers.

> [!NOTE]
> Si vous avez déjà importé des certificats SSL, sauvegardez le contenu du répertoire `certs`. Vous pourrez utiliser la sauvegarde plus tard pour réimporter vos certificats SSL.

## <a name="proxy-issues"></a>Problèmes de proxy

L’Explorateur Stockage prend en charge la connexion aux ressources de Stockage Azure via un serveur proxy. Si vous rencontrez des problèmes de connexion à Azure par proxy, voici quelques suggestions.

> [!NOTE]
> L’Explorateur Stockage ne gère que l’authentification de base avec les serveurs proxy. Les autres méthodes d’authentification (par exemple NTLM) ne sont pas prises en charge.

> [!NOTE]
> L’Explorateur Stockage ne prend pas en charge les fichiers de configuration automatique du proxy pour la configuration des paramètres de proxy.

### <a name="verify-storage-explorer-proxy-settings"></a>Vérification des paramètres de proxy de l’Explorateur Stockage

Le paramètre **Application → Proxy → Configuration du proxy** détermine la source dont l’Explorateur Stockage tire la configuration du proxy.

Si vous sélectionnez « Utiliser des variables d’environnement », veillez à définir la variable d’environnement `HTTPS_PROXY` ou `HTTP_PROXY` (les variables d’environnement étant sensibles à la casse, veillez à définir les bonnes variables). Si ces variables ne sont pas définies ou ne sont pas valides, l’Explorateur Stockage n’utilisera pas de proxy. Redémarrez l’Explorateur Stockage après avoir modifié les variables d’environnement.

Si vous sélectionnez « Utiliser les paramètres du proxy d’application », vérifiez que les paramètres de proxy dans l’application sont corrects.

### <a name="steps-for-diagnosing-issues"></a>Procédure de diagnostic des problèmes

Si vous rencontrez encore des problèmes, essayez les méthodes de résolution des problèmes suivantes :

1. Si vous pouvez vous connecter à Internet sans utiliser votre proxy, vérifiez que l’Explorateur Stockage fonctionne sans les paramètres de proxy activés. Si l’Explorateur Stockage parvient à se connecter, il se peut qu’il y ait un problème avec votre serveur proxy. Contactez votre administrateur pour identifier les problèmes.
2. Vérifiez que les autres applications qui utilisent le serveur proxy fonctionnent normalement.
3. Vérifiez que vous pouvez vous connecter au portail correspondant à l’environnement Azure que vous essayez d’utiliser.
4. Vérifiez que vous pouvez recevoir des réponses de vos points de terminaison de service. Entrez une des URL de point de terminaison dans votre navigateur. Si vous parvenez à vous connecter, vous recevez normalement une réponse XML du type `InvalidQueryParameterValue`.
5. Vérifiez si une autre personne qui utilise l’Explorateur Stockage avec le même serveur proxy peut se connecter. Si c’est le cas, vous devrez peut-être contacter l’administrateur de votre serveur proxy.

### <a name="tools-for-diagnosing-issues"></a>Outils pour diagnostiquer les problèmes

Un outil de mise en réseau comme Fiddler peut vous aider à diagnostiquer les problèmes.

1. Configurez votre outil de mise en réseau comme serveur proxy s’exécutant sur l’hôte local. Si vous devez continuer de travailler derrière un proxy réel, il vous faudra peut-être configurer votre outil de mise en réseau pour vous connecter via le proxy.
2. Vérifiez le numéro de port utilisé par votre outil de mise en réseau.
3. Configurez le proxy de l’Explorateur Stockage de façon à utiliser l’hôte local et le numéro de port de l’outil de mise en réseau (par exemple « localhost:8888 »).
 
Si ces paramètres sont définis correctement, l’outil de mise en réseau consignera les demandes réseau effectuées par l’Explorateur Stockage sur les points de terminaison de gestion et de service.
 
Si votre outil de mise en réseau ne semble pas consigner le trafic de l’Explorateur Stockage, essayez de le tester avec une autre application. Par exemple, entrez l’URL du point de terminaison de l’une de vos ressources de stockage (par exemple `https://contoso.blob.core.windows.net/`) dans un navigateur web. Vous recevrez une réponse de ce type :

  ![Exemple de code](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  La réponse indique que la ressource existe, même si vous ne pouvez pas y accéder.

Si votre outil de mise en réseau n’affiche que le trafic provenant d’autres applications, vous devrez peut-être ajuster les paramètres du proxy dans l’Explorateur Stockage. Sinon, il vous faudra potentiellement ajuster les paramètres de votre outil.

### <a name="contact-proxy-server-admin"></a>Contacter l’administrateur du serveur proxy

Si les paramètres de votre proxy sont corrects, vous devrez peut-être contacter l’administrateur de votre serveur proxy pour effectuer les vérifications suivantes :

* Vérifier que votre proxy ne bloque pas le trafic vers les points de terminaison de gestion ou de ressources Azure.
* Vérifier le protocole d’authentification utilisé par votre serveur proxy. L’Explorateur Stockage ne prend en charge que les protocoles d’authentification de base. Il ne gère pas les proxys NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Message d’erreur indiquant qu’il est impossible de récupérer les enfants

Si vous êtes connecté à Azure par le biais d’un proxy, vérifiez que vos paramètres de proxy sont corrects.

Si le propriétaire d’un abonnement ou d’un compte vous a donné accès à une ressource, vérifiez que vous disposez des autorisations d’accès en lecture et de liste la concernant.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>La chaîne de connexion n’a pas de paramètres de configuration complets

Si vous obtenez ce message d’erreur, il est possible que vous n’avez pas les autorisations nécessaires pour obtenir les clés de votre compte de stockage. Pour vérifier que c’est le cas, accédez au portail et recherchez votre compte de stockage. Pour cela, vous pouvez cliquer avec le bouton droit sur le nœud de votre compte de stockage et sélectionner **Ouvrir dans le portail**. Accédez ensuite au panneau **Clé d’accès**. Si vous n’avez pas les autorisations permettant d’afficher les clés, vous obtenez le message : « Vous n’avez pas accès ». Pour contourner ce problème, vous pouvez soit obtenir la clé du compte auprès de quelqu’un d’autre et attacher le nom et la clé, soit demander à quelqu’un une signature d’accès partagé pour le compte de stockage et l’utiliser pour attacher le compte de stockage.

Si vous ne voyez pas les clés du compte, signalez un problème dans GitHub pour nous permettre de vous aider à le résoudre.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Une erreur s’est produite pendant l’ajout d’une nouvelle connexion : Type d’erreur : Impossible de lire la propriété « version » non définie

Si vous obtenez ce message d’erreur pendant que vous essayez d’ajouter une connexion personnalisée, il se peut que les données de connexion stockées dans le gestionnaire d’informations d’identification local sont endommagées. Pour contourner ce problème, essayez de supprimer vos connexions locales endommagées, puis rajoutez-les :

1. Démarrez l’Explorateur Stockage. Dans le menu, accédez à **Aide** > **Activer/désactiver les outils de développement**.
2. Dans la fenêtre ouverte, sous l’onglet **Application**, accédez à **Stockage local** (côté gauche) > **file://** .
3. Selon le type de la connexion avec laquelle vous rencontrez un problème, recherchez sa clé, puis copiez sa valeur dans un éditeur de texte. La valeur est un tableau constitué de vos noms de connexion personnalisés qui se présente comme suit :
    * Comptes de stockage
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Conteneurs d’objets blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Partages de fichiers
        * `StorageExplorer_CustomConnections_Files_v1`
    * Files d’attente
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tables
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Une fois que vous avez enregistré vos noms de connexion actuels, définissez la valeur dans Outils de développement sur `[]`.

Si vous souhaitez conserver les connexions non endommagées, vous pouvez suivre les étapes ci-dessous pour localiser les connexions endommagées. Si vous ne voyez pas d’inconvénient à perdre toutes les connexions existantes, vous pouvez ignorer ces étapes et suivre les instructions propres à la plateforme pour effacer vos données de connexion.

1. À partir d’un éditeur de texte, ajoutez à nouveau chaque nom de connexion à Outils de développement, puis vérifiez si la connexion fonctionne toujours.
2. Si une connexion fonctionne correctement, c’est qu’elle n’est pas endommagée et vous pouvez l’y laisser sans risque. Si une connexion ne fonctionne pas, supprimez sa valeur de Outils de développement, puis enregistrez-la de façon à la rajouter ultérieurement.
3. Répétez l’opération jusqu’à ce que vous ayez examiné toutes vos connexions.

Une fois que vous les avez toutes passées en revue, pour tous les noms de connexion qui ne sont pas rajoutés, vous devez effacer leurs données endommagées (le cas échéant) et les rajouter en suivant les étapes normales dans l’Explorateur Stockage :

### <a name="windows"></a>[Windows](#tab/Windows)

1. Dans le menu **Démarrer**, recherchez le **Gestionnaire d’informations d’identification** et ouvrez-le.
2. Accédez à **Informations d’identification Windows**.
3. Sous **Informations d’identification génériques**, recherchez les entrées qui comportent la clé `<connection_type_key>/<corrupted_connection_name>` (par exemple, `StorageExplorer_CustomConnections_Accounts_v1/account1`).
4. Supprimez ces entrées et rajoutez les connexions.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Ouvrez Spotlight (Commande + Barre d’espace) et recherchez **Trousseau d’accès**.
2. Recherchez les entrées qui comportent la clé `<connection_type_key>/<corrupted_connection_name>` (par exemple, `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Supprimez ces entrées et rajoutez les connexions.

### <a name="linux"></a>[Linux](#tab/Linux)

La gestion des informations d’identification locales varie en fonction de la distribution Linux. Si votre distribution Linux n’intègre pas d’outil de gestion des informations d’identification locales à interface graphique utilisateur (GUI), vous pouvez installer un outil tiers pour gérer vos informations d’identification locales. Par exemple, vous pouvez utiliser [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), outil GUI open source de gestion des informations d’identification locales Linux.

1. Ouvrez votre outil de gestion des informations d’identification locales et recherchez vos informations d’identification enregistrées.
2. Recherchez les entrées qui comportent la clé `<connection_type_key>/<corrupted_connection_name>` (par exemple, `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Supprimez ces entrées et rajoutez les connexions.
---

Si vous continuez de rencontrer cette erreur après avoir exécuté ces étapes ou si vous souhaitez faire part de vous soupçons quant à l’origine de l’endommagement des connexions, [signalez un problème](https://github.com/microsoft/AzureStorageExplorer/issues) sur notre page GitHub.

## <a name="issues-with-sas-url"></a>Problèmes liés à l’URL SAP

Si vous vous connectez à un service via une URL de signature d’accès partagé (SAP) et que vous rencontrez cette erreur :

* Vérifiez que l’URL fournit les autorisations nécessaires pour lire ou répertorier les ressources.
* Vérifiez que l’URL n’a pas expiré.
* Si l’URL SAP est basée sur une stratégie d’accès, vérifiez que cette dernière n’a pas été révoquée.

Si vous avez accidentellement procédé à un attachement au moyen d’une URL SAP non valide et que vous ne pouvez pas maintenant effectuer de détachement, suivez ces étapes :

1. Pendant que vous exécutez l’Explorateur Stockage, appuyez sur F12 pour ouvrir la fenêtre Outils de développement.
2. Sous l’onglet **Application**, sélectionnez **Stockage local** > **file://** dans l’arborescence située à gauche.
3. Recherchez la clé associée au type de service de l’URI SAS problématique. Par exemple, si l’URI SAS incorrect concerne un conteneur d’objets blob, recherchez la clé nommée `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. La valeur de la clé doit être un tableau JSON. Recherchez l’objet associé à l’URI incorrect, puis supprimez-le.
5. Appuyez sur Ctrl+R pour recharger l’Explorateur de stockage.

## <a name="linux-dependencies"></a>Dépendances Linux

### <a name="snap"></a>Snap

L’Explorateur Stockage 1.10.0 et les versions ultérieures sont disponibles en tant que snap sur le Snap Store. Le snap Explorateur Stockage installe automatiquement toutes ses dépendances, et il est mis à jour dès qu’une nouvelle version du snap est disponible. L’installation du snap Explorateur Stockage est la méthode d’installation recommandée.

Pour bien fonctionner, l’Explorateur Stockage a besoin d’un gestionnaire de mots de passe que vous pouvez être amené à connecter manuellement. Vous pouvez connecter l’Explorateur Stockage au gestionnaire de mots de passe de votre système en exécutant la commande suivante :

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>Fichier.tar.gz

Vous pouvez aussi télécharger l’application sous forme de fichier. tar. gz, mais vous devrez installer les dépendances manuellement.

L'Explorateur Stockage fourni dans le téléchargement .tar.gz est uniquement pris en charge pour les versions suivantes d’Ubuntu. Il se peut que l’Explorateur Stockage fonctionne sur d’autres distributions Linux, mais elles ne sont pas officiellement prises en charge.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

L’Explorateur Stockage exige l’installation de .NET Core sur votre système. Nous vous recommandons .NET Core 2.1, mais l’Explorateur Stockage fonctionnera également avec la version 2.2.

> [!NOTE]
> L’Explorateur Stockage 1.7.0 et versions antérieures nécessitent .NET Core 2.0. Si vous disposez d’une version plus récente de .NET Core, vous devrez appliquer une [mise à jour corrective de l’Explorateur Stockage](#patching-storage-explorer-for-newer-versions-of-net-core). Si vous exécutez l’Explorateur Stockage 1.8.0 ou une version ultérieure, vous devez utiliser .NET Core 2.1.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Téléchargez le fichier .tar.gz de l’Explorateur Stockage.
2. Installez le [runtime .NET Core](/dotnet/core/install/linux) :
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Téléchargez le fichier .tar.gz de l’Explorateur Stockage.
2. Installez le [runtime .NET Core](/dotnet/core/install/linux) :
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Téléchargez le fichier .tar.gz de l’Explorateur Stockage.
2. Installez le [runtime .NET Core](/dotnet/core/install/linux) :
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

De nombreuses bibliothèques requise par l’Explorateur Stockage sont préinstallées avec les installations standard Canonical d’Ubuntu. Il se peut que certaines de ces bibliothèques soient manquantes dans les environnements personnalisés. Si vous rencontrez des problèmes lors du lancement de l’Explorateur Stockage, nous vous recommandons de vérifier si les packages suivants sont installés sur votre système :

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Mise à jour corrective de l’Explorateur Stockage pour les versions plus récentes de .NET Core

Pour l’Explorateur Stockage 1.7.0 ou version antérieure, vous devrez peut-être appliquer une mise à jour corrective à la version de .NET Core utilisée par l’Explorateur Stockage :

1. Téléchargez la version 1.5.43 de StreamJsonRpc [à partir de NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Recherchez le lien « Download package » sur le côté droit de la page.
2. Après avoir téléchargé le package, remplacez son extension de fichier `.nupkg` par `.zip`.
3. Décompressez le package.
4. Ouvrez le dossier `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copiez `StreamJsonRpc.dll` aux emplacements suivants dans le dossier de l’Explorateur Stockage :
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>« Ouvrir dans l’Explorateur » sur le portail Azure ne fonctionne pas

Si le bouton **Ouvrir dans l’Explorateur** du portail Azure ne fonctionne pas, vérifiez que vous utilisez un navigateur compatible. Voici les navigateurs dont la compatibilité a été testée :
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Collecte des journaux

Lorsque vous signalez un problème à GitHub, il peut vous être demandé de rassembler certains journaux pour aider à diagnostiquer votre problème.

### <a name="storage-explorer-logs"></a>Journaux Explorateur Stockage

À partir de la version 1.16.0, Explorateur Stockage consigne plusieurs éléments dans ses propres journaux d’application. Vous pouvez facilement accéder à ces journaux en cliquant sur Aide > Ouvrir le répertoire des journaux. Par défaut, Explorateur Stockage consigne à un faible niveau de verbosité. Pour modifier le niveau de verbosité, ajoutez une variable d’environnement portant le nom `STG_EX_LOG_LEVEL` et l’une des valeurs suivantes :
- `silent`
- `critical`
- `error`
- `warning`
- `info` (niveau par défaut)
- `verbose`
- `debug`

Les journaux sont divisés en dossiers pour chaque session d’Explorateur Stockage que vous exécutez. Pour tous les fichiers journaux que vous devez partager, il est recommandé de les placer dans une archive zip, avec les fichiers de différentes sessions dans différents dossiers.

### <a name="authentication-logs"></a>Journaux d’authentification

Pour les problèmes liés à la connexion ou à la bibliothèque d’authentification d’Explorateur Stockage, vous devrez probablement rassembler les journaux d’authentification. Les journaux d’authentification sont stockés à l’adresse suivante :
- Windows : `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS et Linux : `~/.ServiceHub/logs`

En règle générale, vous pouvez suivre les étapes suivantes pour rassembler les journaux :

1. Accédez à **Paramètres (icône d’engrenage à gauche)**  > **Application** > **Connexion** > cochez la case **Journalisation détaillée de l’authentification**. Si Explorateur Stockage ne parvient pas à démarrer en raison d’un problème avec sa bibliothèque d’authentification, cette opération sera effectuée pour vous.
2. Fermez l’Explorateur Stockage.
1. Facultatif/recommandé : Effacez les journaux existants dans le dossier `logs`. Cela permet de réduire la quantité d’informations que vous devez nous envoyer.
4. Ouvrez Explorateur Stockage et reproduisez votre problème.
5. Fermez Explorateur Stockage.
6. Compressez le contenu du dossier `logs`.

### <a name="azcopy-logs"></a>Journaux AzCopy

Si vous avez des difficultés à transférer des données, vous devrez peut-être obtenir les journaux AzCopy. Vous pouvez facilement trouver les journaux AzCopy à l’aide de deux méthodes différentes :
- Pour les transferts qui ont échoué et qui sont encore dans le journal d’activité, cliquez sur « Accéder au fichier journal AzCopy ».
- Pour les transferts qui ont échoué dans le passé, accédez au dossier des journaux AzCopy. Ce dossier se trouve à l’emplacement suivant :
  - Windows : `C:\Users\<your username>\.azcopy`
  - macOS et Linux : `~/.azcopy

### <a name="network-logs"></a>Journaux réseau

Pour certains problèmes, vous devrez fournir des journaux des appels réseau effectués par Explorateur Stockage. Sous Windows, vous pouvez le faire en utilisant Fiddler.

> [!NOTE]
> Les traces Fiddler peuvent contenir des mots de passe que vous avez entrés/envoyés dans votre navigateur pendant la collecte de la trace. Veillez à lire les instructions sur la façon de nettoyer une trace Fiddler. Ne chargez pas les traces Fiddler sur GitHub. Il vous sera indiqué où vous pouvez envoyer votre trace Fiddler en toute sécurité.

Partie 1 : Installer et configurer Fiddler

1. Installez Fiddler.
2. Démarrez Fiddler.
3. Cliquez sur Tools (Outils) > Options.
4. Cliquez sur l’onglet HTTPS.
5. Vérifiez que les cases Capture connects (Capturer les connexions) et Decrypt HTTPS traffic (Décrypter le trafic HTTPS) sont cochées.
6. Cliquez sur le bouton Actions.
7. Choisissez « Trust Root Certificate » (Faire confiance au certificat racine), puis « Yes » (Oui) dans la boîte de dialogue suivante.
8. Cliquez à nouveau sur le bouton Actions.
9. Choisissez « Export Root Certificate to Desktop » (Exporter le certificat racine vers le Bureau).
10. Accédez à votre Bureau.
11. Recherchez le fichier FiddlerRoot.cer.
12. Double-cliquez sur ce fichier pour l’ouvrir.
13. Accédez à l’onglet Details (Détails).
14. Cliquez sur « Copy to File… » (Copier dans un fichier).
15. Dans l’Assistant d’exportation, choisissez les options suivantes :
    - X.509 encodé en base64
    - Pour le nom de fichier, parcourez (bouton « Browse… ») jusqu’à C:\Users\<your user dir>\AppData\Roaming\StorageExplorer\certs, puis vous pouvez l’enregistrer comme n’importe quel nom de fichier.
16. Fermez la fenêtre du certificat.
17. Démarrez Explorateur Stockage.
18. Accédez à Edit (Modifier) > Configure Proxy (Configurer un proxy).
19. Dans la boîte de dialogue, choisissez « Use app proxy settings » (Utiliser les paramètres du proxy d’application), puis définissez l’URL sur http://localhost et le port sur 8888.
20. Cliquez sur OK.
21. Redémarrez l’Explorateur de stockage
22. Vous devez commencer à voir les appels réseau provenant d’un processus`storageexplorer:` s’afficher dans Fiddler.

Partie 2 : Reproduire le problème
1. Fermez toutes les applications autres que Fiddler.
2. Effacez le journal de Fiddler (icône X en haut à gauche, à côté du menu View [Affichage]).
3. Facultatif/recommandé : Laissez Fiddler défini pendant quelques minutes. Si vous voyez des appels réseau apparaître, cliquez dessus avec le bouton droit et choisissez « Filter Now » (Filtrer) > « Hide <process name> » (Masquer <nom du processus>).
4. Démarrez Explorateur Stockage.
5. Reproduisez le problème
6. Cliquez sur File (Fichier) > Save (Enregistrer) > All Sessions… (Toutes les sessions…), et enregistrez dans un endroit que vous n’oublierez pas.
7. Fermez Fiddler et Explorateur Stockage.

Partie 3 : Nettoyer la trace Fiddler
1. Double-cliquez sur la trace Fiddler (fichier .saz).
2. Appuyez sur `ctrl`+`f`.
3. Dans la boîte de dialogue qui s’affiche, vérifiez que les options suivantes sont définies : Search = Requests and responses (Rechercher = Requêtes et réponses), Examine = Headers and bodies (Examiner = En-têtes et corps).
4. Recherchez les mots de passe que vous avez utilisés lors de la collecte de la trace Fiddler, toutes les entrées qui sont mises en évidence, cliquez avec le bouton droit et choisissez Remove (Supprimer) > Selected sessions (Sessions sélectionnées).
5. Si vous avez certainement entré des mots de passe dans votre navigateur lors de la collecte de la trace, mais que vous ne trouvez aucune entrée lorsque vous utilisez Ctrl+F et que vous ne souhaitez pas modifier vos mots de passe (les mots de passe que vous avez utilisés sont utilisés pour d’autres comptes), n’hésitez pas à ne pas nous envoyer le fichier .saz. Mieux vaut prévenir que guérir. :)
6. Enregistrez à nouveau la trace sous un nouveau nom.
7. Facultatif : Supprimez la trace d’origine.

## <a name="next-steps"></a>Étapes suivantes

Si aucune de ces solutions ne fonctionne, vous pouvez :
- Création d’un ticket de support
- [Ouvrez un ticket sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Vous pouvez aussi cliquer sur le bouton **Report issue to GitHub** en bas à gauche.

![Commentaires](./media/storage-explorer-troubleshooting/feedback-button.PNG)