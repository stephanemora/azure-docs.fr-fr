---
title: Résolution des problèmes pour Datadog – Solutions partenaires Azure
description: Cet article fournit des informations sur la résolution des problèmes liés à Datadog sur Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563706"
---
# <a name="troubleshooting-datadog-on-azure"></a>Résolution des problèmes de Datadog sur Azure

Ce document contient des informations sur le dépannage de vos solutions qui utilisent Datadog.

## <a name="purchase-errors"></a>Erreurs d’achat

* L’achat échoue car aucune carte de crédit valide n’est connectée à l’abonnement Azure, ou car aucun mode de paiement n’est associé à l’abonnement.

  Utilisez un autre abonnement Azure. Vous pouvez également ajouter ou mettre à jour la carte de crédit ou le mode de paiement de l’abonnement. Pour plus d’informations, consultez [Mise à jour de la carte de crédit et du mode de paiement](../../cost-management-billing/manage/change-credit-card.md).

* L’abonnement EA n’autorise pas les achats de la Place de marché.

  Utilisez un autre abonnement. Ou vérifiez si votre abonnement EA est activé pour les achats de la Place de marché. Pour plus d’informations, voir [Activer les achats de la Place de marché](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Si ces options ne résolvent pas le problème, contactez le [support Datadog](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Impossible de créer une ressource Datadog

Pour configurer l’intégration de Datadog à Azure, vous devez disposer d’un accès **Propriétaire** sur l’abonnement Azure. Vérifiez que vous disposez de l’accès approprié avant de commencer l’installation.

## <a name="single-sign-on-errors"></a>Erreurs d’authentification unique

**Impossible d’enregistrer les paramètres d’authentification unique** : cette erreur se produit quand une autre application d’entreprise utilise l’identificateur SAML Datadog. Pour rechercher l’application qui l’utilise, sélectionnez **Modifier** dans la section Configuration SAML de base.

Pour résoudre ce problème, vous pouvez soit désactiver l’autre application, soit utiliser l’autre application en tant qu’application d’entreprise pour configurer l’authentification unique SAML avec Datadog. Si vous décidez d’utiliser l’autre application, assurez-vous que l’application possède les [paramètres requis](create.md#configure-single-sign-on).

**L’application n’apparaît pas dans la page de configuration de l’authentification unique** : commencez par rechercher l’ID de l’application. Si aucun résultat ne s’affiche, vérifiez les paramètres SAML de l’application. La grille affiche uniquement les applications dont les paramètres SAML sont corrects. 

L’URL de l’identificateur doit être `https://us3.datadoghq.com/account/saml/metadata.xml`.

L’URL de réponse doit être `https://us3.datadoghq.com/account/saml/assertion`.

L’image suivante montre les valeurs correctes.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Vérifiez les paramètres SAML pour l’application Datadog dans AAD." border="true":::

**Les utilisateurs invités du locataire ne peuvent pas accéder à l’authentification unique** : certains utilisateurs ont deux adresses e-mail dans le portail Azure. En règle générale, l’une des adresses e-mail est le nom d’utilisateur principal (UPN) et l’autre adresse e-mail est une adresse secondaire.

Quand vous invitez un utilisateur, utilisez le nom d’utilisateur principal du locataire d’accueil. En utilisant le nom d’utilisateur principal, vous conservez l’adresse e-mail synchronisée pendant le processus d’authentification unique. Vous pouvez trouver le nom d’utilisateur principal en recherchant l’adresse e-mail en haut à droite sur le portail Azure de l’utilisateur.
  
## <a name="logs-not-being-emitted"></a>Journaux non émis

Seules les ressources répertoriées dans les catégories de journaux de ressources Azure Monitor émettent des journaux vers Datadog. Pour vérifier si la ressource émet des journaux vers Datadog, accédez au paramètre de diagnostic Azure pour la ressource spécifique. Vérifiez qu’il existe un paramètre de diagnostic Datadog.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Paramètre de diagnostic Datadog sur la ressource Azure" border="true":::

## <a name="metrics-not-being-emitted"></a>Métriques non émises

Un rôle de **Lecteur d’analyse** est attribué à la ressource Datadog dans l’abonnement Azure approprié. Ce rôle permet à la ressource Datadog de collecter les métriques et de les envoyer à Datadog.

Pour vérifier que la ressource dispose de l’attribution de rôle appropriée, ouvrez le portail Azure et sélectionnez l’abonnement. Dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)** . Recherchez le nom de la ressource Datadog. Vérifiez que le rôle de **Lecteur d’analyse** est attribué à la ressource Datadog, comme indiqué ci-dessous.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Attribution de rôle Datadog dans l’abonnement Azure" border="true":::

## <a name="datadog-agent-installation-fails"></a>Échec de l’installation de l’agent Datadog

L’intégration de Datadog dans Azure vous permet d’installer l’agent Datadog sur une machine virtuelle ou une instance App Service. Pour configurer l’agent Datadog, la clé API sélectionnée en tant que **Clé par défaut** dans l’écran des clés API est utilisée. Si aucune clé par défaut n’est sélectionnée, l’installation de l’agent Datadog échoue.

Si l’agent Datadog a été configuré avec une clé incorrecte, accédez à l’écran des clés API et modifiez la **Clé par défaut**. Vous devez désinstaller l’agent Datadog et le réinstaller pour configurer la machine virtuelle avec les nouvelles clés API.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [gestion de votre instance](manage.md) Datadog.
