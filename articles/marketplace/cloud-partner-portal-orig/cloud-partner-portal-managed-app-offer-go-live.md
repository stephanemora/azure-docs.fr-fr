---
title: TITRE | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 643b7d674de3d51f98b69d6f0659478c9893e3b1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806274"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Proposer votre offre Azure Application en direct sur la Place de marché Azure 
===========================================================

Maintenant que vous avez complété tous les détails de l’offre, il est temps de la publier et de la proposer en direct sur la Place de marché Azure. Plusieurs étapes doivent être suivies. Vérifiez que votre contenu marketing et votre application sont conformes aux exigences de qualité Azure avant de proposer votre offre en direct sur le site web.

![Flux de publication](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

Examinons ce processus plus en détail pour mieux comprendre ce qui se passe lors de chaque étape, et pour identifier les mesures à prendre pour garantir la progression de votre offre.

<a name="publishing-process"></a>Processus de publication 
------------------

Pour démarrer le processus de publication, cliquez sur \"Publier\" dans l’onglet Éditeur.

![Offre en direct - Séquence 1 - Publier](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

L'onglet État répertorie les étapes de publication et indique où se trouve votre offre.

![Offre en direct - Séquence 2 - Workflow](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

À tout moment au cours du processus de publication, vous pouvez également vous connecter et cliquer sur l’onglet Toutes les offres pour afficher l’état le plus récent de vos offres. Vous pouvez cliquer directement sur l’état de votre offre et afficher les détails concernant sa position dans le processus de publication.

Nous allons étudier chacune des étapes de publication pour comprendre en quoi elles consistent et estimer leur durée.

### <a name="validate-pre-requisites-1-day"></a>Valider les conditions préalables (\<1 jour) 

Lorsque vous cliquez sur \"Publier\", une vérification automatique intervient pour confirmer que vous avez renseigné tous les champs obligatoires de votre offre. Si un champ n'est pas renseigné, un avertissement apparaît en regard de celui-ci. Complétez-le correctement, puis cliquez à nouveau sur \'Publier\'.

Au terme de cette étape, une fenêtre contextuelle apparaît pour vous demander votre adresse de messagerie. Il s’agit de la messagerie sur laquelle vous recevrez les notifications d’état de publication pour le reste du processus de publication. Une fois l’adresse de messagerie communiquée, cette étape est terminée.

![Offre en direct - Séquence 1 - Publier 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>Certification (\<5 jours) 

Lors de cette étape, nous réalisons plusieurs tests afin de vérifier que votre package Azure Application est conforme aux conditions de certification Azure.

Cette étape pouvant prendre plusieurs jours, vous pouvez vous déconnecter du portail Microsoft Cloud Partner. Nous vous enverrons un e-mail de notification si des erreurs sont détectées. Si tout se passe correctement, le processus passe automatiquement à l’étape d’approvisionnement.

### <a name="packaging-and-lead-generation-registration-1-hour"></a>Empaquetage et référencement de génération de prospects (\<1 heure) 

Pendant cette phase, nous combinons le contenu technique et marketing sur la page produit du site web.

### <a name="offer-available-in-preview"></a>Offre disponible en préversion 

Vous recevrez un e-mail de notification indiquant que votre offre a rempli toutes les étapes requises et qu’elle est désormais disponible en version préliminaire. Au cours de cette étape, vous devez prévisualiser votre offre et vous assurer que tout se présente comme prévu. Vérifiez que votre machine virtuelle est correctement déployée dans l'environnement intermédiaire.

Cette vérification est réservée aux abonnements figurant dans la liste approuvée.\*

### <a name="publisher-sign-out"></a>Déconnexion de l'éditeur 

Lorsque vous aurez vérifié que tous les éléments fonctionnent correctement en version préliminaire, vous êtes prêt à proposer votre offre en direct. Cliquez sur Démarrer sous l’onglet État afin de lancer le processus permettant à votre offre de s’exécuter dans un environnement de production et sur le site web. En règle générale,plusieurs heures peuvent s’écouler entre le moment où vous cliquez sur le bouton Démarrer et le moment où votre offre est disponible en direct sur le site web. Nous vous enverrons un e-mail de notification lorsque votre offre sera officiellement disponible en direct sur le site web.

![Offre en direct - Séquence 6 - Démarrer](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Révision Microsoft 

Lorsque vous êtes prêt et que vous avez cliqué sur \"Démarrer\", un site de travail est créé dans Visual Studio Online, puis une révision du code Microsoft est effectuée sur le contenu du package. La révision du code contient les meilleurs modèles/meilleures pratiques à utiliser lors de la création de modèles, des conseils et des astuces pour optimiser la création de ressources. Si, à l'issue de la révision, l'éditeur apporte des modifications aux fichiers, le processus de publication doit être redémarré. La publication en cours est alors annulée et vous devez procéder à une nouvelle publication en tenant compte des commentaires reçus.

### <a name="live"></a>En direct

Votre offre est désormais disponible en direct sur la Place de marché Azure et le portail Azure. Vos clients peuvent afficher et déployer votre application managée Azure à partir de leur abonnement Azure. Vous pouvez cliquer sur l’onglet Toutes les offres et voir l’état de toutes vos offres dans la colonne de droite. Vous pouvez cliquer sur l’état pour afficher en détail l’état de flux de publication de votre offre.

### <a name="error-handling"></a>Gestion des erreurs 

En cas d'erreur, vous recevrez un e-mail de notification contenant des instructions sur les étapes à suivre. Vous pouvez également voir les erreurs à tout moment au cours de ce processus en cliquant sur l’onglet État. Vous verrez à quel moment du processus l’erreur s’est produite, ainsi qu’un message indiquant les éléments nécessitant votre attention.

Si vous rencontrez des erreurs pendant le processus de publication, vous devez les corriger, puis cliquer sur \'Publier\' pour redémarrer le processus. Vous devez rependre au début des étapes de publication de la séquence Valider les conditions préalables lorsque vous republiez après avoir corrigé une erreur.

Si vous rencontrez des problèmes lors de la résolution d’une erreur, vous pouvez ouvrir une demande de support pour obtenir l’aide de nos ingénieurs du support technique.

### <a name="canceling-the-publishing-request"></a>Annulation de la demande de publication

Vous pouvez démarrer le processus de publication, puis vous rendre compte que vous devez annuler votre demande. Vous pouvez annuler une demande de publication uniquement si l’étape de validation de l’éditeur a été atteinte. Pour annuler, cliquez sur \'Annuler la publication\'. L’état de publication est réinitialisé à l’étape 1, et pour publier de nouveau, vous devez cliquer sur Publier et suivre les étapes de l’état.
