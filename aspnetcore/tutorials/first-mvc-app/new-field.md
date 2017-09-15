---
title: "新しいフィールドの追加"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 16efbacf-fe7b-4b41-84b0-06a1574b95c2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 52fe9ee7bf006553c7dcb17c00ff659d3797b204
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2017
---
# <a name="adding-a-new-field"></a>新しいフィールドの追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このセクションでは、[Entity Framework](http://docs.efproject.net/en/latest/platforms/aspnetcore/new-db.html) Code First Migrations を利用し、新しいフィールドをモデルに追加し、その変更をデータベースに移行します。

EF Code First を利用してデータベースを自動作成すると、Code First はテーブルをデータベースに追加し、データベースのスキーマが生成元のモデル クラスと同期しているか追跡します。 同期していない場合、EF は例外をスローします。 一貫性のないデータベース/コードの問題を簡単に見つけられます。

## <a name="adding-a-rating-property-to-the-movie-model"></a>ムービー モデルへの評価プロパティの追加

*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

アプリをビルドします (Ctrl+Shift+B)。

新しいフィールドを `Movie` クラスに追加したので、この新しいプロパティが含まれるように、拘束力のあるホワイト リストを更新する必要もあります。 *MoviesController.cs* で、アクション メソッドの `Create` と `Edit` の両方の `[Bind]` 属性を更新し、`Rating` プロパティが含まれるようにします。

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

ブラウザー ビューで新しい `Rating` プロパティを表示、作成、編集する目的でビュー テンプレートを更新する必要もあります。

*/Views/Movies/Index.cshtml* ファイルを編集し、`Rating` フィールドを追加します。

[!code-HTML[Main](start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

*/Views/Movies/Create.cshtml* を `Rating` フィールドで更新します。 前の "form group" をコピー/貼り付けし、intelliSense にフィールドを更新させることができます。 IntelliSense は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)と連動します。 注: RTM バージョンの Visual Studio 2017 では、Razor intelliSense の [Razor 言語サービス](https://marketplace.visualstudio.com/items?itemName=ms-madsk.RazorLanguageServices)をインストールする必要があります。 これは次のリリースで修正されます。

![開発者は、ビューの 2 番目のラベル要素で、asp-for の属性値に文字 R を入力しました。 Intellisense のコンテキスト メニューが表示され、[評価] を含む、利用可能なフィールドが表示されました。[評価] は一覧の中で自動的に強調表示されています。 開発者はこのフィールドをクリックするか、キーボードの Enter を押すと、値が [評価] に設定されます。](new-field/_static/cr.png)

DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。 ここですぐに実行すると、次の `SqlException` が表示されます。

`SqlException: Invalid column name 'Rating'.`

このエラーが表示されるのは、更新された Movie モデル クラスが既存のデータベースの Movie テーブルのスキーマと異なるためです  (データベース テーブルに [評価] 列はありません)。

このエラーを解決するための手法がいくつかあります。

1. Entity Framework に、新しいモデル クラス スキーマに基づいてデータベースを自動的にドロップさせ、再作成させます。 この手法は、開発周期の早い段階で、テスト データベースで開発しているときに非常に便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。 ただし、欠点もあり、データベースの既存データが失われます。本稼働データベースではこの手法は推奨されません。 初期化子を利用し、データベースにテスト データを自動的に初期投入します。多くの場合、アプリケーション開発の手法として有益な方法です。

2. モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。 この手法の長所は、データが維持されることです。 この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。

3. Code First Migrations を使用して、データベース スキーマを更新します。

このチュートリアルでは、Code First Migrations を利用します。

新しい列に値を提供するように、`SeedData` クラスを更新します。 下に変更のサンプルがありますが、`new Movie` ごとにこの変更を行ってください。

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

ソリューションをビルドします。

**[ツール]** メニューで、**[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。

  ![PMC メニュー](adding-model/_static/pmc.png)

PMC で、次のコマンドを入力します。

```PMC
Add-Migration Rating
Update-Database
```

`Add-Migration` コマンドは移行フレームワークに現在の `Movie` モデルを現在の `Movie` DB スキーマで調べ、DB を新しいモデルに移行するために必要なコードを作成します。 "Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。 移行ファイルには意味のある名前を使用すると便利です。

DB 内のすべてのレコードを削除すると、初期化子は DB にデータを初期投入し、`Rating` フィールドを追加します。 これはブラウザーの削除リンクで行うか、SSOX から行うことができます。

アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。 また、`Rating` フィールドはビュー テンプレートの `Edit`、`Details`、`Delete` にも追加してください。

>[!div class="step-by-step"]
[前へ](search.md)
[次へ](validation.md)  