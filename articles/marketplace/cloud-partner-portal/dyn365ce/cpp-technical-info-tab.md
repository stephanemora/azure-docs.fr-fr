---
title: Dynamics 365 pour Customer Engagement technique Infos | Place de marché Azure
description: Guide pratique pour spécifier les informations techniques relatives à une application Dynamics 365 for Customer Engagement sur la Place de marché AppSource.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: df7f3be5a92a183176da7851ce1943793b8b57d0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942413"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Onglet Informations techniques de Dynamics 365 for Customer Engagement

L’onglet **Informations techniques** de la page **Nouvelle offre** vous permet de spécifier des informations détaillées sur votre application Dynamics 365 for Customer Engagement, notamment les ressources de package CRM et de logo marketing.  Cet onglet est divisé en quatre sections : **Informations sur l’application**, **Package CRM**, **Disponibilité du package CRM** et **Artefacts marketing**. Un astérisque (*) en regard du nom d’un champ indique que ce champ est obligatoire. 


## <a name="application-info-section"></a>Section Informations sur l’application

Vous devez fournir des détails sur votre application Dynamics 365 dans cette section.

![Section Informations sur l’application de l’onglet Informations techniques](./media/dynce-technical-info-tab1.png)

Le tableau suivant décrit ces champs. Les champs obligatoires sont indiqués par un astérisque (*).

|      Champ                    |    Description                  |
|    ---------                  |  ---------------                |
|   Modèle de licence de base\*          |  Le modèle de licence détermine la façon dont votre application est affectée aux clients dans le centre d’administration Dynamics 365. La gestion des licences des **ressources** est basée sur l’instance, tandis que les licences **utilisateur** sont affectées à raison d’une par locataire.  |
|  S2S sortant & CRM Secure Store Access\* |  Permet la configuration de l’accès à un magasin sécurisé CRM ou de l’accès sortant S2S (serveur à serveur). *Cette fonctionnalité exige une considération spécialisée de la part de l’équipe Dynamics 365 pendant la phase de certification.* Microsoft vous contactera pour effectuer des étapes supplémentaires afin de prendre en charge cette fonctionnalité.  |
| S’abonner aux événements de cycle de vie CRM\* | L’intégration à des événements de cycle de vie Dynamics 365 vous oblige à fournir un service dédié qui est inscrit par le biais d’un contrat spécial avec Microsoft. *Cette fonctionnalité exige une considération spécialisée de la part de l’équipe Dynamics 365 pendant la phase de certification.* Vous serez contacté pour effectuer des étapes supplémentaires afin de prendre en charge cette fonctionnalité.  |
| URL de configuration d’application | URL de la page web qui permet à l’utilisateur de configurer l’application |
| Produits Dynamics 365 applicables  | Sélectionnez les produits Dynamics 365 auxquels s’applique cette offre. Cette dernière s’affichera sous les produits sélectionnés dans AppSource.  |
| Changement d’ordre marketing uniquement         | Définir cette option sur Oui indique que seuls des changements descriptifs/d’ordre marketing ont été apportés à l’offre existante.  Ces changements permettent à l’offre d’ignorer les étapes de certification et de provisionnement.  |
|  |  |


## <a name="crm-package-section"></a>Section Package CRM

Vous devez fournir des détails sur votre fichier de package AppSource dans cette section.  Ces informations seront utilisées par les équipes de validation et de certification Dynamics 365.

![Section Package CRM de l’onglet Informations techniques](./media/dynce-technical-info-tab2.png)

Le tableau suivant décrit ces champs.  Les champs obligatoires sont indiqués par un astérisque (*).

|      Champ                    |    Description                  |
|    ---------                  |  ---------------                |
|  Nom de fichier de votre package\*     |  Nom de fichier de votre package (.zip).  Ce nom n’est *pas* public et sera utilisé en interne par l’équipe de certification Dynamics 365.  |
|  URL de l’emplacement du package\*      |  URL d’un compte de Stockage Azure qui contient le fichier de package chargé. Cette URL doit inclure une clé SAP en lecture seule pour permettre à notre équipe de récupérer votre package à des fins de vérification.  |
| Plusieurs packages de crm\*     | Sélectionnez Oui UNIQUEMENT si vous prenez en charge plusieurs versions de CRM avec différents packages.  Chaque version aura un fichier de package correspondant que vous devez créer individuellement.  |
| Scénario et utilisez asset cas\*   | Permet le chargement d’un document de spécification fonctionnelle pour votre application, à utiliser par l’équipe de validation Dynamics 365.  Le format par défaut de cette spécification est le [modèle de scénario utilisateur E2E](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>Section Disponibilité du package CRM

Dans cette section, sélectionnez les régions géographiques où votre application sera disponible pour les clients.  Un déploiement sur les régions souveraines suivantes *exige une autorisation spéciale et une validation* pendant le processus de certification : [Allemagne](https://docs.microsoft.com/azure/germany/), [cloud US Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)et TIP.


## <a name="marketing-artifacts-section"></a>Section Artefacts marketing

Cette section exige que vous chargiez un logo d’application qui sera utilisé pour représenter votre package dans la Place de marché AppSource.  L’image du logo doit être au format PNG et avoir une taille de 255 x 115 pixels.


## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons de proposer une démonstration de votre application en remplissant l’[onglet Version d’évaluation](./cpp-testdrive-tab.md).
