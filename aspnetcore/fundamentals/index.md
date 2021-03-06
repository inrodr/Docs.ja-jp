---
title: ASP.NET Core の基礎
author: rick-anderson
description: ASP.NET Core アプリの構築に関する基本概念について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/index
ms.openlocfilehash: ab140051648c1640b3c4f382bfd8201c5c0c2039
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50207473"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core の基礎

ASP.NET Core アプリは、その `Program.Main` メソッドで Web サーバーを作成するコンソール アプリです。 `Main` メソッドは、アプリの*マネージド エントリ ポイント*です。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs)]

.NET Core ホスト:

* [.NET Core ランタイム](https://github.com/dotnet/coreclr)を読み込みます。
* エントリ ポイント (`Main`) を含むマネージド バイナリへのパスとして最初のコマンドライン引数を使用し、コードの実行を開始します。

`Main` メソッドは、Web ホストを作成する[ビルダー パターン](https://wikipedia.org/wiki/Builder_pattern)に従う、[WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*) を呼び出します。 ビルダーには、Web サーバー (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> など) とスタートアップ クラス (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>) を定義するメソッドがあります。 前の例では、[Kestrel](xref:fundamentals/servers/kestrel) Web サーバーが自動的に割り当てられます。 ASP.NET Core の Web ホストは、IIS (使用可能な場合) で実行を試みます。 [HTTP.sys](xref:fundamentals/servers/httpsys) などの他の Web サーバーは、適切な拡張メソッドを呼び出して使用することができます。 `UseStartup` については、次のセクションで詳しく説明します。

<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> (`WebHost.CreateDefaultBuilder` 呼び出しの戻り値の型) では、省略可能な多くのメソッドが提供されます。 これらのメソッドの一部には、HTTP.sys でアプリをホストするための `UseHttpSys` と、ルート コンテンツ ディレクトリを指定するための <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> が含まれています。 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> および <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> メソッドは、アプリをホストし、HTTP 要求のリッスンを開始する <xref:Microsoft.AspNetCore.Hosting.IWebHost> オブジェクトをビルドします。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs)]

.NET Core ホスト:

* [.NET Core ランタイム](https://github.com/dotnet/coreclr)を読み込みます。
* エントリ ポイント (`Main`) を含むマネージド バイナリへのパスとして最初のコマンドライン引数を使用し、コードの実行を開始します。

`Main` メソッドは、Web アプリ ホストを作成する[ビルダー パターン](https://wikipedia.org/wiki/Builder_pattern)に従う、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を使用します。 ビルダーには、Web サーバー (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> など) とスタートアップ クラス (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>) を定義するメソッドがあります。 前の例では、[Kestrel](xref:fundamentals/servers/kestrel) Web サーバーが使用されています。 [WebListener](xref:fundamentals/servers/weblistener) などの他の Web サーバーは、適切な拡張メソッドを呼び出して使用することができます。 `UseStartup` については、次のセクションで詳しく説明します。

`WebHostBuilder` では、IIS および IIS Express でホストするための <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> と、ルート コンテンツ ディレクトリを指定するための <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> を含む、省略可能な多くのメソッドが提供されます。 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> および <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> メソッドは、アプリをホストし、HTTP 要求のリッスンを開始する <xref:Microsoft.AspNetCore.Hosting.IWebHost> オブジェクトをビルドします。

::: moniker-end

## <a name="startup"></a>スタートアップ

`WebHostBuilder` の `UseStartup` メソッドは、アプリの `Startup` クラスを指定します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs?highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs?highlight=7)]

::: moniker-end

`Startup` クラスでは、要求処理パイプラインを定義し、アプリで必要なサービスが構成されます。 `Startup` クラスはパブリックであり、次のメソッドを含む必要があります。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Startup.cs)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Startup.cs)]

::: moniker-end

<xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> は、アプリで使用される[サービス](#dependency-injection-services) (ASP.NET Core MVC、Entity Framework Core、Identity など) を定義します。 <xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> は、要求パイプラインで呼び出される[ミドルウェア](xref:fundamentals/middleware/index)を定義します。

詳細については、「<xref:fundamentals/startup>」を参照してください。

## <a name="content-root"></a>コンテンツ ルート

コンテンツ ルートは、[Razor ページ](xref:razor-pages/index)、MVC ビュー、静的資産など、アプリで使用されるコンテンツへの基本パスです。 既定では、コンテンツ ルートは、アプリをホストする実行可能ファイルのアプリ基本パスと同じ場所です。

## <a name="web-root-webroot"></a>Web ルート (webroot)

アプリの webroot は、CSS、JavaScript、イメージ ファイルなどのパブリックな静的なリソースを含むプロジェクトのディレクトリです。 既定では、*wwwroot* が webroot です。

Razor (*.cshtml*) のファイルの場合、チルダとスラッシュ `~/` が webroot を指します。 `~/` で始まるパスは仮想パスと呼ばれます。

## <a name="dependency-injection-services"></a>依存性の注入 (サービス)

*サービス*は、アプリでの一般的な使用を想定されているコンポーネントです。 サービスは[依存性の注入 (DI)](xref:fundamentals/dependency-injection) を介して使用できます。 ASP.NET Core には、既定で[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)をサポートする、ネイティブの制御の反転 (Inversion of Control、IoC) コンテナーが含まれます。 必要に応じて、既定のコンテナーを置き換えることができます。 その[疎結合の利点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)に加え、DI はアプリ全体でサービス ([ログ](xref:fundamentals/logging/index)など) を使用できるようにします。

詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。

## <a name="middleware"></a>ミドルウェア

ASP.NET Core では、[ミドルウェア](xref:fundamentals/middleware/index)を使用して要求パイプラインを構成します。 ASP.NET Core ミドルウェアは `HttpContext` で非同期操作を実行してから、シーケンス内の次のミドルウェアを呼び出すか、直接要求を終了します。

通常、"XYZ" と呼ばれるミドルウェア コンポーネントは、`Configure` メソッドで `UseXYZ` 拡張メソッドを呼び出してパイプラインに追加されます。

ASP.NET Core には組み込みのミドルウェアの豊富なセットが含まれており、独自のカスタム ミドルウェアを作成できます。 ASP.NET Core アプリでは、[Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) がサポートされています。これにより、Web アプリケーションを Web サーバーから切り離すことができます。

詳細については、次のトピックを参照してください。 <xref:fundamentals/middleware/index> および <xref:fundamentals/owin>.

::: moniker range=">= aspnetcore-2.1"

## <a name="initiate-http-requests"></a>HTTP 要求の開始

<xref:System.Net.Http.IHttpClientFactory> を使用して <xref:System.Net.Http.HttpClient> インスタンスアクセスにアクセスし、HTTP 要求を行うことができます。

詳細については、「<xref:fundamentals/http-requests>」を参照してください。

::: moniker-end

## <a name="environments"></a>環境

*開発*や*実稼働*などの環境は ASP.NET Core におけるファースト クラスの概念であり、環境変数、設定ファイル、およびコマンド ライン引数を使用して設定できます。

詳細については、「<xref:fundamentals/environments>」を参照してください。

## <a name="hosting"></a>ホスト

ASP.NET Core アプリは、アプリの起動と有効期間の管理を担当する*ホスト*を構成して起動します。

詳細については、「<xref:fundamentals/host/index>」を参照してください。

## <a name="servers"></a>サーバー

ASP.NET Core のホスティング モデルは、直接要求をリッスンしません。 ホスティング モデルは HTTP サーバー実装に依存して要求をアプリに転送します。 転送された要求は、インターフェイスを介してアクセス可能な一連の機能オブジェクトとしてラップされます。 ASP.NET Core には、[Kestrel](xref:fundamentals/servers/kestrel) と呼ばれる、マネージド クロスプラットフォーム Web サーバーが含まれています。 Kestrel は通常、リバース プロキシ構成で、[IIS](https://www.iis.net/) や [Nginx](http://nginx.org) などの実稼働 Web サーバーの背後で実行されます。 ASP.NET Core 2.0 以降では、Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。

詳細については、「<xref:fundamentals/servers/index>」を参照してください。

## <a name="configuration"></a>構成

ASP.NET Core は、名前と値のペアに基づく構成モデルを使用します。 この構成モデルは <xref:System.Configuration> または *web.config* には基づきません。構成は、構成プロバイダーの順序付けされたセットから設定を取得します。 組み込みの構成プロバイダーは、さまざまなファイル形式 (XML、JSON、INI)、環境変数、およびコマンド ライン引数をサポートします。 独自のカスタム構成プロバイダーを作成することもできます。

詳細については、「<xref:fundamentals/configuration/index>」を参照してください。

## <a name="logging"></a>ログの記録

ASP.NET Core は、さまざまなログ プロバイダーと連携するログ API をサポートします。 組み込みプロバイダーは、1 つまたは複数の送信先へのログの送信をサポートします。 サード パーティ製のログ記録フレームワークを使用できます。

詳細については、「<xref:fundamentals/logging/index>」を参照してください。

## <a name="error-handling"></a>エラー処理

ASP.NET Core には、開発者の例外ページ、カスタム エラー ページ、静的ステータス コード ページ、起動時の例外処理を含む、アプリのエラー処理のシナリオが組み込まれています。

詳細については、「<xref:fundamentals/error-handling>」を参照してください。

## <a name="routing"></a>ルーティング

ASP.NET Core は、ルート ハンドラーにアプリ要求をルーティングするシナリオを提供します。

詳細については、「<xref:fundamentals/routing>」を参照してください。

## <a name="background-tasks"></a>バックグラウンド タスク

バックグラウンド タスクは*ホストされるサービス*として実装されます。 ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。

詳細については、「<xref:fundamentals/host/hosted-services>」を参照してください。

## <a name="access-httpcontext"></a>HttpContext へのアクセス

`HttpContext` は、Razor ページおよび MVC で要求を処理するときに自動的に使用可能になります。 `HttpContext` をすぐに使用できない状況では、<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> インターフェイスとその既定の実装 <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> を介して `HttpContext` にアクセスできます。

詳細については、「<xref:fundamentals/httpcontext>」を参照してください。
