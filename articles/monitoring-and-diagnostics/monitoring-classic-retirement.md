---
title: La gestion des alertes et la supervision unifiées dans Azure Monitor remplace la gestion des alertes et la supervision classiques
description: Vue d’ensemble de la mise hors service des services et des fonctionnalités de supervision classique, présentée précédemment dans le portail Azure sous Alertes (classiques). La fonctionnalité de gestion des alertes et de la supervision classiques inclut les alertes de métriques classiques pour les ressources Azure, les alertes de métriques classiques pour Application Insights, les alertes de test web classiques pour Application Insights, la métrique personnalisée classique basées sur des alertes pour Application Insights et les alertes classiques pour Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 589aae8321d2c081f09ed46d9def2229d3973ffd
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613194"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>La gestion des alertes et la supervision unifiées dans Azure Monitor remplace la gestion des alertes et la supervision classiques

Azure Monitor est désormais devenu un service unifié de supervision de la pile complète qui prend maintenant en charge les fonctionnalités « Métriques unifiées » et « Alertes unifiées » pour l’ensemble des ressources. Pour plus d’informations, consultez notre [billet de blog sur la nouvelle instance Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). La nouvelle plateforme de supervision et de gestion des alertes Azure a été conçue pour être plus rapide, plus intelligente et extensible, permettant ainsi de suivre le rythme de l’expansion croissante du cloud computing et conformément à la philosophie du cloud intelligent Microsoft. 

La nouvelle plateforme de supervision et de gestion des alertes Azure étant en place, nous allons mettre hors service la plateforme de supervision et de gestion des alertes « classique », hébergée dans la section *Afficher les alertes classiques* des alertes Azure, et qui sera dépréciée d’ici juin 2019.

 ![Alerte classique dans le portail Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Nous vous encourageons à l’utiliser et à recréer vos alertes sur la nouvelle plateforme. Pour les clients ayant un grand nombre d’alertes, nous nous employons à fournir un moyen automatisé de déplacer les alertes classiques existantes vers le nouveau système d’alertes sans interruption de service ou coûts supplémentaires.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Métriques et alertes unifiées dans Application Insights

La nouvelle plateforme de métrique d’Azure Monitor va maintenant dynamiser la supervision provenant d’Application Insights. Ce déplacement signifie qu’Application Insights se raccrochera aux groupes d’actions, ce qui offrira bien plus d’options que les simples appels d’e-mail et webhook précédents. Les alertes peuvent maintenant déclencher des appels vocaux, Azure Functions, Logic Apps, des SMS et des outils ITSM comme ServiceNow et les runbooks Automation. Avec la supervision et la gestion des alertes en quasi temps réel, la nouvelle plateforme permet aux utilisateurs Application Insights de tirer parti de la même technologie en exploitant la supervision d’autres ressources Azure et en soutenant la supervision de produits Microsoft.

Les nouvelles supervision et gestion des alertes unifiées pour Application Insights engloberont les éléments suivants :

- **Métriques de plateforme Application Insights** : fournit des métriques prédéfinies populaires à partir du produit Application Insights. Pour plus d’informations, consultez cet article relatif à l’utilisation de [métriques de plateforme pour Application Insights sur la nouvelle instance Azure Monitor](../application-insights/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test web et de disponibilité d’Application Insights** : vous offre la possibilité d’évaluer la réactivité et la disponibilité de votre serveur ou application web. Pour plus d’informations, consultez cet article relatif à l’utilisation de [tests de disponibilité et d’alertes pour Application Insights sur la nouvelle instance Azure Monitor](../application-insights/app-insights-monitor-web-app-availability.md).
- **Métriques personnalisées Application Insights** : vous permet de définir et d’émettre vos propres métriques pour la supervision et les alertes. Pour plus d’informations, consultez cet article relatif à l’utilisation d’une [métrique personnalisée pour Application Insights sur la nouvelle instance Azure Monitor](../application-insights/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalies des échecs Application Insights (partie de la Détection intelligente)**  : vous avertit automatiquement en quasi temps réel si une augmentation anormale du taux des requêtes HTTP en échec ou des appels de dépendance est détectée dans votre application web. La fonctionnalité Anomalies des échecs Application Insights (partie de la Détection intelligente) sera bientôt disponible comme partie de la nouvelle instance Azure Monitor. Nous mettrons alors à jour ce document dans les prochains mois avec des liens sur la prochaine itération quand elle est sera développée.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>Métriques et alertes unifiées sur d’autres ressources Azure

La nouvelle génération de gestion des alertes et la supervision multidimensionnelle pour les ressources Azure sont disponibles depuis mars 2018. La nouvelle plateforme de métrique et la nouvelle gestion des alertes sont maintenant plus rapides avec des fonctionnalités en temps quasi réel. Plus important encore, les alertes de la nouvelle plateforme de métrique fournissent plus de granularité, car cette dernière inclut l’option des dimensions, qui vous permettent de segmenter et de filtrer une combinaison de valeurs, une condition ou une opération spécifique. Comme toutes les alertes dans la nouvelle instance Azure Monitor, les alertes de métrique plus récentes sont plus extensibles avec l’utilisation d’ActionGroups, ce qui permet d’étendre les notifications au-delà des e-mails ou des webhooks en utilisant des SMS, des appels vocaux, Azure Functions, des runbooks Automation, et bien plus encore.
Les métriques plus récentes pour les ressources Azure sont disponibles sous les formes suivantes :

- **Métriques standard de la plateforme Azure Monitor**, ce qui fournit des métriques préremplies populaires à partir de divers produits et services Azure. Pour plus d’informations, consultez cet article relatif aux [Métriques prises en charge sur Azure Monitor](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) et aux [Alertes pour les métriques prises en charge sur Azure Monitor](alert-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Métriques personnalisées Azure Monitor**, ce qui fournit des métriques provenant de sources contrôlées par l’utilisateur, notamment l’agent de diagnostics Azure. Pour plus d’informations, consultez cet article relatif aux [métriques personnalisées dans Azure Monitor](metrics-custom-overview.md). En utilisant des métriques personnalisées, vous pouvez également publier des métriques collectées par [l’agent de diagnostics Microsoft Azure](metrics-store-custom-guestos-resource-manager-vm.md) et [l’agent InfluxData Telegraf](metrics-store-custom-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Mise hors service de la plateforme de supervision et de gestion des alertes classiques

Comme indiqué précédemment, la plateforme de supervision et de gestion des alertes classiques actuellement accessible à partir de la [section Alertes (classiques)](monitoring-overview-alerts-classic.md) du portail va être mise hors service dans les prochains mois, car elle a été remplacée par un nouveau système.
Les anciennes supervision et gestion des alertes classiques seront mises hors service le 30 juin 2019. Les API, l’interface du portail Azure et les services associés qu’elles contiennent seront également supprimés. Plus précisément, les fonctionnalités suivantes seront dépréciées :

- Les anciennes métriques et alertes (classiques) pour les ressources Azure telles que disponibles par le biais de la [section Alertes (classiques)](monitoring-overview-alerts-classic.md) du portail Azure et accessibles en tant que ressource [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Les anciennes métriques personnalisées et de plateforme (classiques) pour Application Insights ainsi que la gestion des alertes définies dessus, telles que disponibles par le biais de la [section Alertes (classiques)](monitoring-overview-alerts-classic.md) du portail Azure et accessibles en tant que ressource [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- L’ancienne alerte Anomalies d’échecs (classique) actuellement disponible sous forme de [détection intelligente au sein d’Application Insights](../application-insights/app-insights-proactive-diagnostics.md) sur le portail Azure, avec des alertes configurées affichées dans la [section Alertes (classiques)](monitoring-overview-alerts-classic.md) du portail Azure

Tous les systèmes de supervision et de gestion des alertes classiques, dont [l’API](https://msdn.microsoft.com/library/azure/dn931945.aspx) correspondante, [PowerShell](insights-alerts-powershell.md), [CLI](insights-alerts-command-line-interface.md), la page du portail Azure et le [modèle de ressource](monitoring-enable-alerts-using-template.md) resteront utilisables jusqu’en juin 2019. Après cette date, le service de supervision et d’alertes classiques sera supprimé et ne sera plus disponibles pour être utilisé. Toutefois, toutes les règles d’alerte qui continueront d’exister dans le service Alertes (classiques) après le mois de juin 2019 continueront à s’exécuter, mais elles ne pourront pas être modifiées.

Toutes les alertes restant dans la plateforme de supervision et de gestion des alertes classiques après le mois de juin 2019 seront automatiquement migrées par Microsoft vers leur équivalent dans la nouvelle plateforme Azure Monitor en juillet 2019. Le processus sera transparent et sans interruption de service, et garantira que les clients ne subiront aucune perte de couverture de la supervision.

Nous vous fournirons bientôt les outils vous permettant de migrer délibérément vos alertes de la [section Alertes (classiques)](monitoring-overview-alerts-classic.md) du portail Azure vers les nouvelles alertes Azure. Toutes les règles configurées dans Alertes (classiques) qui sont migrées vers la nouvelle instance Azure Monitor resteront gratuites et ne seront pas facturées. Les règles d’alerte classique migrées ne seront soumises à aucun frais pour l’envoi (push) des notifications par e-mail, webhook ou LogicApp. Toutefois, l’utilisation de nouveaux types de notification ou d’action (par exemple, les SMS, les appels vocaux, l’intégration ITSM, etc.) sera soumise à facturation, qu’ils soient ajoutés à une alerte migrée ou nouvelle. Pour plus d’informations, consultez [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

De plus, les éléments suivants seront facturables conformément aux [tarifs Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) :

- Toute règle d’alerte (non migrée) créée au-delà des unités gratuites, sur la nouvelle plateforme Azure Monitor
- Toutes les données reçues et conservées au-delà des unités gratuites incluses par Azure Monitor
- Tous les tests web multiples exécutés par Application Insights
- Toutes les métriques personnalisées stockées au-delà des unités gratuites incluses dans Azure Monitor

Cet article sera continuellement mis à jour avec des liens et des informations détaillées concernant la nouvelle fonctionnalité de supervision et de gestion des alertes Azure, ainsi que la disponibilité des outils permettant d’aider les utilisateurs à adopter la nouvelle plateforme Azure Monitor.


## <a name="next-steps"></a>Étapes suivantes

* Découvrir la [nouvelle instance Azure Monitor](../azure-monitor/overview.md).
* Découvrir les nouvelles [Alertes Azure](monitoring-overview-alerts.md).
