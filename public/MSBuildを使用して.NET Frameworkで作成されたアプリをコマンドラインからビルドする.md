---
title: MSBuildを使用して.NET Frameworkで作成されたアプリをコマンドラインからビルド&デプロイする
tags:
  - MSBuild
  - VisualStudio
  - .NETFramework
  - .NET
  - PowerShell
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

## 実際のコマンド

### ClickOnesアプリケーションの場合

```powershell
.\msbuild "C:\ProjectPath\Project.sln" `
  /t:Publish `
  /t:Rebuild `
  /p:Configuration=Release `
  /p:RestorePackagesConfig=True `
  /p:PublishDir="C:\OutProject\" `
  /p:Platform="Any CPU" `
  -verbosity:quiet `
  -maxCpuCount:4
```
※msbuildのインストール場所については、記事末尾に記載しています。

### Webアプリケーションの場合(Folder出力)

```powershell
.\msbuild "C:\ProjectPath\Project.sln" `
  /t:Publish `
  /t:Rebuild `
  /p:Configuration=Release `
  /p:RestorePackagesConfig=True `
  /p:PublishProfile="C:\ProjectPath\Properties\PublishProfiles\FolderProfile.pubxml" `
  /p:DeployOnBuild=true `
  /p:PublishUrl="C:\OutProject\" `
  -verbosity:quiet `
  -maxCpuCount:4
```

`PublishProfiles`に設定しているファイルは以下の通りです。
`<PropertyGroup>`内で設定している各値は、msbuildのオプションの`/p:プロパティ名`で上書きすることが出来ます。

```xml:\Properties\PublishProfiles\FolderProfile.pubxml
<?xml version="1.0" encoding="utf-8"?>
<!--
https://go.microsoft.com/fwlink/?LinkID=208121.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <DeleteExistingFiles>True</DeleteExistingFiles>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <PublishProvider>FileSystem</PublishProvider>
    <PublishUrl>C:\DefaultOutDirectory\</PublishUrl>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <SiteUrlToLaunchAfterPublish />
  </PropertyGroup>
</Project>
```

## 各オプションの説明(共通)

### `/t:Publish`

MSBuildに対してプロジェクトをビルドし、その後にプロジェクトをデプロイすることを指示します。デプロイ時には、ビルドされた成果物が指定された場所にコピーされます。

### `/t:Rebuild`

プロジェクトのリビルドを行います。つまり、ビルド前に古い成果物をクリーンアップし、新しいビルドを実行します。

### `/p:Configuration`

ビルド時に使用する構成を指定します。一般的な値にはDebugやReleaseなどがあります。

### `-verbosity`

ビルド ログに表示する情報量を指定します。値には以下が指定可能です。
- quiet     ・・・  最小限の情報
- minimal   ・・・  簡易的な情報
- normal    ・・・  通常の情報(規定値)
- detailed  ・・・  詳細な情報
- diagnostic・・・  診断情報

### `-maxCpuCount`

ビルドに使用される最大のプロセッサ数を指定します。並列ビルドを有効にするためのオプションです。
並列ビルドを有効にすると、ほとんどの場合でビルドパフォーマンスが向上しますが、実行環境によってはパフォーマンスが低下する可能性もあります(高負荷時や低コアのCPUなど)

### `/p:RestorePackagesConfig`

NuGet パッケージがプロジェクトに復元されるように指定します。
プロジェクトの`package.config`に記載のあるパッケージが復元対象です。

## 各オプションの説明(ClickOnes)


### `/p:PublishDir`

デプロイ先のディレクトリを指定します。

### `/p:Platform`

デプロイ対象のプラットフォームを指定します。


## 各オプションの説明(Webアプリケーション(Folder出力))


### `/p:DeployOnBuild`

Webアプリケーションをビルドした際にデプロイを行うかどうかを指定します。trueの場合、デプロイが実行されます。

### `/p:PublishProfile`

このオプションは、Webアプリケーションのデプロイ設定が記述されたプロファイルファイルのパスを指定します。
プロファイルの場所については、一度でもVisualStudio上で以下のアプリケーションデプロイ画面(`プロジェクトを右クリック -> 発行`)でプロファイルを作成した場合は、
`[プロジェクトのディレクトリ]\Properties\PublishProfiles\FolderProfile.pubxml`に存在します。


![Visual Studio 2019のアプリケーションデプロイ画面](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/243130/ea3bae4c-b296-a2e4-051f-20a7ea9c2dfa.png)

### `/p:PublishUrl`

デプロイ先のディレクトリを指定します。
`PublishUrl`は`PublishProfile`内で指定していますが、今回はコマンドからデプロイ先を指定したい為、上書きしています。


## MSBuildのインストール場所

### Visual Studio 2017の場合

`C:\Program Files (x86)\Microsoft Visual Studio\2017\{Edition}\MSBuild\15.0\Bin\MSBuild.exe`

### Visual Studio 2019の場合

`C:\Program Files (x86)\Microsoft Visual Studio\2019\{Edition}\MSBuild\Current\Bin\MSBuild.exe`

### Visual Studio 2022の場合

`C:\Program Files\Microsoft Visual Studio\2022\{Edition}\MSBuild\Current\Bin\MSBuild.exe`






# 参考

https://learn.microsoft.com/ja-jp/visualstudio/msbuild/msbuild-command-line-reference