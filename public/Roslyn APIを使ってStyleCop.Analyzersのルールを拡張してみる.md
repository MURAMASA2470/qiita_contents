---
title: Roslyn APIを使ってStyleCop.Analyzersのルールを拡張してみる
tags:
  - VisualStudio
  - Roslyn
  - StyleCop.Analyzers
  - コード分析
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## 事前準備

### 環境

`StyleCopAnalyzers`の開発を行うには、以下のものが必要です。

- C# 8.0 以上
- .NET 5 以上
- VisualStudio 2022 以上

### リポジトリ

以下の`StyleCopAnalyzers`の公式リポジトリをローカルにクローンし、`VisualStudio 2022`でプロジェクトを開きます。

https://github.com/DotNetAnalyzers/StyleCopAnalyzers

## StyleCopAnalyzersのルールについて

`StyleCopAnalyzers`には、以下のようなルール既にあります。

**Special Rules(特別ルール)  (SA0000～)**

回避策、構成エラーなどの特別な機能を提供するルール。

**Spacing Rules(間隔の規則) (SA1000～)**

コード内のキーワードと記号の間隔要件を強制するルール。

**Readability Rules(可読性ルール)  (SA1100～)**

コードが適切にフォーマットされ、読みやすいことを保証するルール。

**Ordering Rules(発注ルール)  (SA1200～)**

コードの内容に標準の順序付けスキームを適用するルール。

**Naming Rules(命名規則)  (SA1300～)**

メンバー、型、および変数の名前付け要件を強制する規則。

**Maintainability Rules(保守容易性ルール)  (SA1400～)**

コードの保守性を向上させるルール。

**Layout Rules(レイアウトルール)  (SA1500～)**

コードのレイアウトと行間を強制するルール。

**Documentation Rules(ドキュメンテーションルール)  (SA1600～)**

コードドキュメントの内容とフォーマットを検証するルール。

**Alternative Rules(代替ルール)  (SX0000～)**

既定の StyleCop の動作に非標準の拡張機能を提供するルール。

## 開発

今回は、`Layout Rules`に「三項演算子をネストしてはならない」と言うルールを追加していこうと思います。
適当なファイル(`SA1520UseBracesConsistently.cs`)辺りを複製し、名前を`SX1580TernaryOperatorsNotBeNested.cs`に変更します。

クラスを定義している部分を、以下のソース

### ベースのコード

新規でルールを作る際は、以下のような状態がベースとなります。
ここに肉付けしていきます。

```csharp:StyleCop.Analyzers\LayoutRules\SX1580TernaryOperatorsNotBeNested.cs

[DiagnosticAnalyzer(LanguageNames.CSharp)]
internal class SX1580TernaryOperatorsNotBeNested : DiagnosticAnalyzer
{
    /// <summary>
    /// The ID for diagnostics produced by the <see cref="SX1580TernaryOperatorsNotBeNested"/> analyzer.
    /// </summary>
    public const string DiagnosticId = "SX1580";

    private static readonly LocalizableString Title = new LocalizableResourceString(nameof(LayoutResources.SX1580Title), LayoutResources.ResourceManager, typeof(LayoutResources));
    private static readonly LocalizableString MessageFormat = new LocalizableResourceString(nameof(LayoutResources.SX1580MessageFormat), LayoutResources.ResourceManager, typeof(LayoutResources));
    private static readonly LocalizableString Description = new LocalizableResourceString(nameof(LayoutResources.SX1580Description), LayoutResources.ResourceManager, typeof(LayoutResources));

    private static readonly DiagnosticDescriptor Descriptor =
        new DiagnosticDescriptor(DiagnosticId, Title, MessageFormat, AnalyzerCategory.LayoutRules, DiagnosticSeverity.Warning, AnalyzerConstants.EnabledByDefault, Description);

    /// <inheritdoc/>
    public override ImmutableArray<DiagnosticDescriptor> SupportedDiagnostics { get; } = ImmutableArray.Create(Descriptor);

    /// <inheritdoc/>
    public override void Initialize(AnalysisContext context)
    {
        context.ConfigureGeneratedCodeAnalysis(GeneratedCodeAnalysisFlags.None);
        context.EnableConcurrentExecution();
    }
}
```

それぞれの変数の説明です。
- `DiagnosticId`
  - 各ルールを識別するためのIDです。重複不可。
- `Title`
  - ルールのタイトルです。
  - 後述するリソースファイルからメッセージを取得します。
- `MessageFormat`
  - ルールの本文です。実際にアナライザの指摘部分に表示される部分になります。
  - 後述するリソースファイルからメッセージを取得します。
- `Description`
  - ルールの説明です。
  - 後述するリソースファイルからメッセージを取得します。
- `Descriptor`
  - 上記のIDやメッセージ、ルールカテゴリなどをまとめたものです。
  - 基本変更不要です。
- `SupportedDiagnostics`
  - `Descriptor`を`ImmutableArray`型に変換して、外から参照出来るようにします。
  - 基本変更不要です。

### 処理を追加する

ベースのコードに以下の部分を追加します。
以下では、`CheckChildExpression`メソッドを追加し、三項演算子がネストしている事を検知する処理を記載しています。

引数の`ExpressionSyntax`型の引数`childExpression`に、構文やトークンの情報※1が入っている為、それが三項演算子かどうか判定しています。
構文が三項演算子と判定された後に、再度三項演算子と判定されれば、ネストしていると断定することが出来ます。

最後に、`Initialize`メソッドで先ほど追加した`CheckChildExpression`をコンテキストに登録します。
こうすることで、アナライザー実行時に分析を行ってくれます。

```diff_c_sharp:StyleCop.Analyzers\LayoutRules\SX1580TernaryOperatorsNotBeNested.cs

[DiagnosticAnalyzer(LanguageNames.CSharp)]
internal class SX1580TernaryOperatorsNotBeNested : DiagnosticAnalyzer
{
    /// <summary>
    /// The ID for diagnostics produced by the <see cref="SX1580TernaryOperatorsNotBeNested"/> analyzer.
    /// </summary>
    public const string DiagnosticId = "SX1580";

    private static readonly LocalizableString Title = new LocalizableResourceString(nameof(LayoutResources.SX1580Title), LayoutResources.ResourceManager, typeof(LayoutResources));
    private static readonly LocalizableString MessageFormat = new LocalizableResourceString(nameof(LayoutResources.SX1580MessageFormat), LayoutResources.ResourceManager, typeof(LayoutResources));
    private static readonly LocalizableString Description = new LocalizableResourceString(nameof(LayoutResources.SX1580Description), LayoutResources.ResourceManager, typeof(LayoutResources));

    private static readonly DiagnosticDescriptor Descriptor =
        new DiagnosticDescriptor(DiagnosticId, Title, MessageFormat, AnalyzerCategory.LayoutRules, DiagnosticSeverity.Warning, AnalyzerConstants.EnabledByDefault, Description);

    /// <inheritdoc/>
    public override ImmutableArray<DiagnosticDescriptor> SupportedDiagnostics { get; } = ImmutableArray.Create(Descriptor);

    /// <inheritdoc/>
    public override void Initialize(AnalysisContext context)
    {
        context.ConfigureGeneratedCodeAnalysis(GeneratedCodeAnalysisFlags.None);
        context.EnableConcurrentExecution();

+       context.RegisterSyntaxNodeAction(ctx => CheckChildExpression(ctx, ((ConditionalExpressionSyntax)ctx.Node)), SyntaxKind.ConditionalExpression);
    }

+   private static void CheckChildExpression(SyntaxNodeAnalysisContext context, ExpressionSyntax childExpression)
+   {
+       if (childExpression is ConditionalExpressionSyntax child)
+       {
+           if (child.Parent is ConditionalExpressionSyntax)
+           {
+               context.ReportDiagnostic(Diagnostic.Create(Descriptor, childExpression.GetLocation()));
+           }
+       }

        return;
    }
}
```

※1 構文やトークンの情報について
VisualStudioの`Roslyn Syntax Visualizer`と言うツールを使用すると、以下のようにコードが木構造で分析された結果を表示することが出来ます。

![image-2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/e3cac21a-66e3-f949-c57c-184d16c4acb2.png)

### メッセージの追加

リソースファイルにメッセージを追加します。
以下はコードで表現していますが、基本的にGUIで編集することが多いです。
`MessageFormat`では「"if分を{0}回以上ネストする事は出来ません"」のように記述することで、後で値を受けこむことが可能です。

```xml:StyleCop.Analyzers\LayoutRules\LayoutResources.ja-JP.resx
.....
<data name="SX1580Description" xml:space="preserve">
  <value>三項演算子をネストすることは出来ません</value>
</data>
<data name="SX1580MessageFormat" xml:space="preserve">
  <value>三項演算子をネストすることは出来ません</value>
</data>
<data name="SX1580Title" xml:space="preserve">
  <value>三項演算子をネストすることは出来ません</value>
</data>
.....
```

## アナライザーの出力

### ターゲットフレームワークの設定

アナライザーの作成が終わったら、アナライザーを動かす為のターゲットフレームワークを指定します。
`プロジェクト -> 右クリック -> プロパティ -> アプリケーション -> 全般 -> ターゲットフレームワーク`

今回は.NET Framework 4.7.2向けに出力したいので、`net472`の様に指定します。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/064f033f-9519-026f-b900-7026a352761c.png)

### dllファイルの生成

設定が終わったら、プロジェクトを'Relese'でビルドします。
そうすると`\StyleCop.Analyzers\bin\Release\net472\`の配下に`StyleCop.Analyzers.dll`というファイルが生成されます。

## アナライザーの適応

出力が終わったら、適応したいプロジェクトをVisualStudioで開き、
`プロジェクト -> 参照 -> アナライザー -> 右クリック -> アナライザーの追加`で、先ほど出力した`StyleCop.Analyzers.dll`を参照に加えます。

![image-1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/500210ce-38fd-f750-c7d6-f23751b7a9f8.png)

ここまで来たら、もう少しです。

`プロジェクト -> 右クリック -> プロパティ -> コード分析 -> アクティブな規則 -> 構成`でStyleCopの使いたいルールにチェックを入れます。(SA～やSX～がStyleCopのルールになります。)
ルールを保存すると、`*.ruleset`をファイルが生成される為、エディタ等で編集することも可能です。
※VisualStudio 2022からは上記の方法は廃止され、EditorConfigによるルール設定に切り替わっています。