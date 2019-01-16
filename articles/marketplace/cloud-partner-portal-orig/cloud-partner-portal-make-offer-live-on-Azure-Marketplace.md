---
title: Proposer votre offre de machine virtuelle en direct sur la Place de marché Azure
description: Proposer votre offre de machine virtuelle en direct sur la Place de marché Azure
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
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
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075336"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Proposer votre offre de machine virtuelle en direct sur la Place de marché Azure
=========================================================

Une fois tous les détails de l’offre complétés, vous pouvez la publier et la proposer en direct sur la Place de marché Azure. L'offre passe par plusieurs étapes. Vérifiez que le contenu marketing et l’image de votre machine virtuelle sont conformes aux exigences de qualité Azure en vue de proposer votre offre en direct sur le site web.

![Offre en direct - Séquence 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

Examinons ce processus plus en détail pour mieux comprendre ce qui se passe à chaque étape. Au cours de ce processus, vous devrez intervenir pour veiller à ce que la progression de votre offre se poursuive.

<a name="publishing-process"></a>Processus de publication
------------------

Cliquez sur « Publier » sous l’onglet Éditeur pour démarrer le processus de publication.

![Offre en direct - Séquence 1 - Publier](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

Sous l’onglet État, vous verrez les étapes de publication et la position de votre offre dans le processus.

![Offre en direct - Séquence 2 - État](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

À tout moment au cours du processus de publication, vous pouvez également vous connecter et cliquer sur l’onglet Toutes les offres pour afficher l’état le plus récent de vos offres. Vous pouvez cliquer directement sur l’état de votre offre et afficher les détails concernant sa position dans le processus de publication.

![Offre en direct - Séquence 3 - État de toutes les offres](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Nous allons étudier chacune des étapes de publication pour comprendre en quoi elles consistent et estimer leur durée.

**Valider les conditions préalables (\<1 jour)**

Lorsque vous cliquez sur « Publier », une vérification automatique intervient pour confirmer que vous avez renseigné tous les champs obligatoires de votre offre. Si tous les champs ne sont pas renseignés, un avertissement s’affiche en regard du champ vide. Remplissez-le correctement, puis cliquez sur « Publier » à nouveau.

Au terme de cette étape, une fenêtre contextuelle vous demande une adresse e-mail. Il s’agit de la messagerie sur laquelle vous recevrez les notifications d’état de publication pour le reste du processus de publication. Une fois l’adresse de messagerie communiquée, cette étape est terminée.

![Offre en direct - Séquence 4 - Publier votre offre](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certification (\<5 jours)**

Lors de cette étape, nous exécutons plusieurs tests afin de vérifier que votre image de machine virtuelle répond aux conditions de certification Azure. Vous trouverez [ici](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md) tous les conseils nécessaires pour remplir les conditions de certification.

Cette étape pouvant prendre plusieurs jours, vous pouvez vous déconnecter du portail Microsoft Cloud Partner. Nous vous enverrons un e-mail de notification si des erreurs sont à résoudre. Si tout se passe correctement, le processus passe automatiquement à l’étape d’approvisionnement.

**Approvisionnement (\<1 jour)**

Pendant cette phase, nous répliquons vos images vers tous les centres de données Azure dans le monde. Cette opération peut prendre jusqu’à 24 heures.

**Empaquetage et référencement de génération de prospects (\<1 heure)**

Pendant cette phase, nous combinons le contenu technique et marketing sur la page produit du site web.

En outre, si vous avez configuré la fonctionnalité de génération de prospect, nous vérifions que votre intégration CRM fonctionne en lui envoyant un message test. Vous verrez un enregistrement contenant des données fictives dans votre CRM ou dans votre table Azure une fois cette étape terminée. Toute la documentation concernant la génération de prospects se trouve [ici](./cloud-partner-portal-get-customer-leads.md).

**Offre disponible en version préliminaire**

Vous recevrez un e-mail de notification indiquant que votre offre a rempli toutes les étapes requises et qu’elle est désormais disponible en version préliminaire. Au cours de cette étape, il est recommandé d’afficher un aperçu de votre offre afin de vous assurer que tout se présente comme il convient et que votre machine virtuelle se déploie correctement dans l’environnement intermédiaire.

*Seuls les abonnements figurant dans la liste verte peuvent procéder à cette vérification.*

**Validation de l’éditeur**

Lorsque vous aurez vérifié que tous les éléments fonctionnent correctement en version préliminaire, vous êtes prêt à proposer votre offre en direct. Cliquez sur **Démarrer** sous l’onglet **État** afin de lancer le processus permettant à votre offre de s’exécuter dans un environnement de production et sur le site web. En règle générale,plusieurs heures peuvent s’écouler entre le moment où vous cliquez sur le bouton Démarrer et le moment où votre offre est disponible en direct sur le site web. Nous vous enverrons un e-mail de notification lorsque votre offre sera officiellement disponible en direct sur le site web.

![Offre en direct - Séquence 5 - Démarrer](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**En direct**

Votre offre est désormais disponible en direct sur la Place de marché Azure et sur le portail Azure. Les clients sont ainsi en mesure d’afficher et de déployer votre machine virtuelle dans leurs abonnements Azure. À tout moment, vous pouvez cliquer sur l’onglet Toutes les offres et voir l’état de toutes vos offres répertorié dans la colonne de droite. Vous pouvez cliquer sur l’état pour afficher en détail l’état de flux de publication de votre offre.

<a name="error-handling"></a>Gestion des erreurs
--------------

Une erreur peut se produire lors du processus de publication. Le cas échéant, vous recevrez un e-mail de notification vous informant de l’erreur et contenant des instructions pour les étapes à suivre. Vous pouvez également voir les erreurs à tout moment au cours de ce processus en cliquant sur l’onglet État. Vous verrez à quel moment du processus l’erreur s’est produite, ainsi qu’un message indiquant les éléments nécessitant votre attention.

![Offre en direct - Séquence 6 - Message d'erreur](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

Si vous rencontrez des erreurs pendant le processus de publication, vous devez corriger les problèmes, puis cliquer sur « Publier » pour redémarrer le processus. Vous devez rependre au début des étapes de publication de la séquence « Valider les conditions préalables » quand vous republiez après avoir corrigé une erreur.

Si vous rencontrez des problèmes lors de la résolution d’une erreur, vous pouvez ouvrir une demande de support pour obtenir l’aide de nos ingénieurs du support technique.

![Offre en direct - Séquence 7 - Obtenir de l'aide](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>Annulation de la demande de publication
--------------------------------

Vous pouvez démarrer le processus de publication, puis vous rendre compte que vous devez annuler votre demande. Vous ne pouvez annuler une demande de publication que si l’étape de validation de l’éditeur a été atteinte. Pour annuler, cliquez sur « Annuler la publication ». L’état de publication est réinitialisé à l’étape 1, et pour publier de nouveau, vous devez cliquer sur Publier et suivre les étapes de l’état.

![Offre en direct - Séquence 8 - État](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

