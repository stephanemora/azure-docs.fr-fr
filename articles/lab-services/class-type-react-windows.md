---
title: Configurer un labo pour enseigner le développement frontal avec React sur Windows à l’aide d’Azure Lab Services
description: Découvrez comment configurer des labos pour enseigner le développement front-end avec React.
author: emaher
ms.topic: article
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 089ec3d3d4836b4dadb5a0a60023b85ebf9950e0
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112456541"
---
# <a name="set-up-lab-for-react-on-windows"></a>Configurer un labo pour React sur Windows

[React](https://reactjs.org/) est une bibliothèque JavaScript couramment utilisée pour créer des interfaces utilisateur (IU). React est un moyen déclaratif de créer des composants réutilisables pour votre site web.  De nombreuses autres bibliothèques sont disponibles pour le développement front-end basé sur JavaScript.  Nous utiliserons quelques-unes de ces bibliothèques lors de la création de notre labo.  [Redux](https://redux.js.org/) est une bibliothèque qui fournit un conteneur d'état prévisible pour les applications JavaScript. Elle est souvent utilisée en complément de React. [JSX](https://reactjs.org/docs/introducing-jsx.html) est une extension syntaxique de la bibliothèque JavaScript souvent utilisée avec React pour décrire ce à quoi l'interface utilisateur doit ressembler.  [NodeJS](https://nodejs.org/) est un moyen pratique d'exécuter un serveur web pour votre application React.

Cet article explique comment installer [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pour votre environnement de développement, et présente les outils et bibliothèques nécessaires pour un cours de développement web avec React.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre tutoriel sur [la configuration d’un compte de labo](./tutorial-setup-lab-account.md). Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres de votre compte Lab (cf. tableau suivant). Pour plus d’informations sur l’activation des images de la Place de marché Azure, consultez [Spécification des images de la Place de marché Azure accessibles aux créateurs d’instances Lab](./specify-marketplace-images.md).

| Paramètres du compte lab | Instructions |
| -------------------- | ----- |
| Image de la Place de marché | Activez l’image « Visual Studio 2019 Community (dernière version) sur Windows Server 2019 (x64) » pour l’utiliser dans votre compte lab. |

### <a name="lab-settings"></a>Paramètres du labo

La taille de la machine virtuelle que nous vous recommandons dépend des types de charges de travail qui sont soumises aux élèves.  

| Paramètre de labo | Valeur et description |
| ------------ | ------------------ |
| Taille de la machine virtuelle | **Moyenne** |

Nous vous recommandons de tester vos charges de travail pour savoir si une taille supérieure est nécessaire.  Pour plus d'informations sur chaque taille, consultez [Dimensionnement des machines virtuelles](administrator-guide.md#vm-sizing).

## <a name="template-machine-configuration"></a>Configuration du modèle de machine

Pour configurer le modèle de machine virtuelle, procédez comme suit :

1. Installez les outils de développement.
1. Installez les extensions de débogueur correspondant à votre navigateur web.
1. Mettez à jour les paramètres du pare-feu.

### <a name="install-development-tools"></a>Installer les outils de développement

La [charge de travail de **développement Node.js**](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true#prerequisites) requise est déjà installée pour [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) sur l’image Visual Studio 2019 Community (version la plus récente) sur Windows Server 2019 (x64).

1. Installez votre navigateur web préféré.  Internet Explorer est installé par défaut sur l’image.
1. Accédez au site web [Node.js](https://nodejs.org) et sélectionnez le bouton **Télécharger**.  Vous pouvez utiliser la dernière version du service à long terme (LTS), la version actuelle avec les fonctionnalités les plus récentes ou une version précédente.  Installer Node JS installera également [Node Package Manager](https://www.npmjs.com/). Il sera utilisé pour installer React, Redux et JSX.
1. [Mettez à jour Visual Studio 2019](/visualstudio/install/update-visual-studio?view=vs-2019&preserve-view=true) vers la dernière version, si nécessaire.

Les autres composants nécessaires à un site web basé sur React sont installés à l'aide de NPM dans une application spécifique.  Pour ajouter des packages NPM, consultez [gérer vos packages NPM dans Visual Studio](/visualstudio/javascript/npm-package-management?view=vs-2019&preserve-view=true#add-npm-packages).  

Par exemple, si vous utilisez la [fenêtre Node.js interactive](/visualstudio/javascript/nodejs-interactive-repl?view=vs-2019&preserve-view=true) dans un projet, entrez les commandes suivantes pour installer les bibliothèques React, Redux et JSX :

```bash
.npm install react
.npm install react-dom
.npm install react-redux
.npm install react-jsx
```

Pour créer votre première Node.js avec l’application React dans Visual Studio, consultez [Didacticiel : créer une application Node.js et React dans Visual Studio](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true).

### <a name="install-debugger-extensions"></a>Installer les extensions de débogueur

Installez les extensions Outils de développement React correspondant à votre navigateur pour pouvoir inspecter les composants React et enregistrer les informations de performances.  

- [Module complémentaire Edge pour l'extension Outils de développement React](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [Module complémentaire Chrome pour l'extension Outils de développement React](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Module complémentaire FireFox pour l'extension Outils de développement React](https://addons.mozilla.org/firefox/addon/react-devtools/)

### <a name="update-firewall-settings"></a>Mettre à jour les paramètres du pare-feu

Par défaut, le trafic entrant vers votre serveur Node.js est bloqué.  Si vous souhaitez accéder au site Web d’un étudiant pendant qu’il est en cours d’exécution, ajoutez une règle de pare-feu entrante pour autoriser le trafic.  Examinez la propriété **port d’application** du projet pour déterminer le port qui sera utilisé pendant le débogage.  L’exemple ci-dessous utilise le cmdlet PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2019-ps&preserve-view=true) pour autoriser l’accès au port 1337.  

```powershell
New-NetFirewallRule -DisplayName "Allow access to Port 1337" -Direction Inbound -LocalPort 1337 -Protocol TCP -Action Allow
```

>[!IMPORTANT]
>Les formateurs doivent utiliser le modèle de machine virtuelle ou une autre machine virtuelle lab pour accéder au site web d'un élève.

## <a name="cost"></a>Coût

Penchons-nous sur un exemple d’estimation du coût pour ce cours.  Supposons que vous ayez une classe de 25 élèves. Chaque élève dispose de 20 heures de cours programmées.  Chaque élève dispose également d'un quota de 10 heures pour les devoirs et activités à la maison en dehors des heures de cours programmées.  La taille de machine virtuelle que nous avons choisie était **Moyenne**, soit 55 unités Lab.

- 25 élèves &times; (20 heures programmées + quota de 10 heures) &times; 55 unités Lab &times; 0,01 USD/heure = 412,50 USD

> [!IMPORTANT]
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour connaître les tarifs actuels, consultez [Tarifs Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Étapes suivantes

L’image du modèle peut à présent être publiée dans le labo. Pour plus d'informations, consultez [Publier le modèle de machine virtuelle](how-to-create-manage-template.md#publish-the-template-vm).

Au fil de la configuration de votre instance Lab, consultez les articles suivants :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir les quotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)
