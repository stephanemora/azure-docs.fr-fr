---
title: Pack de détection de la sécurité avec Azure Application Insights
description: Effectuez le monitoring des applications avec Azure Application Insights et la détection intelligente pour détecter les éventuels problèmes de sécurité.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 2db677b5e08c7b890f66dfa1d5cc227da4dd8a34
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536773"
---
# <a name="application-security-detection-pack-preview"></a>Pack de détection de la sécurité des applications (préversion)

La détection intelligente analyse automatiquement les données de télémétrie générées par votre application et détecte les problèmes potentiels de sécurité. Cela vous permet d’identifier les problèmes de sécurité potentiels. Vous pouvez atténuer ces problèmes en corrigeant l’application ou en adoptant les mesures de sécurité nécessaires.

Cette fonctionnalité ne requiert aucune autre configuration spéciale que la [configuration de l’envoi des données de télémétrie par l’application](./usage-overview.md).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quand reçoit-on ce type de notification de détection intelligente ?
Trois types de problèmes de sécurité sont détectés :
1. Accès à l’URL non sécurisé : une URL de l’application est accessible à la fois suivant le protocole HTTP et suivant le protocole HTTPS. En règle générale, une URL qui accepte les requêtes HTTPS ne doit pas autoriser les requêtes HTTP. Cette détection peut être le signe d’un problème de sécurité ou d’un bogue dans l’application.
2. Formulaire non sécurisé : un formulaire (ou une autre requête « POST ») de l’application utilise le protocole HTTP au lieu de HTTPS. Cela peut compromettre les données utilisateur envoyées par le biais du formulaire.
3. Activité utilisateur suspecte : le même utilisateur accède à l’application à partir de plusieurs pays ou régions, à peu près en même temps. par exemple, de l’Espagne et des États-Unis au cours de la même heure. Cette détection indique une tentative potentiellement malveillante d’accès à l’application.

## <a name="does-my-app-definitely-have-a-security-issue"></a>L’application présente-t-elle nécessairement un problème de sécurité ?
Une notification ne signifie pas nécessairement que l’application présente réellement un problème de sécurité. Dans de nombreux cas, la détection d’un des scénarios ci-dessus peut indiquer un problème de sécurité. Dans d’autres cas, il se peut qu’elle ait une justification naturelle pour l’entreprise, et qu’elle puisse être ignorée.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Comment corriger la détection « Accès à l’URL non sécurisé » ?
1. **Triage.** La notification indique le nombre d’utilisateurs ayant accédé à des URL non sécurisées, ainsi que l’URL la plus affectée par des accès non sécurisés. Ces informations vous permettent d’attribuer une priorité au problème.
3. **Portée.** Quelle est la proportion d’utilisateurs ayant accédé à des URL non sécurisées ? Combien d’URL ont été affectées ? Ces informations peuvent être obtenues dans la notification.
4. **Diagnostic**. La détection donne la liste des requêtes non sécurisées, ainsi que des URL et des utilisateurs affectés, pour permettre de diagnostiquer le problème.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Comment corriger la détection « Formulaire non sécurisé » ?
1. **Triage.** La notification indique le nombre de formulaires non sécurisés et le nombre d’utilisateurs dont les données sont potentiellement compromises. Ces informations vous permettent d’attribuer une priorité au problème.
2. **Portée.** Quel est le formulaire qui a été impliqué dans le plus grand nombre de transmissions non sécurisées, et quelle est la distribution des transmissions non sécurisées au fil du temps ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection donne la liste des formulaires non sécurisés, ainsi que la répartition des transmissions non sécurisées pour chaque formulaire, pour permettre de diagnostiquer le problème.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Comment corriger la détection « Activité utilisateur suspecte » ?
1. **Triage.** La notification indique le nombre d’utilisateurs différents qui ont présenté le comportement suspect. Ces informations vous permettent d’attribuer une priorité au problème.
2. **Portée.** De quels pays ou régions les requêtes suspectes provenaient-elles ? Quel est l’utilisateur le plus suspect ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. La détection donne la liste des utilisateurs suspects et des pays ou régions de chacun, pour permettre de diagnostiquer le problème.
