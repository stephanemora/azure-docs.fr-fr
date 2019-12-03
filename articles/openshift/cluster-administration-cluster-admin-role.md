---
title: Rôle d'administrateur de cluster Azure Red Hat OpenShift | Microsoft Docs
description: Attribution et utilisation du rôle d’administrateur de cluster Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539268"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rôle d'administrateur de client Azure Red Hat OpenShift

Vous êtes l’administrateur d’un cluster Azure Red Hat OpenShift. Votre compte dispose de plus en plus d’autorisations et d’un accès à tous les projets créés par l’utilisateur.

Quand le rôle d’autorisation customer-admin-cluster est lié à votre compte, celui-ci peut automatiquement gérer un projet.

> [!Note] 
> Le rôle de cluster customer-admin-cluster est différent de celui de cluster-admin.


Par exemple, vous pouvez exécuter des actions associées à un ensemble de verbes (`create`) pour agir sur un ensemble de noms de ressources (`templates`). Pour afficher les détails de ces rôles et de leurs ensembles de verbes et de ressources, exécutez la commande suivante :

`$ oc get clusterroles customer-admin-cluster -o yaml`

Les noms de verbes ne sont pas nécessairement tous mappés directement aux commandes `oc`. Ils s’assimilent plus généralement aux types d’opérations CLI que vous pouvez effectuer. 

Par exemple, le fait de disposer du verbe `list` signifie que vous pouvez afficher la liste de tous les objets d’un nom de ressource (`oc get`). Le verbe `get` signifie que vous pouvez afficher les détails d’un objet spécifique si vous en connaissez le nom (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Configurer le rôle d’administrateur de client

Vous pouvez configurer le rôle de cluster uniquement au moment de créer le cluster en fournissant l’indicateur `--customer-admin-group-id`. Pour savoir comment configurer Azure Active Directory et le groupe Administrateurs, consultez [Intégration d’Azure Active Directory pour Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

Configurez le rôle de cluster customer-admin-cluster :
> [!div class="nextstepaction"]
> [Intégration d'Azure Active Directory pour Azure Red Hat OpenShift](howto-aad-app-configuration.md)
