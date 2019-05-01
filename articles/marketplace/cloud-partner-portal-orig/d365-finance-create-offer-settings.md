---
title: Comment remplir le formulaire de paramètres de l’offre | Place de marché Azure
description: Explique les différents champs qui requièrent des valeurs dans le formulaire des paramètres de l’offre pour une nouvelle application Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pabutler
ms.openlocfilehash: d29b17e1a109b37a51a0e6bd2af2a7bb02b977a9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934902"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>Comment remplir le formulaire Paramètres de l’offre
=======================================

Le formulaire Paramètres de l’offre est un formulaire de base.
Les champs obligatoires sont décrits ci-dessous.

### <a name="offer-id"></a>ID de l’offre

`OfferId` est un identificateur unique de l’offre au sein du profil du serveur de publication.
Cet ID sera visible dans les URL de produit. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.

Par exemple, si le partenaire « Contoso » crée un ID d’offre appelé « sample-Web App », il s’affichera dans AppSource en tant que :

&emsp;`https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>ID de l’éditeur

Cette liste déroulante vous permet de choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.


### <a name="name"></a>Nom

Il s’agit du nom d’affichage pour votre application/offre, qui sera affiché dans Microsoft [AppSource](https://appsource.microsoft.com/). Il ne peut pas comprendre plus de 50 caractères.

> [!NOTE]
> Le nom court doit être le même que le nom du serveur de publication spécifié dans le manifeste d’application.

Cliquez sur **Enregistrer** pour enregistrer votre progression. L’étape suivante consiste à ajouter des informations techniques à votre offre.
