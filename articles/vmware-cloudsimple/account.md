---
title: Gestion des comptes - Portail Azure VMware Solutions (AVS)
description: Explique comment gérer les comptes sur le portail Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025365"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Gérer les comptes sur le portail Azure VMware Solutions (AVS)

Lorsque vous créez votre service AVS, celui-ci crée un compte sur AVS. Ce compte est associé à votre abonnement Azure où se trouve le service. Tous les utilisateurs de l'abonnement qui disposent du rôle de propriétaire ou de contributeur ont accès au portail AVS. L'ID d'abonnement Azure et l'ID de locataire associés au service AVS figurent sur la page Comptes.

Pour gérer les comptes sur le portail AVS, [accédez au portail](access-cloudsimple-portal.md) et sélectionnez **Compte** dans le menu latéral.

Sélectionnez **Résumé** pour afficher des informations sur la configuration AVS de votre entreprise. La capacité actuelle de votre configuration cloud est indiquée, avec notamment le nombre de clouds privés AVS, le stockage total, la configuration du cluster vSphere, le nombre de nœuds et le nombre de cœurs de calcul. Un lien est inclus pour l’achat de nœuds supplémentaires si la configuration actuelle ne répond pas à tous vos besoins.

## <a name="email-alerts"></a>Alertes par e-mail

Vous pouvez ajouter les adresses e-mail des personnes que vous souhaitez avertir des modifications apportées à la configuration du cloud privé AVS.

1. Dans la zone **Alertes e-mail supplémentaires**, cliquez sur **Ajouter**.
2. Saisissez l'adresse e-mail.
3. Appuyez sur Retour.  

Pour supprimer une entrée, cliquez sur **X**.

## <a name="avs-operator-access"></a>Accès opérateur AVS

Le paramètre d'accès opérateur permet à AVS de vous aider à résoudre les problèmes en permettant à un ingénieur du support technique de se connecter à votre portail AVS. Le paramètre est activé par défaut. Toutes les actions effectuées par l’ingénieur du support technique lors de la connexion à votre compte client sont enregistrées et disponibles pour examen sur la page **Activité** > **Audit**.

Cliquez sur l'option **Accès opérateur AVS activé** pour activer ou désactiver l'accès.
