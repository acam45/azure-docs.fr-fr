---
title: Profiler des applications web Azure en production avec Application Insights | Microsoft Docs
description: Profiler des applications web en production avec Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 9aae08aa5906f341a890ac15e30d2863109d83a2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140006"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profiler des applications web Azure en production avec Application Insights

Profiler fonctionne actuellement pour les applications web ASP.NET et ASP.NET Core s’exécutant sur Web Apps. L’utilisation de Profiler nécessite au minimum le niveau de service De base.

## <a id="installation"></a> Activer Profiler pour votre application Web Apps
Pour activer Profiler pour une application web, suivez les instructions ci-dessous. Si vous exécutez un autre type de service Azure, voici les instructions nécessaires pour activer Profiler sur d’autres plateformes prises en charge :
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Applications Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler est installé avec l’extension de site Application Insights. Vous devez installer l’extension de site et la configurer pour obtenir des profils pour vos applications web Azure. Une fois que vous avez déployé une application web, même si vous avez inclus le SDK App Insights dans le code source, suivez les étapes ci-dessous pour activer le profileur.

1. Accédez au volet **App Services** dans le portail Azure.
1. Accédez au volet **Paramètres > Surveillance**.

   ![Activer App Insights sur le portail App Services](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Suivez les instructions dans le volet pour créer une nouvelle ressource, ou sélectionnez une ressource Application Insights existante pour surveiller votre application web. Acceptez toutes les options par défaut. **Diagnostics de niveau code** est activé par défaut et active Profiler.

   ![Ajouter l’extension de site Application Insights][Enablement UI]

1. Profiler est maintenant installé avec l’extension de site Application Insights et il est activé avec un paramètre d’application App Services.

    ![Paramètre d’application pour Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Désactiver Profiler

Pour arrêter ou redémarrer Profiler pour une instance d’application web individuelle, accédez à la ressource Web Apps sous **Tâches web**. Pour supprimer Profiler, accédez à **Extensions**.

![Désactiver Profiler pour une tâche web][disable-profiler-webjob]

Nous vous recommandons d’activer, dès que possible, Profiler sur toutes vos applications web afin de découvrir d’éventuels problèmes de performance.

Si vous utilisez WebDeploy pour déployer des modifications sur votre application web, veillez à ne pas supprimer le dossier App_Data lors du déploiement. Dans le cas contraire, les fichiers de l’extension de Profiler seront supprimés la prochaine fois que vous déploierez l’application web dans Azure.



## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

