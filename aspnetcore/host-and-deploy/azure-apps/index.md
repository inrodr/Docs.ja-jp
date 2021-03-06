---
title: Azure App Service での ASP.NET Core のホスト
author: guardrex
description: 役に立つリソースへのリンクを使用して Azure App Service で ASP.NET Core アプリをホストする方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 08/29/2018
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: bc2a686c5ddc44fded135c9eed5caf676218773a
ms.sourcegitcommit: ecf2cd4e0613569025b28e12de3baa21d86d4258
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43312071"
---
# <a name="host-aspnet-core-on-azure-app-service"></a>Azure App Service での ASP.NET Core のホスト

[Azure App Service](https://azure.microsoft.com/services/app-service/) は ASP.NET Core を含む Web アプリをホストするための [Microsoft クラウド コンピューティング プラットフォーム サービス](https://azure.microsoft.com/)です。

## <a name="useful-resources"></a>役に立つリソース

Azure の「[Web Apps のドキュメント](/azure/app-service/)」は、Azure アプリのドキュメント、チュートリアル、サンプル、ハウツー ガイド、その他のリソースのホームです。 ASP.NET Core アプリのホスティングに関連する次の 2 つのチュートリアルは特に重要です。

[クイック スタート: Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)  
Visual Studio を使用して ASP.NET Core Web アプリを作成し、Windows の Azure App Service に配置します。

[クイック スタート: App Service on Linux での .NET Core Web アプリの作成](/azure/app-service/containers/quickstart-dotnetcore)  
コマンド ラインを使用して ASP.NET Core Web アプリを作成し、Linux の Azure App Service に配置します。

ASP.NET Core のドキュメントでは、次の記事を参照できます。

[Visual Studio による Azure への公開](xref:tutorials/publish-to-azure-webapp-using-vs)  
Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。

[CLI ツールによる Azure への公開](xref:tutorials/publish-to-azure-webapp-using-cli)  
Git コマンド ライン クライアントを使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。

[Visual Studio と Git による Azure への継続的配置](xref:host-and-deploy/azure-apps/azure-continuous-deployment)  
Visual Studio で ASP.NET Core Web アプリを作成し、それを Azure App Service に配置する方法について説明します。Git を利用し、継続的に配置します。

[VSTS による Azure への継続的配置](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure)  
ASP.NET Core アプリ用に CI ビルドを設定し、Azure App Service に継続的配置リリースを作成します。

[Azure Web アプリのサンドボックス](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Azure アプリのプラットフォームで適用される Azure App Service ランタイム実行の制限事項について説明します。

::: moniker range=">= aspnetcore-2.0"

## <a name="application-configuration"></a>アプリケーション構成

ASP.NET Core 2.0 以降では、次の NuGet パッケージで Azure App Service にデプロイされたアプリの自動ログ記録機能が提供されます。

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) は [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) を使用して Azure App Service と ASP.NET Core の Light-Up 統合を提供します。 追加されるログ記録機能は `Microsoft.AspNetCore.AzureAppServicesIntegration` パッケージによって提供されます。
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) は [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) を実行して、`Microsoft.Extensions.Logging.AzureAppServices` パッケージに Azure App Service 診断ログ記録プロバイダーを追加します。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) はロガー実装を提供することで、Azure App Service 診断ログとログ ストリーミング機能をサポートします。

.NET Core を対象とし、[Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)を参照している場合、パッケージが既に含まれています。 このパッケージは、新しい [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。 .NET Framework を対象とし、`Microsoft.AspNetCore.App` メタパッケージを参照している場合は、個々のログ記録パッケージを参照します。

::: moniker-end

## <a name="override-app-configuration-using-the-azure-portal"></a>Azure Portal を使用してアプリの構成をオーバーライドする

**[アプリケーションの設定]** ブレードの **[アプリの設定]** 領域を使用すると、アプリの環境変数を設定できます。 環境変数は、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)で使用できます。

アプリが [Web ホスト](xref:fundamentals/host/web-host)を使用し、[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) を使用してホストをビルドする場合、ホストを構成する環境変数では `ASPNETCORE_` プレフィックスが使用されます。 詳細については、<xref:fundamentals/host/web-host> および「[Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider)」(環境変数構成プロバイダー) をご覧ください。

アプリが[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合、環境変数は既定ではアプリの構成に読み込まれません。開発者が構成プロバイダーを追加する必要があります。 開発者は、構成プロバーダーを追加する際に環境変数のプレフィックスを決定します。 詳細については、<xref:fundamentals/host/generic-host> および「[Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider)」(環境変数構成プロバイダー) をご覧ください。

## <a name="proxy-server-and-load-balancer-scenarios"></a>プロキシ サーバーとロード バランサーのシナリオ

要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する IIS 統合ミドルウェアと、ASP.NET Core モジュールが構成されます。 追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。 詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="monitoring-and-logging"></a>監視およびログ記録

監視、ログ記録、トラブルシューティングに関する情報は、次の記事を参照してください。

[Azure App Service でアプリを監視する方法](/azure/app-service/web-sites-monitor)  
アプリと App Service プランに関するクォータとメトリックを確認する方法を説明します。

[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)  
HTTP 状態コード、失敗した要求、Web サーバー アクティビティの診断ログを有効にしてアクセスする方法を説明します。

[ASP.NET Core でのエラー処理の概要](xref:fundamentals/error-handling)  
ASP.NET Core アプリでエラーを処理するための一般的な手法について理解します。

[Azure App Service での ASP.NET Core のトラブルシューティング](xref:host-and-deploy/azure-apps/troubleshoot)  
ASP.NET Core アプリを使用した Azure App Service の配置に関する問題を診断する方法を説明します。

[Azure App Service および IIS と ASP.NET Core の一般的なエラーのリファレンス](xref:host-and-deploy/azure-iis-errors-reference)  
Azure App Service/IIS によってホストされるアプリの一般的な配置の構成エラーのトラブルシューティングに関するアドバイスを参照してください。

## <a name="data-protection-key-ring-and-deployment-slots"></a>データ保護キー リングとデプロイ スロット

[データ保護キー](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)は *%HOME%\ASP.NET\DataProtection-Keys* フォルダーに保存されます。 このフォルダーはネットワーク ストレージにバックアップされ、アプリをホストしているすべてのマシンで同期されています。 保存中のキーは保護されていません。 このフォルダーから、単一のデプロイ スロットのアプリのすべてのインスタンスにキー リングが提供されます。 ステージングや運用などの別のデプロイ スロットでは、キー リングが共有されません。

デプロイ スロットを切り替えると、データ保護を使用しているすべてのシステムが前のスロット内のキー リングを使用して格納されたデータを復号化できなくなります。 ASP.NET Cookie ミドルウェアは、その Cookie の保護にデータ保護を使用します。 これにより、ユーザーが標準の ASP.NET Cookie ミドルウェアを使用するアプリからサインアウトします。 スロットに依存しないキー リング ソリューションの場合、次のような外部キー リング プロバイダーを使用します。

* Azure Blob Storage
* Azure Key Vault
* SQL ストア
* Redis Cache

詳細については、「[キー記憶域プロバイダー](xref:security/data-protection/implementation/key-storage-providers)」を参照してください。

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>Azure App Service に ASP.NET Core プレビュー リリースを展開する

ASP.NET Core プレビュー アプリは、次の方法で Azure App Service に展開できます。

* [プレビュー サイト拡張機能をインストールする](#install-the-preview-site-extension)
<!-- * [Deploy the app self-contained](#deploy-the-app-self-contained) -->
* [コンテナー用の Web アプリで Docker を使用する](#use-docker-with-web-apps-for-containers)

### <a name="install-the-preview-site-extension"></a>プレビュー サイト拡張機能をインストールする

プレビュー サイト拡張機能の使用に関する問題が発生した場合は、[GitHub](https://github.com/aspnet/azureintegration/issues/new) に問題を投稿してください。

1. Azure Portal から [App Service] ブレードに移動します。
1. Web アプリを選択します。
1. 検索ボックスに「ex」と入力するか、管理セクションの一覧を **[開発ツール]** までスクロール ダウンします。
1. **[開発ツール]** > 、**[拡張機能]** の順に選択します。
1. **[追加]** を選びます。
1. **[ASP.NET Core &lt;x.y&gt; (x86) ランタイム]** 拡張機能を一覧から選択します。`<x.y>` は ASP.NET Core のプレビュー バージョンです (**ASP.NET Core 2.2 (x86) ランタイム**など)。 x86 ランタイムは、ASP.NET Core モジュールによるプロセス外ホスティングに依存する[フレームワーク依存のデプロイ](/dotnet/core/deploying/#framework-dependent-deployments-fdd)に適しています。
1. **[OK]** を選んで法的条項に同意します。
1. **[OK]** を選択し、拡張機能をインストールします。

操作が完了すると、最新の .NET Core プレビューがインストールされます。 次のようにしてインストールを検証します。

1. **[開発ツール]** の下の **[高度なツール]** を選択します。
1. **[高度なツール]** ブレードの下の **[Go]** を選択します。
1. **[デバッグ コンソール]** > **[PowerShell]** のメニュー項目を選択します。
1. PowerShell のプロンプトで次のコマンドを実行します。 コマンドの `<x.y>` を ASP.NET Core ランタイム バージョンに置き換えます。

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.<x.y>.x86\
   ```
   インストールされているプレビュー ランタイムが ASP.NET Core 2.2 向けである場合、コマンドは次のようになります。
   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.2.2.x86\
   ```
   x64 プレビューのランタイムがインストールされていると、コマンドで `True` が返されます。

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> App Services アプリのプラットフォーム アーキテクチャ (x86/x64) が、A シリーズ計算、またはより優れたホスティング階層でホストされているアプリの **[全般設定]** の下の **[アプリケーション設定]** に設定されます。 アプリがインプロセス モードで実行されていて、プラットフォーム アーキテクチャが 64 ビット (x64) 向けに構成されている場合は、ASP.NET Core モジュールで 64 ビット プレビュー ランタイム (ある場合) が使用されます。 **ASP.NET Core &lt;x.y&gt; (x64) ランタイム**拡張機能 (たとえば、**ASP.NET Core 2.2 (x64) ランタイム**) をインストールします。
>
> x64 プレビュー ランタイムがインストールされたら、Kudu PowerShell コマンド ウィンドウで次のコマンドを実行して、インストールを確認します。 コマンドの `<x.y>` を ASP.NET Core ランタイム バージョンに置き換えます。
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.<x.y>.x64\
> ```
> インストールされているプレビュー ランタイムが ASP.NET Core 2.2 向けである場合、コマンドは次のようになります。
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.2.2.x64\
> ```
> x64 プレビューのランタイムがインストールされていると、コマンドで `True` が返されます。

::: moniker-end

> [!NOTE]
> **ASP.NET Core 拡張機能**によって、たとえば Azure のログ記録など、Azure App Services での ASP.NET Core の追加機能が有効になります。 この拡張機能は、Visual Studio からデプロイするときに自動的にインストールされます。 拡張機能がインストールされていない場合は、アプリにインストールします。

**ARM テンプレートでプレビュー サイト拡張機能を使用する**

ARM テンプレートを使用してアプリを作成し、展開する場合は、リソースの種類として `siteextensions` を使用してサイト拡張機能を Web アプリに追加することができます。 例:

[!code-json[Main](index/sample/arm.json?highlight=2)]

<!--
### Deploy the app self-contained

A [self-contained app](/dotnet/core/deploying/#self-contained-deployments-scd) can be deployed that carries the preview runtime in the deployment. When deploying a self-contained app:

* The site doesn't need to be prepared.
* The app must be published differently than when publishing for a framework-dependent deployment with the shared runtime and host on the server.

Self-contained apps are an option for all ASP.NET Core apps.
-->

### <a name="use-docker-with-web-apps-for-containers"></a>コンテナー用の Web アプリで Docker を使用する

[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) には最新のプレビュー Docker イメージが含まれています。 イメージを基本イメージとして使用できます。 通常は、イメージを使用して、Web App for Containers に展開します。

## <a name="additional-resources"></a>その他の技術情報

* [Web Apps の概要 (5 分間の概要ビデオ)](/azure/app-service/app-service-web-overview)
* [Azure App Service: .NET アプリのホスティングに最適な場所 (55 分間の概要ビデオ)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Azure App Service の診断の概要](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Windows Server の Azure App Service では[インターネット インフォメーション サービス (IIS)](https://www.iis.net/) が使用されます。 次のトピックは、基になる IIS テクノロジと関連しています。

* <xref:host-and-deploy/iis/index>
* <xref:fundamentals/servers/aspnet-core-module>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Microsoft TechNet ライブラリ: Windows Server](/windows-server/windows-server-versions)
