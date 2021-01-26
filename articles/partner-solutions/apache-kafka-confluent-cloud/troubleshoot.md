---
title: Résolution des problèmes d’Apache Kafka pour Confluent Cloud – Solutions partenaires Azure
description: Cet article fournit des informations sur la résolution des problèmes et la foire aux questions (FAQ) concernant Confluent Cloud sur Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253221"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Résolution des problèmes d’Apache Kafka pour les solutions Confluent Cloud

Ce document contient des informations sur le dépannage de vos solutions qui utilisent Apache Kafka pour Confluent Cloud.

Si vous ne trouvez pas de réponse ou que vous ne parvenez pas à résoudre un problème, [créez une demande via le portail Azure](manage.md#get-support) ou contactez le [support de Confluent](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Impossible de trouver une offre sur le marketplace

Pour trouver l’offre sur Place de marché Azure, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.
1. Recherchez _Apache Kafka sur Confluent Cloud_.
1. Sélectionnez la vignette de l’application.

Si l’offre n’est pas affichée, contactez le [support de Confluent](https://support.confluent.io). Votre ID de locataire Azure Active Directory doit figurer dans la liste des locataires autorisés. Pour savoir comment trouver votre ID de locataire, consultez [Comment trouver votre ID de locataire Azure Active Directory](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="conflict-error"></a>Erreur de conflit

Si vous vous êtes déjà inscrit à Confluent Cloud, vous devez utiliser une nouvelle adresse e-mail pour créer une autre ressource d’organisation Confluent Cloud. Si vous utilisez une adresse e-mail inscrite précédemment, vous obtiendrez une erreur **Conflit**. Réinscrivez-vous, mais cette fois-ci avec une nouvelle adresse e-mail.

## <a name="deploymentfailed-error"></a>Erreur DeploymentFailed

Si vous recevez une erreur **DeploymentFailed**, vérifiez l’état de votre abonnement Azure. Assurez-vous qu’il n’est pas suspendu et qu’il n’existe aucun problème de facturation.

## <a name="resource-isnt-displayed"></a>La ressource n’est pas affichée

Si les panneaux **Vue d’ensemble** ou **Supprimer** pour Confluent Cloud ne sont pas affichées dans le portail, essayez d’actualiser la page. Il peut s’agir d’un problème intermittent du portail. Si cela ne fonctionne pas, contactez le [support de Confluent](https://support.confluent.io).

Si la ressource Confluent Cloud est introuvable dans la liste **Toutes les ressources** d’Azure, contactez le [support de Confluent](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>La création de ressources prend beaucoup de temps

Si le processus de déploiement prend plus de trois heures, contactez le support.

Si le déploiement échoue et que la ressource Confluent Cloud a l’état `Failed`, supprimez la ressource. Après la suppression, réessayez de créer la ressource.

## <a name="offer-plan-doesnt-load"></a>Le plan de l’offre ne se charge pas

Il peut s’agir d’un problème intermittent du portail Azure. Réessayez de déployer l’offre.

## <a name="unable-to-delete"></a>Impossible de supprimer

Si vous ne parvenez pas à supprimer des ressources Confluent, vérifiez que vous disposez des autorisations nécessaires pour supprimer la ressource. Vous devez être autorisé à effectuer des actions Microsoft.Confluent/*/Delete. Pour obtenir des informations sur l’affichage des autorisations, consultez [Répertorier les attributions de rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-list-portal.md).

Si vous disposez des autorisations appropriées, mais que vous ne pouvez toujours pas supprimer la ressource, contactez le [support de Confluent](https://support.confluent.io). Cette condition peut être liée à la stratégie de rétention de Confluent. Le support de Confluent peut supprimer l’organisation et l’adresse e-mail pour vous.

## <a name="unable-to-use-single-sign-on"></a>Impossible d’utiliser l’authentification unique

Si l’authentification unique ne fonctionne pas pour le portail SaaS de Confluent Cloud, vérifiez que vous utilisez la bonne adresse e-mail Azure Active Directory. Vous devez également avoir consenti à autoriser l’accès pour le portail SaaS de Confluent Cloud. Pour plus d’informations, consultez l’[aide relative à l’authentification unique](manage.md#single-sign-on).

Si le problème persiste, contactez le [support de Confluent](https://support.confluent.io).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [gestion de votre instance](manage.md) d’Apache Kafka pour Confluent Cloud.
