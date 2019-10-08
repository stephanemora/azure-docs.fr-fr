---
title: Rôle d'administrateur de cluster Azure Red Hat OpenShift | Microsoft Docs
description: Attribution et utilisation du rôle d'administrateur de cluster Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71712836"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rôle d'administrateur de client Azure Red Hat OpenShift

En tant qu'administrateur de cluster ARO (Azure Red Hat OpenShift) d'un cluster OpenShift, votre compte dispose d'autorisations étendues et d'un accès à tous les projets créés par les utilisateurs.

Lorsque le rôle d'autorisation osa-customer-admins est lié à votre compte, celui-ci peut automatiquement gérer un projet.

> [!Note] 
> Le rôle de cluster osa-customer-admin est différent de celui de cluster-admin.


Par exemple, vous pouvez exécuter des actions associées à un ensemble de verbes (`create`) pour agir sur un ensemble de noms de ressources (`templates`). Pour afficher les détails de ces rôles et de leurs ensembles de verbes et de ressources, exécutez la commande suivante :

`$ oc describe clusterrole/osa-customer-admin`

Les noms des verbes ne correspondent pas nécessairement tous directement aux commandes oc, mais plus généralement aux types d'opérations CLI que vous pouvez effectuer. Par exemple, le verbe `list` signifie que vous pouvez afficher la liste de tous les objets d'un nom de ressource donné (`oc get`), tandis que le verbe `get` signifie que vous pouvez afficher les détails d'un objet spécifique si vous connaissez son nom (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Configurer le rôle d'administrateur de client

Le rôle d'administrateur de client ne peut être configuré que lors de la création du cluster en fournissant l'indicateur `--customer-admin-group-id`. Pour configurer Azure Active Directory et le groupe Administrateurs, suivez les instructions ci-dessous : [Intégration d'Azure Active Directory pour Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Étapes suivantes

Configurer le rôle osa-customer-admin :
> [!div class="nextstepaction"]
> [Intégration d'Azure Active Directory pour Azure Red Hat OpenShift](howto-aad-app-configuration.md)
