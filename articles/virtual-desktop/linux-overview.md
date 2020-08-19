---
title: Prise en charge Linux pour Windows Virtual Desktop – Azure
description: Une brève présentation de la prise en charge Linux pour Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008693"
---
# <a name="linux-support"></a>Prise en charge Linux

Vous pouvez accéder aux ressources de Windows Virtual Desktop à partir de vos appareils Linux avec les clients pris en charge suivants, fournis par nos partenaires clients légers Linux. Nous travaillons en collaboration avec un certain nombre de partenaires pour activer les clients Windows Virtual Desktop pris en charge sur d’autres systèmes d’exploitation et périphériques basés sur Linux. Si vous souhaitez prendre en charge Windows Virtual Desktop sur une plateforme Linux qui n’est pas répertoriée ici, faites-le nous savoir sur notre page [UserVoice](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Se connecter avec votre appareil Linux

Les partenaires suivants ont approuvé les clients Windows Virtual Desktop pour les appareils Linux.

|Partenaire|Documentation du partenaire|Support technique du partenaire|
|:------|:--------------------|:--------------|
|![Logo IGEL](./media/partners/igel.png)|[Documentation du client IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Support technique IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Qu’est-ce que le Kit de développement logiciel (SDK) Linux ?

Les partenaires de client léger Linux peuvent utiliser les API du Kit de développement logiciel (SDK) Linux de Windows Virtual Desktop afin de récupérer des flux de ressources, se connecter à des sessions d’applications à distance ou de Bureau et utiliser une grande partie des redirections prises en charge par nos clients principaux. Le Kit de développement logiciel (SDK) est compatible avec la plupart des systèmes d’exploitation basés sur Ubuntu 18.04 ou une version ultérieure.

### <a name="feature-support"></a>Prise en charge des fonctionnalités

Le Kit de développement logiciel (SDK) prend en charge plusieurs connexions aux sessions d’applications à distance ou de Bureau. Les redirections suivantes sont prises en charge :

| Redirection       | Prise en charge |
| :---------------- | :-------: |
| Clavier          | &#10004;  |
| Souris             | &#10004;  |
| Entrée audio          | &#10004;  |
| Sortie audio         | &#10004;  |
| Presse-papiers (texte)  | &#10004;  |
| Presse-papiers (image) | &#10004;  |
| Presse-papiers (fichier)  | &#10004;  |
| Carte à puce         | &#10004;  |
| Lecteur/dossier      | &#10004;  |

Le Kit de développement logiciel (SDK) prend également en charge plusieurs configurations d’affichage des écrans, à condition que les écrans que vous sélectionnez pour votre session soient contigus.

Nous mettrons à jour ce document au fur et à mesure que nous ajoutons la prise en charge des nouvelles fonctionnalités et redirections. Si vous souhaitez suggérer de nouvelles fonctionnalités et d’autres améliorations, rendez-vous sur notre [page UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="next-steps"></a>Étapes suivantes

Consultez notre documentation pour les clients suivants :

- [Client Windows Desktop](connect-windows-7-10.md)
- [Client web](connect-web.md)
- [Client Android](connect-android.md)
- [Client macOS](connect-macos.md)
- [Client iOS](connect-ios.md)
