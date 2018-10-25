---
title: Étapes de publication d’application | Microsoft Docs
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806298"
---
<a name="app-publishing-steps"></a>Étapes de publication d’application
====================

Pour démarrer le processus de publication, cliquez sur « Publier » dans l’onglet Éditeur.

![Bouton de publication d’application sur le CPP](./media/d365-financials/image014.jpg)


Sous l’onglet État, vous verrez les étapes de publication indiquant la position de votre offre dans le processus de publication. À tout moment au cours du processus de publication, vous pouvez également vous connecter et cliquer sur l’onglet Toutes les offres pour afficher l’état le plus récent de vos offres. Vous pouvez cliquer directement sur l’état de votre offre et afficher les détails concernant sa position dans le processus de publication.

Nous allons étudier chacune des étapes de publication pour comprendre en quoi elles consistent et estimer leur durée.

![Diagramme du processus de publication](./media/d365-financials/image017.png)


**Valider les prérequis**

Lorsque vous cliquez sur « Publier », une vérification automatique s’effectue afin de confirmer que vous avez rempli tous les champs obligatoires pour votre offre. Si tous les champs ne sont pas renseignés, un avertissement s’affiche en regard du champ vide. Remplissez-le correctement, puis cliquez sur « Publier » à nouveau.

Une fois cette étape terminée, une fenêtre contextuelle apparaît pour vous demander votre adresse de messagerie, qui servira à vous envoyer des notifications de publication. Une fois l’adresse de messagerie communiquée, cette étape est terminée.


**Validation automatique de l’application**

À cette étape, notre service de certification automatique vérifie les extensions d’application fournies avec une offre dont le contenu correspond aux métadonnées fournies. Assurez-vous toujours que le nom, la version, l’éditeur et l’ID de votre application correspondent à ceux fournis dans le manifeste d’extension nommé `app.json`.


**Validation Test Drive**

Si vous avez choisi de configurer Test Drive pour une version d’évaluation, cette étape valide vos paramètres Test Drive.


**Validation et inscription de la gestion des prospects**

À cette étape, si vous avez configuré la fonctionnalité de génération de prospect, nous vérifions que votre intégration CRM fonctionne en lui envoyant un message test. Vous verrez un enregistrement contenant des données fictives dans votre CRM ou dans votre table Azure une fois cette étape terminée. Toute la documentation concernant la génération de prospects se trouve ici.


**Création de paquet sur AppSource**

Vos artefacts des détails de la vitrine sont vérifiés, et le package d’aperçu AppSource est généré.


**Déconnexion de l’éditeur**

Durant cette phase, le bouton **Démarrer** devient actif. Vous disposez maintenant d’un lien pour prévisualiser votre offre (avec votre hidekey). Une fois que vous êtes satisfait de l’aspect de l’aperçu, cliquez sur le bouton Démarrer.
Gardez à l’esprit que cette requête ne publie pas directement votre application sur AppSource, mais qu’elle déclenche notre processus de validation interne.


**Validation marketing et technique de l’application**

C’est à cette étape que nous effectuons les validations marketing et technique en parallèle. Pour connaître les exigences et les recommandations, reportez-vous aux documents [Checklist for Submitting Your App](https://aka.ms/CheckBeforeYouSubmit) (Liste de contrôle pour la soumission de votre application) et le livre blanc [Developing Apps for Dynamics 365 for Finance and Operations](https://go.microsoft.com/fwlink/?linkid=841518) (Développer des applications financières et opérationnelles pour Dynamics 365). Pendant le processus de validation, nous allons :
-  travailler avec vous sur les problèmes et questions en suspens ;  
- vous fournir une date de publication d’application et vous avertir quand votre application est publiée ; 
- vous fournir un premier retour sur les aspects techniques et marketing de la validation dans la délai de 5 à 7 jours ouvrables.

Ces étapes peuvent généralement durer plus d’une semaine, mais vous n’avez pas besoin de rester connecté en permanence sur le Portail Microsoft Cloud Partner.


**Publier l’application avec le service**

Votre offre subit un traitement final. Votre application a passé avec succès la validation marketing et technique, mais doit maintenant passer par un traitement final en vue de sa mise en ligne sur AppSource.


**En direct**

Votre offre est désormais disponible en direct sur AppSource, et les clients sont en mesure d’afficher et de déployer votre application dans leurs abonnements Microsoft Dynamics 365 Business Central. Vous recevrez un e-mail de notre part vous informant que votre application a été publiée sur AppSource. À tout moment, vous pouvez cliquer sur l’onglet Toutes les offres et voir l’état de toutes vos offres répertorié dans la colonne de droite. Vous pouvez cliquer sur l’état pour afficher en détail l’état de flux de publication de votre offre.


<a name="error-handling"></a>Gestion des erreurs
--------------

Une erreur peut se produire lors du processus de publication. Le cas échéant, vous recevrez un e-mail de notification vous informant de l’erreur et contenant des instructions pour les étapes à suivre. Vous pouvez également voir les erreurs à tout moment au cours de ce processus en cliquant sur l’onglet État. Vous verrez à quel moment du processus l’erreur s’est produite, ainsi qu’un message indiquant les éléments nécessitant votre attention.

Si vous rencontrez des erreurs pendant le processus de publication, vous devez les corriger, puis cliquer sur **Publier** pour redémarrer le processus. Vous devez rependre au début des étapes de publication de la séquence **Valider les conditions préalables** lorsque vous republiez après avoir corrigé une erreur.

Si vous rencontrez des problèmes lors de la résolution d’une erreur, vous pouvez ouvrir une demande de support pour obtenir l’aide de nos ingénieurs du support technique.


<a name="canceling-the-publishing-request"></a>Annulation de la demande de publication
--------------------------------

Vous pouvez démarrer le processus de publication, puis vous rendre compte que vous devez annuler votre demande. Vous pouvez annuler une demande de publication uniquement si l’étape de validation de l’éditeur a été atteinte. Pour annuler, cliquez sur **Annuler la publication**. L’état de publication est réinitialisé à l’étape 1, et pour publier de nouveau, vous devez cliquer sur Publier et suivre les étapes de l’état.

![Bouton Annuler la publication](./media/d365-financials/image013.png)
