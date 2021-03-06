---
title: Profiler des applications web s’exécutant sur une machine virtuelle Azure avec Application Insights Profiler | Microsoft Docs
description: Profilez des applications web sur une machine virtuelle Azure avec Application Insights Profiler.
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
ms.openlocfilehash: 152632c55fc21d2b49f6dfd8ae734833ea870898
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978364"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profiler des applications web s’exécutant sur une machine virtuelle Azure ou un groupe de machines virtuelles identiques avec Application Insights Profiler
Vous pouvez également déployer Application Insights Profiler sur ces services :
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Déployer Profiler sur une machine virtuelle ou un groupe de machines virtuelles identiques
Cette page décrit les étapes nécessaires pour exécuter Application Insights Profiler sur votre machine virtuelle ou groupe de machines virtuelles identiques Azure. Application Insights Profiler est installé avec l’extension Windows Azure Diagnostics pour les machines virtuelles. Vous devez configurer l’extension pour exécuter le profileur et intégrer le SDK Application Insights à votre application afin d’obtenir des profils pour vos applications web exécutées sur une machine virtuelle.

1. Ajoutez le SDK Application Insights à votre [application ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) ou [application .NET](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) ordinaire. Vous devez envoyer des données de télémétrie sur les requêtes à Application Insights pour voir les profils pour vos requêtes.
1. Installez l’extension Windows Azure Diagnostics sur votre machine virtuelle. Pour obtenir des exemples complets de modèles Resource Manager, consultez :  
    * [Machine virtuelle](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Groupe de machines virtuelles identiques](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. Déployez la définition du déploiement d’environnement modifiée.  

   Pour appliquer les modifications, vous devez généralement effectuer un déploiement du modèle complet ou une publication basée sur les services cloud par le biais des applets de commande PowerShell ou Visual Studio.  

   Les commandes PowerShell suivantes offrent une autre approche pour des machines virtuelles existantes qui touche uniquement l’extension Azure Diagnostics :  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Si l’application souhaitée s’exécute avec [IIS](https://www.microsoft.com/web/downloads/platform.aspx), activez la fonctionnalité Windows `IIS Http Tracing` en procédant comme suit :  

   a. Établissez l’accès à distance à l’environnement, puis utilisez la fenêtre [Ajouter des fonctionnalités Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ou exécutez la commande suivante dans PowerShell (en tant qu’administrateur) :  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Si l’établissement de l’accès à distance pose problème, vous pouvez utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) pour exécuter la commande suivante :  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Déployez votre application.

## <a name="can-profiler-run-on-on-premises-servers"></a>Le profileur peut-il être exécuté sur des serveurs locaux ?
Nous n’envisageons pas de prendre en charge Application Insights Profiler pour les serveurs locaux. 

## <a name="next-steps"></a>Étapes suivantes

- Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
- Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) dans le portail Azure.
- Consultez l’article [Résolution des problèmes du profileur](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json) pour obtenir de l’aide pour résoudre les problèmes rencontrés avec le profileur.
