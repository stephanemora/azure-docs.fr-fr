---
title: Prise en charge Linux pour Windows Virtual Desktop – Azure
description: Une brève présentation de la prise en charge Linux pour Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3dc9d62141a63574a6796982542cb1491108eedc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259857"
---
# <a name="linux-support"></a>Prise en charge Linux

Les partenaires peuvent utiliser le SDK Linux pour Windows Virtual Desktop pour créer un client Windows Virtual Desktop autonome. Vous pouvez également l’utiliser pour activer la prise en charge de Windows Virtual Desktop sur votre application cliente. Ce guide rapide explique ce qu’est le Kit de développement logiciel (SDK) Linux et comment commencer à l’utiliser.

## <a name="connect-with-your-linux-device"></a>Se connecter avec votre appareil Linux

Les partenaires suivants ont approuvé les clients Windows Virtual Desktop pour les appareils Linux.

|Partenaire|Documentation du partenaire|Support technique du partenaire|
|:------|:--------------------|:--------------|
|![Logo IGEL](./media/partners/igel.png)|[Documentation du client IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Support technique IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Qu’est-ce que le Kit de développement logiciel (SDK) Linux ?

Vous pouvez utiliser les API du Kit de développement logiciel (SDK) pour récupérer des flux de ressources, vous connecter à des sessions d’applications à distance ou de Bureau et utiliser une grande partie des redirections prises en charge par nos clients principaux.

> [!NOTE]
> Le SDK est en cours de développement. Nous mettrons à jour ce document avec des instructions permettant d’accéder au SDK quand il sera en disponibilité générale.

### <a name="supported-linux-distributions"></a>Distributions de Linux prises en charge

Le Kit de développement logiciel (SDK) est compatible avec la plupart des systèmes d’exploitation basés sur Ubuntu 18.04 ou une version ultérieure. Si vous avez une distribution Linux différente, nous pouvons travailler avec vous pour trouver la meilleure façon de répondre à vos besoins.

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

## <a name="get-started-with-the-linux-sdk"></a>Prise en main du Kit de développement logiciel (SDK) Linux

Avant de pouvoir développer un client Linux pour Windows Virtual Desktop, vous devez effectuer les opérations suivantes :

1. Créer et déployer un environnement Windows Virtual Desktop à des fins de test ou de production.
2. Tester les clients principaux disponibles pour vous familiariser avec l’expérience utilisateur de Windows Virtual Desktop.

## <a name="next-steps"></a>Étapes suivantes

Consultez notre documentation pour les clients suivants :

- [Client Windows Desktop](connect-windows-7-10.md)
- [Client web](connect-web.md)
- [Client Android](connect-android.md)
- [Client macOS](connect-macos.md)
- [Client iOS](connect-ios.md)
