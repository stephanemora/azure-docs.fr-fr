---
title: Configurer un lab avec React sur Linux à l'aide d'Azure Lab Services
description: Apprenez à configurer des labs pour des cours de développement avec React.
author: emaher
ms.topic: article
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 60ac7c3a95564fad5c271c543beac875334b05a1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482778"
---
# <a name="set-up-lab-for-react-on-linux"></a>Configurer un lab pour React sur Linux

[React](https://reactjs.org/) est une bibliothèque JavaScript couramment utilisée pour créer des interfaces utilisateur (IU). React est un moyen déclaratif de créer des composants réutilisables pour votre site web.  De nombreuses autres bibliothèques sont disponibles pour le développement front-end basé sur JavaScript.  Nous utiliserons quelques-unes de ces bibliothèques lors de la création de notre labo.  [Redux](https://redux.js.org/) est une bibliothèque qui fournit un conteneur d'état prévisible pour les applications JavaScript. Elle est souvent utilisée en complément de React. [Jsx](https://reactjs.org/docs/introducing-jsx.html) est une extension syntaxique de la bibliothèque JavaScript souvent utilisée avec React pour décrire ce à quoi l'interface utilisateur doit ressembler.  [NodeJS](https://nodejs.org/) est un moyen pratique d'exécuter un serveur web pour votre application React.

Cet article explique comment installer [Visual Studio Code](https://code.visualstudio.com/) pour votre environnement de développement, et présente les outils et bibliothèques nécessaires pour un cours de développement web avec React.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre tutoriel sur [la configuration d’un compte de labo](./tutorial-setup-lab-account.md). Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres de votre compte Lab (cf. tableau suivant). Pour plus d’informations sur l’activation des images de la Place de marché Azure, consultez [Spécification des images de la Place de marché Azure accessibles aux créateurs d’instances Lab](./specify-marketplace-images.md).

| Paramètres du compte lab | Instructions |
| ----------- | ------------ |  
| Images de la Place de marché | Activez l'image « Ubuntu Server 18.04 LTS » pour l'utiliser dans votre compte lab. |

### <a name="lab-settings"></a>Paramètres du labo

La taille de la machine virtuelle que nous vous recommandons dépend des types de charges de travail qui sont soumises aux élèves.  

| Paramètre de labo | Valeur et description |
| ------------ | ------------------ |
| Taille de la machine virtuelle | **Petite**.|

Nous vous recommandons de tester vos charges de travail pour savoir si une taille supérieure est nécessaire.  Pour plus d'informations sur chaque taille, consultez [Dimensionnement des machines virtuelles](administrator-guide.md#vm-sizing).

## <a name="template-machine-configuration"></a>Configuration du modèle de machine

Pour configurer le modèle de machine virtuelle, procédez comme suit :

1. Installez les outils de développement.
1. Installez les extensions de débogueur correspondant à votre navigateur web.
1. Mettez à jour les paramètres du pare-feu.

### <a name="install-development-tools"></a>Installer les outils de développement

1. Installez votre navigateur web préféré.  
1. Installez [Node.js](https://nodejs.org).

    ```bash
    sudo apt install nodejs
    ```

1. Installez [Node Package Manager](https://www.npmjs.com/). Il sera utilisé pour installer React, Redux et JSX.

    ```bash
    sudo apt install npm
    ```

1. Installez [Visual Studio Code](https://code.visualstudio.com/docs/setup/linux).
1. Installez l'[extension Outils React Native pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=msjsdiag.vscode-react-native).
1. Si vous le souhaitez, vous pouvez également installer des extensions destinées au développement à l'aide de [Redux](https://marketplace.visualstudio.com/search?term=Redux&target=VSCode&category=All%20categories&sortBy=Relevance) et [JSX](https://marketplace.visualstudio.com/search?term=JSX&target=VSCode&category=All%20categories&sortBy=Relevance).

[Create React App](https://create-react-app.dev/) prend officiellement en charge la création d'applications React et ne nécessite aucune configuration supplémentaire si vous utilisez NPM 5.2 ou version ultérieure.  Pour plus d'informations sur l'utilisation de Create React App, consultez la documentation de [prise en main](https://create-react-app.dev/docs/getting-started) correspondante.

Les autres composants nécessaires à un site web basé sur React sont installés à l'aide de NPM dans une application spécifique. Par exemple, entrez les commandes suivantes pour installer les bibliothèques Redux et JSX :

```bash
npm install react-redux
npm install react-jsx
```

### <a name="install-debugger-extensions"></a>Installer les extensions de débogueur

Installez les extensions Outils de développement React correspondant à votre navigateur pour pouvoir inspecter les composants React et enregistrer les informations de performances.  

- [Module complémentaire Edge pour l'extension Outils de développement React](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [Module complémentaire Chrome pour l'extension Outils de développement React](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [Module complémentaire FireFox pour l'extension Outils de développement React](https://addons.mozilla.org/firefox/addon/react-devtools/)

Pour exécuter l'application en mode de développement, utilisez la commande `npm start` intégrée.  Les URL locales et réseau seront répertoriées dans la sortie de la commande.  Pour utiliser HTTPS au lieu de HTTP, consultez [Créer une application React à l'aide du protocole HTTPS dans le cadre du développement](https://create-react-app.dev/docs/using-https-in-development).

>[!IMPORTANT]
>Les formateurs doivent utiliser le modèle de machine virtuelle ou une autre machine virtuelle lab pour accéder au site web d'un élève.

## <a name="cost"></a>Coût

Penchons-nous sur un exemple d’estimation du coût pour ce cours.  Supposons que vous ayez une classe de 25 élèves. Chaque élève dispose de 20 heures de cours programmées.  Chaque élève dispose également d'un quota de 10 heures pour les devoirs et activités à la maison en dehors des heures de cours programmées.  Nous avons choisi la taille de machine virtuelle **Petite**, soit 20 unités Lab.

- 25 élèves &times; (20 heures programmées + quota de 10 heures) &times; 20 unités Lab &times; 0,01 USD/heure = 150,00 USD

> [!IMPORTANT]
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour connaître les tarifs actuels, consultez [Tarifs Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Étapes suivantes

L’image du modèle peut à présent être publiée dans le labo. Pour plus d'informations, consultez [Publier le modèle de machine virtuelle](how-to-create-manage-template.md#publish-the-template-vm).

Au fil de la configuration de votre instance Lab, consultez les articles suivants :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir les quotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)
